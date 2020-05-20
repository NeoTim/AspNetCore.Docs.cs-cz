---
<span data-ttu-id="1e552-101">title: ' zabezpečení Blazor samostatné aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory ' Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1e552-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e552-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e552-102">'Blazor'</span></span>
- <span data-ttu-id="1e552-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e552-103">'Identity'</span></span>
- <span data-ttu-id="1e552-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e552-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e552-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e552-105">'Razor'</span></span>
- <span data-ttu-id="1e552-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="1e552-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="1e552-107">Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="1e552-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="1e552-108">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1e552-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1e552-109">Vytvoření Blazor samostatné aplikace WebAssembly, která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) :</span><span class="sxs-lookup"><span data-stu-id="1e552-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="1e552-110">[Vytvoření TENANTA AAD a webové aplikace](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="1e552-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="1e552-111">Zaregistrujte aplikaci AAD v **Azure Active Directory**  >  **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="1e552-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="1e552-112">Zadejte **název** aplikace (například \*\* Blazor samostatná AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="1e552-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="1e552-113">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="1e552-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="1e552-114">**Účty v tomto organizačním adresáři** můžete vybrat jenom pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="1e552-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="1e552-115">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web**a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="1e552-115">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="1e552-116">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="1e552-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="1e552-117">Pro IIS Express se náhodně generovaný port dá najít ve vlastnostech aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="1e552-117">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="1e552-118">Zakažte **oprávnění**  >  **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="1e552-118">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="1e552-119">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="1e552-119">Select **Register**.</span></span>

<span data-ttu-id="1e552-120">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="1e552-120">Record the following information:</span></span>

* <span data-ttu-id="1e552-121">ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="1e552-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="1e552-122">ID adresáře (ID klienta) (například `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="1e552-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="1e552-123">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="1e552-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="1e552-124">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="1e552-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="1e552-125">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="1e552-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="1e552-126">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="1e552-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="1e552-127">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="1e552-127">Select the **Save** button.</span></span>

<span data-ttu-id="1e552-128">Vytvořte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1e552-128">Create the app.</span></span> <span data-ttu-id="1e552-129">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="1e552-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="1e552-130">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="1e552-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1e552-131">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="1e552-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="1e552-132">Po vytvoření aplikace byste měli mít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="1e552-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="1e552-133">Přihlaste se k aplikaci pomocí uživatelského účtu AAD.</span><span class="sxs-lookup"><span data-stu-id="1e552-133">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="1e552-134">Vyžádá přístupové tokeny pro rozhraní API Microsoftu.</span><span class="sxs-lookup"><span data-stu-id="1e552-134">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="1e552-135">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="1e552-135">For more information, see:</span></span>
  * [<span data-ttu-id="1e552-136">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="1e552-136">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="1e552-137">[Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)</span><span class="sxs-lookup"><span data-stu-id="1e552-137">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="1e552-138">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="1e552-138">Authentication package</span></span>

<span data-ttu-id="1e552-139">Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty ( `SingleOrg` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="1e552-139">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1e552-140">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1e552-140">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1e552-141">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="1e552-141">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="1e552-142">`Microsoft.Authentication.WebAssembly.Msal`Balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication` do této aplikace přidá balíček.</span><span class="sxs-lookup"><span data-stu-id="1e552-142">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="1e552-143">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="1e552-143">Authentication service support</span></span>

<span data-ttu-id="1e552-144">Podpora ověřování uživatelů je registrovaná v kontejneru služby s `AddMsalAuthentication` metodou rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="1e552-144">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1e552-145">Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="1e552-145">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1e552-146">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e552-146">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="1e552-147">`AddMsalAuthentication`Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e552-147">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1e552-148">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="1e552-148">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="1e552-149">Konfigurace je dodána souborem *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1e552-149">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="1e552-150">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1e552-150">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="1e552-151">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="1e552-151">Access token scopes</span></span>

<span data-ttu-id="1e552-152">BlazorŠablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1e552-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="1e552-153">Pokud chcete jako součást toku přihlášení zřídit přístupový token, přidejte obor do výchozích oborů přístupového tokenu `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="1e552-153">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="1e552-154">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="1e552-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="1e552-155">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="1e552-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="1e552-156">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="1e552-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="1e552-157">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="1e552-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="1e552-158">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="1e552-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="1e552-159">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="1e552-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="1e552-160">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="1e552-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="1e552-161">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="1e552-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="1e552-162">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="1e552-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1e552-163">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1e552-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="1e552-164">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="1e552-164">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="1e552-165">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="1e552-165">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
