---
<span data-ttu-id="2ccf5-101">title: ' zabezpečení Blazor samostatné aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory ' Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2ccf5-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2ccf5-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2ccf5-102">'Blazor'</span></span>
- <span data-ttu-id="2ccf5-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2ccf5-103">'Identity'</span></span>
- <span data-ttu-id="2ccf5-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2ccf5-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2ccf5-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2ccf5-105">'Razor'</span></span>
- <span data-ttu-id="2ccf5-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2ccf5-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="2ccf5-107">Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="2ccf5-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="2ccf5-108">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2ccf5-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2ccf5-109">Vytvoření Blazor samostatné aplikace WebAssembly, která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) :</span><span class="sxs-lookup"><span data-stu-id="2ccf5-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="2ccf5-110">[Vytvoření TENANTA AAD a webové aplikace](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="2ccf5-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="2ccf5-111">Zaregistrujte aplikaci AAD v **Azure Active Directory**  >  **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="2ccf5-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="2ccf5-112">Zadejte **název** aplikace (například \*\* Blazor samostatná AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="2ccf5-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="2ccf5-113">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="2ccf5-114">**Účty v tomto organizačním adresáři** můžete vybrat jenom pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="2ccf5-115">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="2ccf5-115">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="2ccf5-116">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="2ccf5-117">Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="2ccf5-118">Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve vlastnostech aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="2ccf5-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="2ccf5-119">Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="2ccf5-120">Po přeznačení se objeví dál v tomto tématu, které připomínat IIS Express uživatelům aktualizovat identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="2ccf5-121">Zakažte **oprávnění**  >  **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="2ccf5-121">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="2ccf5-122">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-122">Select **Register**.</span></span>

<span data-ttu-id="2ccf5-123">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="2ccf5-123">Record the following information:</span></span>

* <span data-ttu-id="2ccf5-124">ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="2ccf5-124">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="2ccf5-125">ID adresáře (ID klienta) (například `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="2ccf5-125">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="2ccf5-126">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="2ccf5-126">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="2ccf5-127">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-127">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="2ccf5-128">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-128">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="2ccf5-129">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-129">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="2ccf5-130">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-130">Select the **Save** button.</span></span>

<span data-ttu-id="2ccf5-131">Vytvořte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-131">Create the app.</span></span> <span data-ttu-id="2ccf5-132">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="2ccf5-132">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="2ccf5-133">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="2ccf5-133">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="2ccf5-134">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-134">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="2ccf5-135">V Azure Portal **Authentication**  >  **Platform configurations**  >  **Web**  >  pro aplikace, které běží na serveru Kestrel s výchozími nastaveními, je pro port 5001 nakonfigurován**identifikátor URI webového přesměrování** konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="2ccf5-136">Pokud je aplikace spuštěná na náhodném IIS Expressm portu, můžete port pro aplikaci najít ve vlastnostech aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="2ccf5-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="2ccf5-137">Pokud port nebyl dříve nakonfigurovaný se známým portem aplikace, vraťte se k registraci aplikace v Azure Portal a aktualizujte identifikátor URI přesměrování pomocí správného portu.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="2ccf5-138">Po vytvoření aplikace byste měli mít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="2ccf5-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="2ccf5-139">Přihlaste se k aplikaci pomocí uživatelského účtu AAD.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="2ccf5-140">Vyžádá přístupové tokeny pro rozhraní API Microsoftu.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="2ccf5-141">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="2ccf5-141">For more information, see:</span></span>
  * [<span data-ttu-id="2ccf5-142">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="2ccf5-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="2ccf5-143">[Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)</span><span class="sxs-lookup"><span data-stu-id="2ccf5-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="2ccf5-144">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="2ccf5-144">Authentication package</span></span>

<span data-ttu-id="2ccf5-145">Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty ( `SingleOrg` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="2ccf5-145">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="2ccf5-146">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2ccf5-147">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="2ccf5-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="2ccf5-148">`Microsoft.Authentication.WebAssembly.Msal`Balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication` do této aplikace přidá balíček.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-148">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="2ccf5-149">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="2ccf5-149">Authentication service support</span></span>

<span data-ttu-id="2ccf5-150">Podpora ověřování uživatelů je registrovaná v kontejneru služby s `AddMsalAuthentication` metodou rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-150">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="2ccf5-151">Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="2ccf5-151">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="2ccf5-152">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ccf5-152">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="2ccf5-153">`AddMsalAuthentication`Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-153">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="2ccf5-154">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-154">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="2ccf5-155">Konfigurace je dodána souborem *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="2ccf5-155">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="2ccf5-156">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2ccf5-156">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="2ccf5-157">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="2ccf5-157">Access token scopes</span></span>

<span data-ttu-id="2ccf5-158">BlazorŠablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2ccf5-158">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="2ccf5-159">Pokud chcete jako součást toku přihlášení zřídit přístupový token, přidejte obor do výchozích oborů přístupového tokenu `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="2ccf5-159">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="2ccf5-160">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="2ccf5-160">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="2ccf5-161">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="2ccf5-161">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="2ccf5-162">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="2ccf5-162">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="2ccf5-163">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="2ccf5-163">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="2ccf5-164">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="2ccf5-164">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="2ccf5-165">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="2ccf5-165">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="2ccf5-166">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="2ccf5-166">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="2ccf5-167">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="2ccf5-167">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="2ccf5-168">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="2ccf5-168">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2ccf5-169">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2ccf5-169">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="2ccf5-170">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="2ccf5-170">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="2ccf5-171">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="2ccf5-171">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
