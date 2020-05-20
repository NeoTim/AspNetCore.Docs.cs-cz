---
<span data-ttu-id="3f8cc-101">title: ' zabezpečení Blazor samostatné aplikace ASP.NET Core WebAssembly s účty Microsoft ' Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3f8cc-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f8cc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f8cc-102">'Blazor'</span></span>
- <span data-ttu-id="3f8cc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f8cc-103">'Identity'</span></span>
- <span data-ttu-id="3f8cc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f8cc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f8cc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f8cc-105">'Razor'</span></span>
- <span data-ttu-id="3f8cc-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3f8cc-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="3f8cc-107">Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí účtů Microsoft</span><span class="sxs-lookup"><span data-stu-id="3f8cc-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="3f8cc-108">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3f8cc-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3f8cc-109">Vytvoření Blazor samostatné aplikace WebAssembly, která používá [účty Microsoft s Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pro ověřování:</span><span class="sxs-lookup"><span data-stu-id="3f8cc-109">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="3f8cc-110">Vytvoření tenanta a webové aplikace AAD</span><span class="sxs-lookup"><span data-stu-id="3f8cc-110">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="3f8cc-111">Zaregistrujte aplikaci AAD v **Azure Active Directory**  >  **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="3f8cc-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="3f8cc-112">Zadejte **název** aplikace (například \*\* Blazor samostatné účty Microsoft AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="3f8cc-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="3f8cc-113">V **podporovaných typech účtů**vyberte **účty v libovolném organizačním adresáři**.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-113">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="3f8cc-114">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web**a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="3f8cc-114">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="3f8cc-115">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="3f8cc-116">Pro IIS Express se náhodně generovaný port dá najít ve vlastnostech aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="3f8cc-116">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="3f8cc-117">Zakažte **oprávnění**  >  **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="3f8cc-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="3f8cc-118">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-118">Select **Register**.</span></span>

<span data-ttu-id="3f8cc-119">Poznamenejte si ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="3f8cc-119">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="3f8cc-120">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="3f8cc-120">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="3f8cc-121">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-121">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="3f8cc-122">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-122">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="3f8cc-123">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-123">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="3f8cc-124">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-124">Select the **Save** button.</span></span>

<span data-ttu-id="3f8cc-125">Vytvořte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-125">Create the app.</span></span> <span data-ttu-id="3f8cc-126">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="3f8cc-126">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="3f8cc-127">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="3f8cc-127">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3f8cc-128">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-128">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="3f8cc-129">Po vytvoření aplikace byste měli mít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="3f8cc-129">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="3f8cc-130">Přihlaste se k aplikaci pomocí účet Microsoft.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-130">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="3f8cc-131">Vyžádá přístupové tokeny pro rozhraní API Microsoftu.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-131">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="3f8cc-132">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="3f8cc-132">For more information, see:</span></span>
  * [<span data-ttu-id="3f8cc-133">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="3f8cc-133">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="3f8cc-134">[Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)</span><span class="sxs-lookup"><span data-stu-id="3f8cc-134">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="3f8cc-135">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="3f8cc-135">Authentication package</span></span>

<span data-ttu-id="3f8cc-136">Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty ( `SingleOrg` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="3f8cc-136">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="3f8cc-137">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-137">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3f8cc-138">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="3f8cc-138">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="3f8cc-139">`Microsoft.Authentication.WebAssembly.Msal`Balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication` do této aplikace přidá balíček.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="3f8cc-140">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="3f8cc-140">Authentication service support</span></span>

<span data-ttu-id="3f8cc-141">Podpora ověřování uživatelů je registrovaná v kontejneru služby s `AddMsalAuthentication` metodou rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="3f8cc-142">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s Identity poskytovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="3f8cc-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="3f8cc-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3f8cc-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="3f8cc-144">`AddMsalAuthentication`Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="3f8cc-145">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-145">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="3f8cc-146">Konfigurace je dodána souborem *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="3f8cc-146">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="3f8cc-147">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3f8cc-147">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="3f8cc-148">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="3f8cc-148">Access token scopes</span></span>

<span data-ttu-id="3f8cc-149">BlazorŠablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3f8cc-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="3f8cc-150">Pokud chcete jako součást toku přihlášení zřídit přístupový token, přidejte obor do výchozích oborů přístupového tokenu `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="3f8cc-150">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="3f8cc-151">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="3f8cc-151">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="3f8cc-152">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="3f8cc-152">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="3f8cc-153">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="3f8cc-153">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="3f8cc-154">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="3f8cc-154">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="3f8cc-155">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="3f8cc-155">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="3f8cc-156">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="3f8cc-156">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="3f8cc-157">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="3f8cc-157">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="3f8cc-158">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="3f8cc-158">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="3f8cc-159">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="3f8cc-159">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3f8cc-160">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3f8cc-160">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="3f8cc-161">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="3f8cc-161">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="3f8cc-162">Rychlý Start: registrace aplikace s platformou Microsoft identity</span><span class="sxs-lookup"><span data-stu-id="3f8cc-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="3f8cc-163">Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API</span><span class="sxs-lookup"><span data-stu-id="3f8cc-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
