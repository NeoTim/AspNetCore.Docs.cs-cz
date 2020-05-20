---
<span data-ttu-id="86a4a-101">title: ' zabezpečení Blazor samostatné aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory B2C ' Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="86a4a-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="86a4a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="86a4a-102">'Blazor'</span></span>
- <span data-ttu-id="86a4a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="86a4a-103">'Identity'</span></span>
- <span data-ttu-id="86a4a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="86a4a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="86a4a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="86a4a-105">'Razor'</span></span>
- <span data-ttu-id="86a4a-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="86a4a-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="86a4a-107">Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="86a4a-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="86a4a-108">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="86a4a-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="86a4a-109">Chcete-li vytvořit Blazor samostatnou aplikaci WebAssembly, která pro ověřování používá [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) :</span><span class="sxs-lookup"><span data-stu-id="86a4a-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="86a4a-110">Při vytváření tenanta a registraci webové aplikace na webu Azure Portal postupujte podle pokynů v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="86a4a-110">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="86a4a-111">Vytvoření tenanta AAD B2C</span><span class="sxs-lookup"><span data-stu-id="86a4a-111">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="86a4a-112">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="86a4a-112">Record the following information:</span></span>

* <span data-ttu-id="86a4a-113">Instance AAD B2C (například `https://contoso.b2clogin.com/` , která obsahuje koncové lomítko).</span><span class="sxs-lookup"><span data-stu-id="86a4a-113">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="86a4a-114">AAD B2C domény klienta (například `contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="86a4a-114">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="86a4a-115">Postupujte podle pokynů v [kurzu: registrace aplikace v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) znovu k registraci aplikace AAD pro *klientskou aplikaci*:</span><span class="sxs-lookup"><span data-stu-id="86a4a-115">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="86a4a-116">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="86a4a-116">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="86a4a-117">Zadejte **název** aplikace (například \*\* Blazor samostatné AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="86a4a-117">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="86a4a-118">U **podporovaných typů účtů**vyberte možnost více tenantů: **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="86a4a-118">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="86a4a-119">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web**a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="86a4a-119">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="86a4a-120">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="86a4a-120">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="86a4a-121">Pro IIS Express se náhodně generovaný port dá najít ve vlastnostech aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="86a4a-121">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="86a4a-122">Potvrďte, že **oprávnění**  >  **udělují správcům oprávnění k OpenID a offline_access** jsou povolená.</span><span class="sxs-lookup"><span data-stu-id="86a4a-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="86a4a-123">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="86a4a-123">Select **Register**.</span></span>

<span data-ttu-id="86a4a-124">Poznamenejte si ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="86a4a-124">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="86a4a-125">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="86a4a-125">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="86a4a-126">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="86a4a-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="86a4a-127">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="86a4a-127">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="86a4a-128">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="86a4a-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="86a4a-129">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="86a4a-129">Select the **Save** button.</span></span>

<span data-ttu-id="86a4a-130">V **Home**  >  **Azure AD B2C**  >  **toky pro uživatele**doma:</span><span class="sxs-lookup"><span data-stu-id="86a4a-130">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="86a4a-131">Vytvoření uživatelského toku pro registraci a přihlašování</span><span class="sxs-lookup"><span data-stu-id="86a4a-131">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="86a4a-132">Aby bylo **Application claims**  >  **Display Name** možné naplnit `context.User.Identity.Name` `LoginDisplay` součást (*Shared/LoginDisplay. Razor*), vyberte alespoň atribut uživatele zobrazovaný název deklarací identity aplikace.</span><span class="sxs-lookup"><span data-stu-id="86a4a-132">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="86a4a-133">Zaznamenejte si název uživatelského toku pro registraci a přihlašování vytvořený pro aplikaci (například `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="86a4a-133">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="86a4a-134">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="86a4a-134">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="86a4a-135">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="86a4a-135">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="86a4a-136">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="86a4a-136">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="86a4a-137">Po vytvoření aplikace byste měli mít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="86a4a-137">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="86a4a-138">Přihlaste se k aplikaci pomocí uživatelského účtu AAD.</span><span class="sxs-lookup"><span data-stu-id="86a4a-138">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="86a4a-139">Vyžádá přístupové tokeny pro rozhraní API Microsoftu.</span><span class="sxs-lookup"><span data-stu-id="86a4a-139">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="86a4a-140">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="86a4a-140">For more information, see:</span></span>
  * [<span data-ttu-id="86a4a-141">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="86a4a-141">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="86a4a-142">[Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)</span><span class="sxs-lookup"><span data-stu-id="86a4a-142">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="86a4a-143">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="86a4a-143">Authentication package</span></span>

<span data-ttu-id="86a4a-144">Když je aplikace vytvořená tak, aby používala individuální účet B2C ( `IndividualB2C` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="86a4a-144">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="86a4a-145">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="86a4a-145">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="86a4a-146">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="86a4a-146">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="86a4a-147">`Microsoft.Authentication.WebAssembly.Msal`Balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication` do této aplikace přidá balíček.</span><span class="sxs-lookup"><span data-stu-id="86a4a-147">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="86a4a-148">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="86a4a-148">Authentication service support</span></span>

<span data-ttu-id="86a4a-149">Podpora ověřování uživatelů je registrovaná v kontejneru služby s `AddMsalAuthentication` metodou rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="86a4a-149">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="86a4a-150">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s Identity poskytovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="86a4a-150">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="86a4a-151">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="86a4a-151">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="86a4a-152">`AddMsalAuthentication`Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="86a4a-152">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="86a4a-153">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="86a4a-153">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="86a4a-154">Konfigurace je dodána souborem *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="86a4a-154">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="86a4a-155">Příklad:</span><span class="sxs-lookup"><span data-stu-id="86a4a-155">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="86a4a-156">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="86a4a-156">Access token scopes</span></span>

<span data-ttu-id="86a4a-157">BlazorŠablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="86a4a-157">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="86a4a-158">Pokud chcete jako součást toku přihlášení zřídit přístupový token, přidejte obor do výchozích oborů přístupového tokenu `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="86a4a-158">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="86a4a-159">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="86a4a-159">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="86a4a-160">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="86a4a-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="86a4a-161">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="86a4a-161">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="86a4a-162">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="86a4a-162">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="86a4a-163">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="86a4a-163">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="86a4a-164">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="86a4a-164">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="86a4a-165">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="86a4a-165">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="86a4a-166">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="86a4a-166">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="86a4a-167">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="86a4a-167">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="86a4a-168">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="86a4a-168">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="86a4a-169">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="86a4a-169">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="86a4a-170">Kurz: Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="86a4a-170">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="86a4a-171">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="86a4a-171">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
