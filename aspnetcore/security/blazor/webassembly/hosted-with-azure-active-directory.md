---
title: Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 0803e436d66ef7df3c68739e674a8652fde11166
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083844"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="d5aa7-102">Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="d5aa7-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="d5aa7-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d5aa7-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



<span data-ttu-id="d5aa7-104">Tento článek popisuje, jak vytvořit [hostovanou aplikaciBlazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) , která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) .</span><span class="sxs-lookup"><span data-stu-id="d5aa7-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="d5aa7-105">Registrace aplikací v AAD B2C a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="d5aa7-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="d5aa7-106">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="d5aa7-106">Create a tenant</span></span>

<span data-ttu-id="d5aa7-107">Postupujte podle pokynů v [rychlém startu: nastavení tenanta](/azure/active-directory/develop/quickstart-create-new-tenant) pro vytvoření TENANTA v AAD.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="d5aa7-108">Registrace aplikace API serveru</span><span class="sxs-lookup"><span data-stu-id="d5aa7-108">Register a server API app</span></span>

<span data-ttu-id="d5aa7-109">Postupujte podle pokynů v [rychlém startu: registrace aplikace pomocí platformy Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat služby Azure AAD k registraci aplikace AAD pro *aplikaci API serveru* v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="d5aa7-110">Vyberte **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-110">Select **New registration**.</span></span>
1. <span data-ttu-id="d5aa7-111">Zadejte **název** aplikace (například **AADBlazor serveru**).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="d5aa7-112">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="d5aa7-113">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="d5aa7-114">*Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="d5aa7-115">Zakažte **oprávnění** > oprávnění **správce udělit OpenID a offline_access oprávnění** .</span><span class="sxs-lookup"><span data-stu-id="d5aa7-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="d5aa7-116">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-116">Select **Register**.</span></span>

<span data-ttu-id="d5aa7-117">V okně **oprávnění rozhraní API**odeberte **Microsoft Graph** > **User. Read** , protože aplikace nevyžaduje přístup k UER nebo k profilu.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="d5aa7-118">Ve **vystavení rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="d5aa7-119">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="d5aa7-120">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="d5aa7-121">Zadejte **název oboru** (například `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="d5aa7-122">Zadejte **Zobrazovaný název souhlasu správce** (například `Access API`).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="d5aa7-123">Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="d5aa7-124">Potvrďte, že je **stav** nastavený na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="d5aa7-125">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-125">Select **Add scope**.</span></span>

<span data-ttu-id="d5aa7-126">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-126">Record the following information:</span></span>

* <span data-ttu-id="d5aa7-127">*Aplikace API serveru* ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="d5aa7-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="d5aa7-128">ID adresáře (ID klienta) (například `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="d5aa7-128">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="d5aa7-129">Doména tenanta AAD (například `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="d5aa7-129">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="d5aa7-130">Výchozí obor (například `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="d5aa7-130">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="d5aa7-131">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="d5aa7-131">Register a client app</span></span>

<span data-ttu-id="d5aa7-132">Postupujte podle pokynů v [rychlém startu: registrace aplikace pomocí Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat Azure AAD k registraci aplikace AAD pro *klientskou aplikaci* v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-132">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="d5aa7-133">Vyberte **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-133">Select **New registration**.</span></span>
1. <span data-ttu-id="d5aa7-134">Zadejte **název** aplikace (například **Blazor AAD klienta**).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-134">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="d5aa7-135">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-135">Choose a **Supported account types**.</span></span> <span data-ttu-id="d5aa7-136">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-136">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="d5aa7-137">Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro přesměrování `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-137">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="d5aa7-138">Zakažte **oprávnění** > oprávnění **správce udělit OpenID a offline_access oprávnění** .</span><span class="sxs-lookup"><span data-stu-id="d5aa7-138">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="d5aa7-139">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-139">Select **Register**.</span></span>

<span data-ttu-id="d5aa7-140">V > ověřování **Konfigurace platforem** > **webu**:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-140">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="d5aa7-141">Potvrďte, že je k dispozici **identifikátor URI pro přesměrování** `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-141">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="d5aa7-142">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-142">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="d5aa7-143">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-143">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="d5aa7-144">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-144">Select the **Save** button.</span></span>

<span data-ttu-id="d5aa7-145">V **oprávněních rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-145">In **API permissions**:</span></span>

1. <span data-ttu-id="d5aa7-146">Ověřte, že aplikace má **Microsoft Graph** > **uživatel. oprávnění číst** .</span><span class="sxs-lookup"><span data-stu-id="d5aa7-146">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="d5aa7-147">Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-147">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="d5aa7-148">Vyberte *aplikaci API serveru* ze sloupce **název** (například **Blazor serveru AAD**).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-148">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="d5aa7-149">Otevřete seznam **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="d5aa7-149">Open the **API** list.</span></span>
1. <span data-ttu-id="d5aa7-150">Povolte přístup k rozhraní API (například `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-150">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="d5aa7-151">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-151">Select **Add permissions**.</span></span>
1. <span data-ttu-id="d5aa7-152">Vyberte tlačítko **pro udělení obsahu správce pro {TENANT}** .</span><span class="sxs-lookup"><span data-stu-id="d5aa7-152">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="d5aa7-153">Odstranění potvrďte výběrem **Ano**.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-153">Select **Yes** to confirm.</span></span>

<span data-ttu-id="d5aa7-154">Zaznamenejte ID aplikace *klienta aplikace* (ID klienta) (například `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-154">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="d5aa7-155">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="d5aa7-155">Create the app</span></span>

<span data-ttu-id="d5aa7-156">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-156">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP CLIENT ID}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="d5aa7-157">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-157">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="d5aa7-158">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-158">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="d5aa7-159">V části [Podpora služby ověřování](#Authentication service support) najdete důležitou změnu konfigurace pro výchozí obor tokenu přístupu.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-159">See the [Authentication service support](#Authentication service support) section for an important configuration change to the default access token scope.</span></span> <span data-ttu-id="d5aa7-160">Hodnota poskytnutá šablonou Blazor WebAssembly se musí ručně změnit po vytvoření *klientské aplikace* ze šablony.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-160">The value provided by the Blazor WebAssembly template must be manually changed after the *Client app* is created from the template.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="d5aa7-161">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="d5aa7-161">Server app configuration</span></span>

<span data-ttu-id="d5aa7-162">*Tato část se vztahuje k **serverové** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="d5aa7-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d5aa7-163">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="d5aa7-163">Authentication package</span></span>

<span data-ttu-id="d5aa7-164">Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je poskytována `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="d5aa7-165">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="d5aa7-165">Authentication service support</span></span>

<span data-ttu-id="d5aa7-166">Metoda `AddAuthentication` nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="d5aa7-167">Metoda `AddAzureADBearer` nastaví konkrétní parametry v obslužné rutině JWT nosiče vyžadované pro ověřování tokenů emitovaných Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="d5aa7-168">`UseAuthentication` a `UseAuthorization` zajistěte, aby:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="d5aa7-169">Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="d5aa7-170">Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="d5aa7-171">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="d5aa7-171">App settings</span></span>

<span data-ttu-id="d5aa7-172">Soubor *appSettings. JSON* obsahuje možnosti konfigurace obslužné rutiny nosiče JWT používané k ověření přístupových tokenů.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-172">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="d5aa7-173">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="d5aa7-173">WeatherForecast controller</span></span>

<span data-ttu-id="d5aa7-174">Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s atributem `[Authorize]` použitým pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-174">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="d5aa7-175">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-175">It's **important** to understand that:</span></span>

* <span data-ttu-id="d5aa7-176">Atribut `[Authorize]` v tomto kontroleru rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-176">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="d5aa7-177">Atribut `[Authorize]` použitý v Blazor aplikace WebAssembly slouží pouze jako pomocný parametr aplikace, který by měl být uživatelem autorizován, aby mohla aplikace správně fungovat.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-177">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="d5aa7-178">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="d5aa7-178">Client app configuration</span></span>

<span data-ttu-id="d5aa7-179">*Tato část se vztahuje k **klientské** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="d5aa7-179">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d5aa7-180">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="d5aa7-180">Authentication package</span></span>

<span data-ttu-id="d5aa7-181">Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty (`SingleOrg`), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-181">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="d5aa7-182">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-182">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d5aa7-183">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-183">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="d5aa7-184">V odkazu na předchozí balíček nahraďte `{VERSION}` verzí balíčku `Microsoft.AspNetCore.Blazor.Templates` zobrazeného v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-184">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="d5aa7-185">`Microsoft.Authentication.WebAssembly.Msal` balíček do aplikace přidá balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-185">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="d5aa7-186">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="d5aa7-186">Authentication service support</span></span>

<span data-ttu-id="d5aa7-187">Podpora ověřování uživatelů je zaregistrovaná v kontejneru služby s metodou rozšíření `AddMsalAuthentication` poskytovanou balíčkem `Microsoft.Authentication.WebAssembly.Msal`.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-187">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="d5aa7-188">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-188">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="d5aa7-189">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-189">*Program.cs*:</span></span>

<span data-ttu-id="d5aa7-190">Když se vygeneruje *klientská aplikace* , výchozí rozsah přístupového tokenu je `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`formátu.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-190">When the *Client app* is generated, the default access token scope is of the format `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`.</span></span> <span data-ttu-id="d5aa7-191">**Odeberte část `api://` hodnoty oboru.**</span><span class="sxs-lookup"><span data-stu-id="d5aa7-191">**Remove the `api://` portion of the scope value.**</span></span> <span data-ttu-id="d5aa7-192">Tento problém bude řešen v budoucí verzi Preview.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-192">This issue will be addressed in a future preview release.</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> <span data-ttu-id="d5aa7-193">Výchozí rozsah přístupového tokenu musí být ve formátu `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (například `11111111-1111-1111-1111-111111111111/API.Access`).</span><span class="sxs-lookup"><span data-stu-id="d5aa7-193">The default access token scope must be in the format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (for example, `11111111-1111-1111-1111-111111111111/API.Access`).</span></span> <span data-ttu-id="d5aa7-194">Pokud se schéma nebo schéma a hostitel poskytne nastavení oboru (jak je znázorněno na webu Azure Portal), *klientská aplikace* vyvolá neošetřenou výjimku, pokud obdrží *neautorizovanou odpověď 401* od *aplikace API serveru*.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-194">If a scheme or scheme and host is provided to the scope setting (as shown in the Azure Portal), the *Client app* throws an unhandled exception when it receives a *401 Unauthorized* response from the *Server API app*.</span></span>

<span data-ttu-id="d5aa7-195">Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="d5aa7-196">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="d5aa7-197">Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="d5aa7-198">Ve výchozím nastavení zahrnuty v žádosti o přihlášení.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="d5aa7-199">Slouží ke zřízení přístupového tokenu hned po ověření.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="d5aa7-200">Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="d5aa7-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="d5aa7-201">Další obory je možné přidat pro další aplikace API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="d5aa7-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="d5aa7-202">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="d5aa7-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="d5aa7-203">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="d5aa7-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="d5aa7-204">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="d5aa7-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="d5aa7-205">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="d5aa7-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="d5aa7-206">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="d5aa7-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="d5aa7-207">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="d5aa7-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d5aa7-208">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d5aa7-208">Additional resources</span></span>

* <xref:security/authentication/azure-active-directory/index>
