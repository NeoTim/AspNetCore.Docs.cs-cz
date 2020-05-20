---
<span data-ttu-id="0f87b-101">title: ' zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory ' Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0f87b-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0f87b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0f87b-102">'Blazor'</span></span>
- <span data-ttu-id="0f87b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0f87b-103">'Identity'</span></span>
- <span data-ttu-id="0f87b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0f87b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="0f87b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0f87b-105">'Razor'</span></span>
- <span data-ttu-id="0f87b-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="0f87b-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="0f87b-107">Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="0f87b-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="0f87b-108">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0f87b-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0f87b-109">Tento článek popisuje, jak vytvořit [ Blazor hostovanou aplikaci WebAssembly](xref:blazor/hosting-models#blazor-webassembly) , která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) .</span><span class="sxs-lookup"><span data-stu-id="0f87b-109">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="0f87b-110">Registrace aplikací v AAD a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="0f87b-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="0f87b-111">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="0f87b-111">Create a tenant</span></span>

<span data-ttu-id="0f87b-112">Postupujte podle pokynů v [rychlém startu: nastavení tenanta](/azure/active-directory/develop/quickstart-create-new-tenant) pro vytvoření TENANTA v AAD.</span><span class="sxs-lookup"><span data-stu-id="0f87b-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="0f87b-113">Registrace aplikace API serveru</span><span class="sxs-lookup"><span data-stu-id="0f87b-113">Register a server API app</span></span>

<span data-ttu-id="0f87b-114">Postupujte podle pokynů v [rychlém startu: registrace aplikace s využitím platformy Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat Azure AAD k registraci aplikace AAD pro *aplikaci API serveru*:</span><span class="sxs-lookup"><span data-stu-id="0f87b-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="0f87b-115">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="0f87b-116">Zadejte **název** aplikace (například \*\* Blazor AAD serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="0f87b-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="0f87b-117">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="0f87b-118">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="0f87b-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="0f87b-119">*Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="0f87b-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="0f87b-120">Zakažte **oprávnění**  >  **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="0f87b-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="0f87b-121">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-121">Select **Register**.</span></span>

<span data-ttu-id="0f87b-122">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="0f87b-122">Record the following information:</span></span>

* <span data-ttu-id="0f87b-123">*Aplikace API serveru* ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="0f87b-123">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="0f87b-124">ID adresáře (ID klienta) (například `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="0f87b-124">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="0f87b-125">Doména tenanta AAD (například `contoso.onmicrosoft.com` ) &ndash; doména je k dispozici jako **doména vydavatele** v okně **značky** Azure Portal pro registrovanou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0f87b-125">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="0f87b-126">V okně **oprávnění rozhraní API**odeberte **Microsoft Graph**  >  **uživatel. číst** , protože aplikace nevyžaduje přihlášení ani přístup k profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="0f87b-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="0f87b-127">Ve **vystavení rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="0f87b-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="0f87b-128">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="0f87b-129">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="0f87b-130">Zadejte **název oboru** (například `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="0f87b-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="0f87b-131">Zadejte **Zobrazovaný název souhlasu správce** (například `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="0f87b-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="0f87b-132">Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="0f87b-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="0f87b-133">Potvrďte, že je **stav** nastavený na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="0f87b-134">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-134">Select **Add scope**.</span></span>

<span data-ttu-id="0f87b-135">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="0f87b-135">Record the following information:</span></span>

* <span data-ttu-id="0f87b-136">Identifikátor URI ID aplikace (například, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` nebo vlastní hodnota, kterou jste zadali)</span><span class="sxs-lookup"><span data-stu-id="0f87b-136">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="0f87b-137">Výchozí obor (například `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="0f87b-137">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="0f87b-138">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="0f87b-138">Register a client app</span></span>

<span data-ttu-id="0f87b-139">Postupujte podle pokynů v [rychlém startu: registrace aplikace s využitím platformy Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat Azure AAD k registraci aplikace AAD pro *klientskou aplikaci*:</span><span class="sxs-lookup"><span data-stu-id="0f87b-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="0f87b-140">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="0f87b-141">Zadejte **název** aplikace (například \*\* Blazor AAD klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="0f87b-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="0f87b-142">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="0f87b-143">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="0f87b-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="0f87b-144">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web**a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="0f87b-144">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="0f87b-145">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="0f87b-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="0f87b-146">Pro IIS Express se náhodně generovaný port dá najít ve vlastnostech serverové aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="0f87b-146">For IIS Express, the randomly generated port can be found in the Server app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="0f87b-147">Zakažte **oprávnění**  >  **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="0f87b-147">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="0f87b-148">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-148">Select **Register**.</span></span>

<span data-ttu-id="0f87b-149">Zaznamenejte ID aplikace *klienta aplikace* (ID klienta) (například `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="0f87b-149">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="0f87b-150">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="0f87b-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="0f87b-151">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="0f87b-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="0f87b-152">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="0f87b-153">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="0f87b-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="0f87b-154">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-154">Select the **Save** button.</span></span>

<span data-ttu-id="0f87b-155">V **oprávněních rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="0f87b-155">In **API permissions**:</span></span>

1. <span data-ttu-id="0f87b-156">Potvrďte, že aplikace má **Microsoft Graph**  >  oprávnění**uživatel. číst** .</span><span class="sxs-lookup"><span data-stu-id="0f87b-156">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="0f87b-157">Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-157">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="0f87b-158">Vyberte *aplikaci API serveru* ze sloupce **název** (například \*\* Blazor AAD serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="0f87b-158">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="0f87b-159">Otevřete seznam **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="0f87b-159">Open the **API** list.</span></span>
1. <span data-ttu-id="0f87b-160">Povolte přístup k rozhraní API (například `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="0f87b-160">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="0f87b-161">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="0f87b-161">Select **Add permissions**.</span></span>
1. <span data-ttu-id="0f87b-162">Vyberte tlačítko **pro udělení obsahu správce pro {TENANT}** .</span><span class="sxs-lookup"><span data-stu-id="0f87b-162">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="0f87b-163">Výběrem **Ano** potvrďte.</span><span class="sxs-lookup"><span data-stu-id="0f87b-163">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="0f87b-164">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="0f87b-164">Create the app</span></span>

<span data-ttu-id="0f87b-165">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="0f87b-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="0f87b-166">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="0f87b-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="0f87b-167">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="0f87b-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="0f87b-168">Předejte tomuto parametru identifikátor URI ID aplikace `app-id-uri` , ale Všimněte si, že se v klientské aplikaci může vyžadovat Změna konfigurace, která je popsaná v části [obory přístupového tokenu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="0f87b-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="0f87b-169">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="0f87b-169">Server app configuration</span></span>

<span data-ttu-id="0f87b-170">*Tato část se vztahuje k **serverové** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="0f87b-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="0f87b-171">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="0f87b-171">Authentication package</span></span>

<span data-ttu-id="0f87b-172">Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je poskytována `Microsoft.AspNetCore.Authentication.AzureAD.UI` :</span><span class="sxs-lookup"><span data-stu-id="0f87b-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.2.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="0f87b-173">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="0f87b-173">Authentication service support</span></span>

<span data-ttu-id="0f87b-174">`AddAuthentication`Metoda nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="0f87b-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="0f87b-175">`AddAzureADBearer`Metoda nastavuje konkrétní parametry v obslužné rutině JWT nosiče vyžadované k ověření tokenů vygenerovaných Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="0f87b-175">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="0f87b-176">`UseAuthentication`a `UseAuthorization` Ujistěte se, že:</span><span class="sxs-lookup"><span data-stu-id="0f87b-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="0f87b-177">Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="0f87b-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="0f87b-178">Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.</span><span class="sxs-lookup"><span data-stu-id="0f87b-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="0f87b-179">Uživatel. Identity . Jméno</span><span class="sxs-lookup"><span data-stu-id="0f87b-179">User.Identity.Name</span></span>

<span data-ttu-id="0f87b-180">Ve výchozím nastavení rozhraní API serverové aplikace naplní `User.Identity.Name` hodnotu z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu deklarace (například `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="0f87b-180">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="0f87b-181">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hodnotu z `name` typu deklarace, nakonfigurujte [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0f87b-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="0f87b-182">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="0f87b-182">App settings</span></span>

<span data-ttu-id="0f87b-183">Soubor *appSettings. JSON* obsahuje možnosti konfigurace obslužné rutiny nosiče JWT používané k ověření přístupových tokenů:</span><span class="sxs-lookup"><span data-stu-id="0f87b-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="0f87b-184">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0f87b-184">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="0f87b-185">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="0f87b-185">WeatherForecast controller</span></span>

<span data-ttu-id="0f87b-186">Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s `[Authorize]` atributem použitým pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="0f87b-186">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="0f87b-187">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="0f87b-187">It's **important** to understand that:</span></span>

* <span data-ttu-id="0f87b-188">`[Authorize]`Atribut v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="0f87b-188">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="0f87b-189">`[Authorize]`Atribut použitý v Blazor aplikaci WebAssembly slouží pouze jako pomocný parametr aplikace, který by měl být uživatelem autorizován, aby mohla aplikace správně fungovat.</span><span class="sxs-lookup"><span data-stu-id="0f87b-189">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="0f87b-190">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="0f87b-190">Client app configuration</span></span>

<span data-ttu-id="0f87b-191">*Tato část se vztahuje k **klientské** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="0f87b-191">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="0f87b-192">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="0f87b-192">Authentication package</span></span>

<span data-ttu-id="0f87b-193">Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty ( `SingleOrg` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="0f87b-193">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="0f87b-194">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="0f87b-194">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="0f87b-195">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="0f87b-195">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="0f87b-196">`Microsoft.Authentication.WebAssembly.Msal`Balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication` do této aplikace přidá balíček.</span><span class="sxs-lookup"><span data-stu-id="0f87b-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="0f87b-197">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="0f87b-197">Authentication service support</span></span>

<span data-ttu-id="0f87b-198">Přidávají `HttpClient` se podpory pro instance, které zahrnují přístupové tokeny při vytváření žádostí na serverový projekt.</span><span class="sxs-lookup"><span data-stu-id="0f87b-198">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="0f87b-199">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0f87b-199">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="0f87b-200">Podpora ověřování uživatelů je registrovaná v kontejneru služby s `AddMsalAuthentication` metodou rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="0f87b-200">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="0f87b-201">Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="0f87b-201">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="0f87b-202">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0f87b-202">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="0f87b-203">`AddMsalAuthentication`Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="0f87b-203">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="0f87b-204">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="0f87b-204">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="0f87b-205">Konfigurace je dodána souborem *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="0f87b-205">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="0f87b-206">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0f87b-206">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="0f87b-207">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="0f87b-207">Access token scopes</span></span>

<span data-ttu-id="0f87b-208">Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:</span><span class="sxs-lookup"><span data-stu-id="0f87b-208">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="0f87b-209">Ve výchozím nastavení zahrnuty v žádosti o přihlášení.</span><span class="sxs-lookup"><span data-stu-id="0f87b-209">Included by default in the sign in request.</span></span>
* <span data-ttu-id="0f87b-210">Slouží ke zřízení přístupového tokenu hned po ověření.</span><span class="sxs-lookup"><span data-stu-id="0f87b-210">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="0f87b-211">Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="0f87b-211">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="0f87b-212">Další obory je možné přidat pro další aplikace API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="0f87b-212">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="0f87b-213">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="0f87b-213">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="0f87b-214">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="0f87b-214">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="0f87b-215">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="0f87b-215">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="0f87b-216">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="0f87b-216">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="0f87b-217">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="0f87b-217">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="0f87b-218">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="0f87b-218">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="0f87b-219">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="0f87b-219">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="0f87b-220">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="0f87b-220">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="0f87b-221">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="0f87b-221">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="0f87b-222">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="0f87b-222">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="0f87b-223">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="0f87b-223">Run the app</span></span>

<span data-ttu-id="0f87b-224">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="0f87b-224">Run the app from the Server project.</span></span> <span data-ttu-id="0f87b-225">Při použití sady Visual Studio buď:</span><span class="sxs-lookup"><span data-stu-id="0f87b-225">When using Visual Studio, either:</span></span>

* <span data-ttu-id="0f87b-226">Nastavte rozevírací seznam **projekty po spuštění** na panelu nástrojů na *aplikaci API serveru* a vyberte tlačítko **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="0f87b-226">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="0f87b-227">Vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="0f87b-227">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="0f87b-228">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0f87b-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="0f87b-229">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="0f87b-229">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="0f87b-230">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="0f87b-230">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
