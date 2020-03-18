---
title: Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 12e09cf7e27f85473d84f42564d13e1c0ed5dff1
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434444"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="974f4-102">Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="974f4-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="974f4-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="974f4-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="974f4-104">Tento článek popisuje, jak vytvořit samostatnou aplikaci Blazorového sestavení, která pro ověřování používá [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) .</span><span class="sxs-lookup"><span data-stu-id="974f4-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="974f4-105">Registrace aplikací v AAD B2C a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="974f4-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="974f4-106">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="974f4-106">Create a tenant</span></span>

<span data-ttu-id="974f4-107">Postupujte podle pokynů v [kurzu: vytvoření tenanta Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pro vytvoření tenanta AAD B2C a zaznamenání následujících informací:</span><span class="sxs-lookup"><span data-stu-id="974f4-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="974f4-108">Instance AAD B2C (například `https://contoso.b2clogin.com/`, která zahrnuje koncové lomítko)</span><span class="sxs-lookup"><span data-stu-id="974f4-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="974f4-109">AAD B2C domény klienta (například `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="974f4-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="974f4-110">Registrace aplikace API serveru</span><span class="sxs-lookup"><span data-stu-id="974f4-110">Register a server API app</span></span>

<span data-ttu-id="974f4-111">Postupujte podle pokynů v [kurzu: registrace aplikace v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) k registraci aplikace AAD pro *aplikaci API serveru* v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="974f4-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="974f4-112">Vyberte **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="974f4-112">Select **New registration**.</span></span>
1. <span data-ttu-id="974f4-113">Zadejte **název** aplikace (například **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="974f4-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="974f4-114">U **podporovaných typů účtů**vyberte **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="974f4-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="974f4-115">(více tenantů) pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="974f4-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="974f4-116">*Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="974f4-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="974f4-117">Potvrďte, že **oprávnění** > **udělují správcům oprávnění k OpenID a offline_access** je povolená.</span><span class="sxs-lookup"><span data-stu-id="974f4-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="974f4-118">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="974f4-118">Select **Register**.</span></span>

<span data-ttu-id="974f4-119">Ve **vystavení rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="974f4-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="974f4-120">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="974f4-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="974f4-121">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="974f4-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="974f4-122">Zadejte **název oboru** (například `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="974f4-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="974f4-123">Zadejte **Zobrazovaný název souhlasu správce** (například `Access API`).</span><span class="sxs-lookup"><span data-stu-id="974f4-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="974f4-124">Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="974f4-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="974f4-125">Potvrďte, že je **stav** nastavený na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="974f4-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="974f4-126">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="974f4-126">Select **Add scope**.</span></span>

<span data-ttu-id="974f4-127">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="974f4-127">Record the following information:</span></span>

* <span data-ttu-id="974f4-128">*Aplikace API serveru* ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="974f4-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="974f4-129">ID adresáře (ID klienta) (například `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="974f4-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="974f4-130">*Aplikace API serveru* Identifikátor URI ID aplikace (například `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, Azure Portal může být ve výchozím nastavení hodnota ID klienta)</span><span class="sxs-lookup"><span data-stu-id="974f4-130">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="974f4-131">Výchozí obor (například `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="974f4-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="974f4-132">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="974f4-132">Register a client app</span></span>

<span data-ttu-id="974f4-133">Postupujte podle pokynů v [kurzu: znovu zaregistrujte aplikaci v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) k registraci aplikace AAD pro *klientskou aplikaci* v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="974f4-133">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="974f4-134">Vyberte **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="974f4-134">Select **New registration**.</span></span>
1. <span data-ttu-id="974f4-135">Zadejte **název** aplikace (například **Blazor AAD B2C klienta**).</span><span class="sxs-lookup"><span data-stu-id="974f4-135">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="974f4-136">U **podporovaných typů účtů**vyberte **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="974f4-136">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="974f4-137">(více tenantů) pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="974f4-137">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="974f4-138">Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro přesměrování `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="974f4-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="974f4-139">Potvrďte, že **oprávnění** > **udělují správcům oprávnění k OpenID a offline_access** je povolená.</span><span class="sxs-lookup"><span data-stu-id="974f4-139">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="974f4-140">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="974f4-140">Select **Register**.</span></span>

<span data-ttu-id="974f4-141">V > ověřování **Konfigurace platforem** > **webu**:</span><span class="sxs-lookup"><span data-stu-id="974f4-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="974f4-142">Potvrďte, že je k dispozici **identifikátor URI pro přesměrování** `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="974f4-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="974f4-143">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="974f4-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="974f4-144">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="974f4-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="974f4-145">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="974f4-145">Select the **Save** button.</span></span>

<span data-ttu-id="974f4-146">V **oprávněních rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="974f4-146">In **API permissions**:</span></span>

1. <span data-ttu-id="974f4-147">Ověřte, že aplikace má **Microsoft Graph** > **uživatel. oprávnění číst** .</span><span class="sxs-lookup"><span data-stu-id="974f4-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="974f4-148">Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="974f4-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="974f4-149">Ve sloupci **název** vyberte *aplikace API serveru* (například **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="974f4-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="974f4-150">Otevřete seznam **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="974f4-150">Open the **API** list.</span></span>
1. <span data-ttu-id="974f4-151">Povolte přístup k rozhraní API (například `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="974f4-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="974f4-152">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="974f4-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="974f4-153">Vyberte tlačítko **pro udělení obsahu správce pro {TENANT}** .</span><span class="sxs-lookup"><span data-stu-id="974f4-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="974f4-154">Odstranění potvrďte výběrem **Ano**.</span><span class="sxs-lookup"><span data-stu-id="974f4-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="974f4-155">V **domácích** > **Azure AD B2C** > **uživatelských toků**:</span><span class="sxs-lookup"><span data-stu-id="974f4-155">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="974f4-156">Vytvoření uživatelského toku pro registraci a přihlašování</span><span class="sxs-lookup"><span data-stu-id="974f4-156">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="974f4-157">Pokud chcete naplnit `context.User.Identity.Name` ve `LoginDisplay` komponentě (*Shared/LoginDisplay. Razor*), vyberte minimálně hodnotu **deklarace identity aplikace** > atribut pro **zobrazení názvu** uživatele.</span><span class="sxs-lookup"><span data-stu-id="974f4-157">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="974f4-158">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="974f4-158">Record the following information:</span></span>

* <span data-ttu-id="974f4-159">Zaznamenejte ID aplikace *klienta aplikace* (ID klienta) (například `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="974f4-159">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="974f4-160">Zaznamenejte si název uživatelského toku pro registraci a přihlašování vytvořený pro aplikaci (například `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="974f4-160">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="974f4-161">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="974f4-161">Create the app</span></span>

<span data-ttu-id="974f4-162">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="974f4-162">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="974f4-163">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="974f4-163">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="974f4-164">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="974f4-164">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="974f4-165">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="974f4-165">Server app configuration</span></span>

<span data-ttu-id="974f4-166">*Tato část se vztahuje k **serverové** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="974f4-166">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="974f4-167">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="974f4-167">Authentication package</span></span>

<span data-ttu-id="974f4-168">Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je poskytována `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="974f4-168">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="974f4-169">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="974f4-169">Authentication service support</span></span>

<span data-ttu-id="974f4-170">Metoda `AddAuthentication` nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="974f4-170">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="974f4-171">Metoda `AddAzureADBearer` nastaví konkrétní parametry v obslužné rutině JWT nosiče vyžadované pro ověřování tokenů emitovaných Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="974f4-171">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="974f4-172">`UseAuthentication` a `UseAuthorization` zajistěte, aby:</span><span class="sxs-lookup"><span data-stu-id="974f4-172">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="974f4-173">Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="974f4-173">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="974f4-174">Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.</span><span class="sxs-lookup"><span data-stu-id="974f4-174">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="974f4-175">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="974f4-175">App settings</span></span>

<span data-ttu-id="974f4-176">Soubor *appSettings. JSON* obsahuje možnosti konfigurace obslužné rutiny nosiče JWT používané k ověření přístupových tokenů.</span><span class="sxs-lookup"><span data-stu-id="974f4-176">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="974f4-177">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="974f4-177">WeatherForecast controller</span></span>

<span data-ttu-id="974f4-178">Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s atributem `[Authorize]` použitým pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="974f4-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="974f4-179">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="974f4-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="974f4-180">Atribut `[Authorize]` v tomto kontroleru rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="974f4-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="974f4-181">Atribut `[Authorize]` použitý v Blazor aplikace WebAssembly slouží pouze jako pomocný parametr aplikace, který by měl být uživatelem autorizován, aby mohla aplikace správně fungovat.</span><span class="sxs-lookup"><span data-stu-id="974f4-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="974f4-182">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="974f4-182">Client app configuration</span></span>

<span data-ttu-id="974f4-183">*Tato část se vztahuje k **klientské** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="974f4-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="974f4-184">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="974f4-184">Authentication package</span></span>

<span data-ttu-id="974f4-185">Když je aplikace vytvořená tak, aby používala individuální účet B2C (`IndividualB2C`), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="974f4-185">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="974f4-186">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="974f4-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="974f4-187">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="974f4-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="974f4-188">V odkazu na předchozí balíček nahraďte `{VERSION}` verzí balíčku `Microsoft.AspNetCore.Blazor.Templates` zobrazeného v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="974f4-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="974f4-189">`Microsoft.Authentication.WebAssembly.Msal` balíček do aplikace přidá balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.</span><span class="sxs-lookup"><span data-stu-id="974f4-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="974f4-190">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="974f4-190">Authentication service support</span></span>

<span data-ttu-id="974f4-191">Podpora ověřování uživatelů je zaregistrovaná v kontejneru služby s metodou rozšíření `AddMsalAuthentication` poskytovanou balíčkem `Microsoft.Authentication.WebAssembly.Msal`.</span><span class="sxs-lookup"><span data-stu-id="974f4-191">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="974f4-192">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="974f4-192">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="974f4-193">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="974f4-193">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

<span data-ttu-id="974f4-194">Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="974f4-194">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="974f4-195">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="974f4-195">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="974f4-196">Šablona Blazor WebAssembly automaticky nakonfiguruje aplikaci tak, aby požadovala přístupový token pro zabezpečené rozhraní API pro výchozí obor, který je k dispozici pro příkaz `dotnet new` (`{APP ID URI}/{DEFAULT SCOPE}`).</span><span class="sxs-lookup"><span data-stu-id="974f4-196">The Blazor WebAssembly template automatically configures the app to request an access token for a secure API for the default scope provided to the `dotnet new` command (`{APP ID URI}/{DEFAULT SCOPE}`).</span></span>

<span data-ttu-id="974f4-197">Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:</span><span class="sxs-lookup"><span data-stu-id="974f4-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="974f4-198">Ve výchozím nastavení zahrnuty v žádosti o přihlášení.</span><span class="sxs-lookup"><span data-stu-id="974f4-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="974f4-199">Slouží ke zřízení přístupového tokenu hned po ověření.</span><span class="sxs-lookup"><span data-stu-id="974f4-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="974f4-200">Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="974f4-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="974f4-201">Další obory je možné přidat pro další aplikace API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="974f4-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="974f4-202">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="974f4-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="974f4-203">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="974f4-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="974f4-204">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="974f4-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="974f4-205">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="974f4-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="974f4-206">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="974f4-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="974f4-207">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="974f4-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="974f4-208">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="974f4-208">Run the app</span></span>

<span data-ttu-id="974f4-209">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="974f4-209">Run the app from the Server project.</span></span> <span data-ttu-id="974f4-210">Při použití sady Visual Studio vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="974f4-210">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="974f4-211">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="974f4-211">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="974f4-212">Kurz: Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="974f4-212">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
