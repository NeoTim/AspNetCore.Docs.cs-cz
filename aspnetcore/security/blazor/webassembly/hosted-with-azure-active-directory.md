---
title: Zabezpečení ASP.NET Blazor hostované webové sestavy pomocí Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8fec9f585f42469665cf29069674a199e1626629
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977129"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="e103d-102">Zabezpečení ASP.NET Blazor hostované webové sestavy pomocí Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="e103d-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="e103d-103">[Javier Calvarro Nelson](https://github.com/javiercn) a Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e103d-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



<span data-ttu-id="e103d-104">Tento článek popisuje, jak vytvořit [ Blazor hostovku webové sestavy,](xref:blazor/hosting-models#blazor-webassembly) která používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="e103d-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="e103d-105">Registrace aplikací v AAD B2C a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="e103d-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="e103d-106">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="e103d-106">Create a tenant</span></span>

<span data-ttu-id="e103d-107">Postupujte podle pokynů v [úvodním startu: Nastavení klienta](/azure/active-directory/develop/quickstart-create-new-tenant) pro vytvoření klienta v AAD.</span><span class="sxs-lookup"><span data-stu-id="e103d-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="e103d-108">Registrace aplikace serverového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e103d-108">Register a server API app</span></span>

<span data-ttu-id="e103d-109">Postupujte podle pokynů v [úvodním panelu: Zaregistrujte aplikaci s platformou microsoftu pro identity](/azure/active-directory/develop/quickstart-register-app) a další témata Azure AAD k registraci aplikace AAD pro *aplikaci Rozhraní API serveru* v oblasti**registrace aplikací** Azure **Active Directory** > na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e103d-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e103d-110">Vyberte **možnost Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="e103d-110">Select **New registration**.</span></span>
1. <span data-ttu-id="e103d-111">Zadejte **název** aplikace (například \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="e103d-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="e103d-112">Zvolte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="e103d-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="e103d-113">Pro toto prostředí můžete vybrat **účty v tomto organizačním adresáři (pouze** jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="e103d-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="e103d-114">*Aplikace rozhraní API serveru* nevyžaduje identifikátor URI **přesměrování** v tomto scénáři, takže ponechte rozevírací soubor nastavený na **web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="e103d-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="e103d-115">Zamítejte**zaškrtávací** políčko **Oprávnění** > Udělit oprávnění k otevření a offline_access oprávnění.</span><span class="sxs-lookup"><span data-stu-id="e103d-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="e103d-116">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="e103d-116">Select **Register**.</span></span>

<span data-ttu-id="e103d-117">V **oprávnění chodrozhraní API**odeberte oprávnění Microsoft **Graph** > **User.Read,** protože aplikace nevyžaduje přístup k profilu přihlášení nebo uer.</span><span class="sxs-lookup"><span data-stu-id="e103d-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="e103d-118">V **vystavit rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="e103d-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="e103d-119">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="e103d-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="e103d-120">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="e103d-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="e103d-121">Zadejte **název oboru** `API.Access`(například).</span><span class="sxs-lookup"><span data-stu-id="e103d-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e103d-122">Zadejte **zobrazovaný název souhlasu správce** `Access API`(například).</span><span class="sxs-lookup"><span data-stu-id="e103d-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="e103d-123">Uveďte **popis souhlasu** správce `Allows the app to access server app API endpoints.`(například).</span><span class="sxs-lookup"><span data-stu-id="e103d-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="e103d-124">Zkontrolujte, zda je **stav** nastaven na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="e103d-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="e103d-125">Vyberte **přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="e103d-125">Select **Add scope**.</span></span>

<span data-ttu-id="e103d-126">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="e103d-126">Record the following information:</span></span>

* <span data-ttu-id="e103d-127">*Aplikace rozhraní API serveru* ID aplikace (ID klienta) `11111111-1111-1111-1111-111111111111`(například)</span><span class="sxs-lookup"><span data-stu-id="e103d-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="e103d-128">Identifikátor URI ID aplikace `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`(například , nebo vlastní hodnota, kterou jste zadali)</span><span class="sxs-lookup"><span data-stu-id="e103d-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="e103d-129">ID adresáře (ID klienta) `222222222-2222-2222-2222-222222222222`(například)</span><span class="sxs-lookup"><span data-stu-id="e103d-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="e103d-130">Doména klienta AAD `contoso.onmicrosoft.com`(například)</span><span class="sxs-lookup"><span data-stu-id="e103d-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="e103d-131">Výchozí obor (například `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="e103d-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="e103d-132">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="e103d-132">Register a client app</span></span>

<span data-ttu-id="e103d-133">Postupujte podle pokynů v [úvodním panelu: Zaregistrujte aplikaci s platformou microsoftu pro identity](/azure/active-directory/develop/quickstart-register-app) a další témata Azure AAD k registraci aplikace AAD pro *klientskou aplikaci* v oblasti**registrace aplikací** Azure **Active Directory** > na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e103d-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e103d-134">Vyberte **možnost Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="e103d-134">Select **New registration**.</span></span>
1. <span data-ttu-id="e103d-135">Zadejte **název** aplikace (například \*\* Blazor AAD klienta).\*\*</span><span class="sxs-lookup"><span data-stu-id="e103d-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="e103d-136">Zvolte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="e103d-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="e103d-137">Pro toto prostředí můžete vybrat **účty v tomto organizačním adresáři (pouze** jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="e103d-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="e103d-138">Ponechejte rozevírací soubor **URI přesměrování** nastavený `https://localhost:5001/authentication/login-callback`na **web**a poskytněte identifikátor URI přesměrování aplikace .</span><span class="sxs-lookup"><span data-stu-id="e103d-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="e103d-139">Zamítejte**zaškrtávací** políčko **Oprávnění** > Udělit oprávnění k otevření a offline_access oprávnění.</span><span class="sxs-lookup"><span data-stu-id="e103d-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="e103d-140">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="e103d-140">Select **Register**.</span></span>

<span data-ttu-id="e103d-141">V**konfiguracích platformy** >  **ověřování** > **web**:</span><span class="sxs-lookup"><span data-stu-id="e103d-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e103d-142">Potvrďte, že `https://localhost:5001/authentication/login-callback` je přítomen **identifikátor URI přesměrování.**</span><span class="sxs-lookup"><span data-stu-id="e103d-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e103d-143">V **případě implicitního udělení**zaškrtněte políčka u **tokenů přístupu** a **tokenů ID**.</span><span class="sxs-lookup"><span data-stu-id="e103d-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e103d-144">Zbývající výchozí hodnoty pro aplikaci jsou přijatelné pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="e103d-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e103d-145">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="e103d-145">Select the **Save** button.</span></span>

<span data-ttu-id="e103d-146">V **rozhraní API oprávnění**:</span><span class="sxs-lookup"><span data-stu-id="e103d-146">In **API permissions**:</span></span>

1. <span data-ttu-id="e103d-147">Zkontrolujte, zda má aplikace oprávnění **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="e103d-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="e103d-148">Vyberte **přidat oprávnění** následované **moje api**.</span><span class="sxs-lookup"><span data-stu-id="e103d-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="e103d-149">Ve sloupci **Název** vyberte *aplikaci Server API* (například \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="e103d-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="e103d-150">Otevřete seznam **rozhraní API.**</span><span class="sxs-lookup"><span data-stu-id="e103d-150">Open the **API** list.</span></span>
1. <span data-ttu-id="e103d-151">Povolte přístup k rozhraní `API.Access`API (například).</span><span class="sxs-lookup"><span data-stu-id="e103d-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e103d-152">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="e103d-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="e103d-153">Vyberte **tlačítko Udělit obsah správce pro {NÁZEV TENANTA}.**</span><span class="sxs-lookup"><span data-stu-id="e103d-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="e103d-154">Výběrem **Ano** potvrďte.</span><span class="sxs-lookup"><span data-stu-id="e103d-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="e103d-155">Zaznamenejte ID aplikace *klientské aplikace* `33333333-3333-3333-3333-333333333333`(ID klienta) (například).</span><span class="sxs-lookup"><span data-stu-id="e103d-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="e103d-156">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="e103d-156">Create the app</span></span>

<span data-ttu-id="e103d-157">Nahraďte zástupné symboly v následujícím příkazu informacemi zaznamenanými dříve a proveďte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="e103d-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="e103d-158">Chcete-li určit výstupní umístění, které vytvoří složku projektu, pokud neexistuje, zahrňte do `-o BlazorSample`příkazu možnost výstupu s cestou (například ).</span><span class="sxs-lookup"><span data-stu-id="e103d-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e103d-159">Název složky se také stane součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="e103d-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e103d-160">Předejte `app-id-uri` možnosti IDENTIFIKÁTOR UD aplikace, ale všimněte si, že změna konfigurace může být vyžadována v klientské aplikaci, která je popsána v části [Obory tokenů aplikace Access.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="e103d-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="e103d-161">Konfigurace serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="e103d-161">Server app configuration</span></span>

<span data-ttu-id="e103d-162">*Tato část se týkajících se aplikace **Server** řešení.*</span><span class="sxs-lookup"><span data-stu-id="e103d-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e103d-163">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="e103d-163">Authentication package</span></span>

<span data-ttu-id="e103d-164">Podpora ověřování a autorizace volání ASP.NET základní webová api je `Microsoft.AspNetCore.Authentication.AzureAD.UI`poskytována :</span><span class="sxs-lookup"><span data-stu-id="e103d-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="e103d-165">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="e103d-165">Authentication service support</span></span>

<span data-ttu-id="e103d-166">Metoda `AddAuthentication` nastaví ověřovací služby v rámci aplikace a nakonfiguruje obslužnou rutinu Nosiče JWT jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="e103d-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="e103d-167">Metoda `AddAzureADBearer` nastaví konkrétní parametry v obslužné rutině Nosiče JWT, které jsou nutné k ověření tokenů vysílaných službou Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="e103d-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="e103d-168">`UseAuthentication`a `UseAuthorization` zajistit, aby:</span><span class="sxs-lookup"><span data-stu-id="e103d-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="e103d-169">Aplikace se pokusí analyzovat a ověřit tokeny na příchozí požadavky.</span><span class="sxs-lookup"><span data-stu-id="e103d-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="e103d-170">Jakýkoli požadavek na přístup k chráněnému prostředku bez správných pověření se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e103d-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="e103d-171">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="e103d-171">User.Identity.Name</span></span>

<span data-ttu-id="e103d-172">Ve výchozím nastavení se rozhraní `User.Identity.Name` API aplikace `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` Server naplní hodnotou z typu deklarace `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`(například).</span><span class="sxs-lookup"><span data-stu-id="e103d-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="e103d-173">Chcete-li nakonfigurovat aplikaci `name` tak, aby přijímali hodnotu z typu <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`deklarace, nakonfigurujte [tokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) in :</span><span class="sxs-lookup"><span data-stu-id="e103d-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="e103d-174">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="e103d-174">App settings</span></span>

<span data-ttu-id="e103d-175">Soubor *appsettings.json* obsahuje možnosti konfigurace obslužné rutiny nosiče JWT, která slouží k ověření přístupových tokenů.</span><span class="sxs-lookup"><span data-stu-id="e103d-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="e103d-176">Řadič WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="e103d-176">WeatherForecast controller</span></span>

<span data-ttu-id="e103d-177">Kontroler WeatherForecast *(Controllers/WeatherForecastController.cs*) zpřístupňuje `[Authorize]` chráněné rozhraní API s atributem použitým na řadič.</span><span class="sxs-lookup"><span data-stu-id="e103d-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="e103d-178">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="e103d-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="e103d-179">Atribut `[Authorize]` v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="e103d-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="e103d-180">Atribut `[Authorize]` použitý v Blazor aplikaci WebAssembly slouží pouze jako nápověda k aplikaci, že uživatel by měl být autorizován pro aplikaci pracovat správně.</span><span class="sxs-lookup"><span data-stu-id="e103d-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="e103d-181">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="e103d-181">Client app configuration</span></span>

<span data-ttu-id="e103d-182">*Tato část se týkajících se **klientské** aplikace řešení.*</span><span class="sxs-lookup"><span data-stu-id="e103d-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e103d-183">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="e103d-183">Authentication package</span></span>

<span data-ttu-id="e103d-184">Když je aplikace vytvořena pro použití`SingleOrg`pracovních nebo školních účtů ( ), aplikace`Microsoft.Authentication.WebAssembly.Msal`automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="e103d-184">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e103d-185">Balíček obsahuje sadu primitiv, které pomáhají aplikaci ověřovat uživatele a získat tokeny pro volání chráněných api.</span><span class="sxs-lookup"><span data-stu-id="e103d-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e103d-186">Pokud přidáváte ověřování do aplikace, přidejte balíček ručně do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="e103d-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="e103d-187">Nahraďte `{VERSION}` v předchozím odkazu na `Microsoft.AspNetCore.Blazor.Templates` balíček verzí <xref:blazor/get-started> balíčku uvedenou v článku.</span><span class="sxs-lookup"><span data-stu-id="e103d-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="e103d-188">Balíček `Microsoft.Authentication.WebAssembly.Msal` transitively `Microsoft.AspNetCore.Components.WebAssembly.Authentication` přidá balíček do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e103d-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="e103d-189">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="e103d-189">Authentication service support</span></span>

<span data-ttu-id="e103d-190">Podpora pro ověřování uživatelů je registrována v `AddMsalAuthentication` kontejneru služby `Microsoft.Authentication.WebAssembly.Msal` s metodou rozšíření poskytované balíček.</span><span class="sxs-lookup"><span data-stu-id="e103d-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e103d-191">Tato metoda nastaví všechny služby potřebné pro interakci aplikace s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="e103d-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e103d-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e103d-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="e103d-193">Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů potřebných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="e103d-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e103d-194">Hodnoty potřebné pro konfiguraci aplikace lze získat z konfigurace Azure Portal AAD při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e103d-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="e103d-195">Obory přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="e103d-195">Access token scopes</span></span>

<span data-ttu-id="e103d-196">Výchozí obory přístupových tokenů představují seznam oborů přístupových tokenů, které jsou:</span><span class="sxs-lookup"><span data-stu-id="e103d-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="e103d-197">Ve výchozím nastavení zahrnuto do požadavku na přihlášení.</span><span class="sxs-lookup"><span data-stu-id="e103d-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="e103d-198">Slouží k zřízení přístupového tokenu ihned po ověření.</span><span class="sxs-lookup"><span data-stu-id="e103d-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="e103d-199">Všechny obory musí patřit do stejné aplikace podle pravidel služby Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e103d-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="e103d-200">Další obory lze přidat pro další aplikace rozhraní API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="e103d-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="e103d-201">Pokud portál Azure poskytuje identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku,** když obdrží *neoprávněnou odpověď 401* z rozhraní API, zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="e103d-201">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="e103d-202">Portál Azure může například poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="e103d-202">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="e103d-203">Zadej oblast URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="e103d-203">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="e103d-204">Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="e103d-204">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="e103d-205">Importuje soubor</span><span class="sxs-lookup"><span data-stu-id="e103d-205">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="e103d-206">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="e103d-206">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="e103d-207">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="e103d-207">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="e103d-208">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="e103d-208">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="e103d-209">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e103d-209">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="e103d-210">Ověřovací komponenta</span><span class="sxs-lookup"><span data-stu-id="e103d-210">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="e103d-211">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="e103d-211">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="e103d-212">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e103d-212">Run the app</span></span>

<span data-ttu-id="e103d-213">Spusťte aplikaci z projektu Server.</span><span class="sxs-lookup"><span data-stu-id="e103d-213">Run the app from the Server project.</span></span> <span data-ttu-id="e103d-214">Při použití sady Visual Studio vyberte projekt serveru v **Průzkumníku řešení** a vyberte tlačítko **Spustit** na panelu nástrojů nebo spusťte aplikaci z nabídky **Ladění.**</span><span class="sxs-lookup"><span data-stu-id="e103d-214">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e103d-215">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e103d-215">Additional resources</span></span>

* [<span data-ttu-id="e103d-216">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="e103d-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="e103d-217">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="e103d-217">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
