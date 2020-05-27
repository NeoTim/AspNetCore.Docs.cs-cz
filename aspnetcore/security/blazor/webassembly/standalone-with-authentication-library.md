---
<span data-ttu-id="1f628-101">title: ' zabezpečení Blazor samostatné aplikace ASP.NET Core WebAssembly s knihovnou ověřování ' Autor: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1f628-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1f628-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1f628-102">'Blazor'</span></span>
- <span data-ttu-id="1f628-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1f628-103">'Identity'</span></span>
- <span data-ttu-id="1f628-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1f628-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1f628-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1f628-105">'Razor'</span></span>
- <span data-ttu-id="1f628-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="1f628-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="1f628-107">Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí knihovny ověřování</span><span class="sxs-lookup"><span data-stu-id="1f628-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="1f628-108">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1f628-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1f628-109">*V případě Azure Active Directory (AAD) a Azure Active Directory B2C (AAD B2C) nepostupujte podle pokynů v tomto tématu. Podívejte se na témata AAD a AAD B2C v tomto uzlu obsah.*</span><span class="sxs-lookup"><span data-stu-id="1f628-109">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="1f628-110">Chcete-li vytvořit Blazor samostatnou aplikaci WebAssembly, která používá knihovnu [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) Library, spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1f628-110">To create a Blazor WebAssembly standalone app that uses [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="1f628-111">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="1f628-111">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1f628-112">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="1f628-112">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="1f628-113">V aplikaci Visual Studio [vytvořte Blazor aplikaci WebAssembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="1f628-113">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="1f628-114">Nastavte **ověřování** na **jednotlivé uživatelské účty** s možností **Uložit uživatelské účty do aplikace** .</span><span class="sxs-lookup"><span data-stu-id="1f628-114">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="1f628-115">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="1f628-115">Authentication package</span></span>

<span data-ttu-id="1f628-116">Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty, aplikace automaticky obdrží odkaz na balíček pro balíček [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f628-116">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="1f628-117">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1f628-117">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1f628-118">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="1f628-118">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="1f628-119">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="1f628-119">Authentication service support</span></span>

<span data-ttu-id="1f628-120">Podpora ověřování uživatelů je zaregistrovaná v kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> metodou rozšíření poskytovanou balíčkem [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="1f628-120">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="1f628-121">Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="1f628-121">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1f628-122">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f628-122">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="1f628-123">Konfigurace je dodána souborem *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1f628-123">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="1f628-124">Podpora ověřování pro samostatné aplikace se nabízí pomocí Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="1f628-124">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="1f628-125"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace pomocí OIDC.</span><span class="sxs-lookup"><span data-stu-id="1f628-125">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="1f628-126">Hodnoty požadované pro konfiguraci aplikace lze získat z IP adresy kompatibilní s OIDC.</span><span class="sxs-lookup"><span data-stu-id="1f628-126">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="1f628-127">Získejte hodnoty při registraci aplikace, ke kterým obvykle dochází na online portálu.</span><span class="sxs-lookup"><span data-stu-id="1f628-127">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="1f628-128">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="1f628-128">Access token scopes</span></span>

<span data-ttu-id="1f628-129">BlazorŠablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1f628-129">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="1f628-130">Pro zřízení přístupového tokenu v rámci procesu přihlašování přidejte obor do výchozího oboru tokenu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="1f628-130">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="1f628-131">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="1f628-131">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="1f628-132">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="1f628-132">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="1f628-133">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="1f628-133">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="1f628-134">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="1f628-134">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="1f628-135">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="1f628-135">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="1f628-136">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="1f628-136">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="1f628-137">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="1f628-137">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="1f628-138">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="1f628-138">LoginDisplay component</span></span>

<span data-ttu-id="1f628-139">`LoginDisplay`Součást (*Shared/LoginDisplay. Razor*) je vykreslena ve `MainLayout` komponentě (*Shared/MainLayout. Razor*) a spravuje následující chování:</span><span class="sxs-lookup"><span data-stu-id="1f628-139">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="1f628-140">Pro ověřené uživatele:</span><span class="sxs-lookup"><span data-stu-id="1f628-140">For authenticated users:</span></span>
  * <span data-ttu-id="1f628-141">Zobrazí aktuální uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="1f628-141">Displays the current username.</span></span>
  * <span data-ttu-id="1f628-142">Nabízí tlačítko pro odhlášení od aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f628-142">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="1f628-143">Pro anonymní uživatele nabízí možnost přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="1f628-143">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="1f628-144">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="1f628-144">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1f628-145">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1f628-145">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="1f628-146">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="1f628-146">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
