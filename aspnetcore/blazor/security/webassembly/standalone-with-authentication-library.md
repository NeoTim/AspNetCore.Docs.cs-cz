---
title: Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí knihovny ověřování
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 5a05543c77f1ebaebadc27236aa8f7634e84f1fd
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243408"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="ff887-102">Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí knihovny ověřování</span><span class="sxs-lookup"><span data-stu-id="ff887-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="ff887-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ff887-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ff887-104">*V případě Azure Active Directory (AAD) a Azure Active Directory B2C (AAD B2C) nepostupujte podle pokynů v tomto tématu. Podívejte se na témata AAD a AAD B2C v tomto uzlu obsah.*</span><span class="sxs-lookup"><span data-stu-id="ff887-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="ff887-105">Chcete-li vytvořit Blazor samostatnou aplikaci WebAssembly, která používá [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) knihovnu, spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ff887-105">To create a Blazor WebAssembly standalone app that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="ff887-106">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="ff887-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="ff887-107">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="ff887-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="ff887-108">V aplikaci Visual Studio [vytvořte Blazor aplikaci WebAssembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="ff887-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="ff887-109">Nastavte **ověřování** na **jednotlivé uživatelské účty** s možností **Uložit uživatelské účty do aplikace** .</span><span class="sxs-lookup"><span data-stu-id="ff887-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="ff887-110">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="ff887-110">Authentication package</span></span>

<span data-ttu-id="ff887-111">Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty, aplikace automaticky obdrží odkaz na balíček [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="ff887-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="ff887-112">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="ff887-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ff887-113">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="ff887-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="ff887-114">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="ff887-114">Authentication service support</span></span>

<span data-ttu-id="ff887-115">Podpora ověřování uživatelů je registrovaná v kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> metodou rozšíření poskytovanou [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) balíčkem.</span><span class="sxs-lookup"><span data-stu-id="ff887-115">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="ff887-116">Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="ff887-116">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="ff887-117">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="ff887-117">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="ff887-118">Soubor zadal konfiguraci `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="ff887-118">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="ff887-119">Podpora ověřování pro samostatné aplikace se nabízí pomocí Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="ff887-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="ff887-120"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace pomocí OIDC.</span><span class="sxs-lookup"><span data-stu-id="ff887-120">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="ff887-121">Hodnoty požadované pro konfiguraci aplikace lze získat z IP adresy kompatibilní s OIDC.</span><span class="sxs-lookup"><span data-stu-id="ff887-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="ff887-122">Získejte hodnoty při registraci aplikace, ke kterým obvykle dochází na online portálu.</span><span class="sxs-lookup"><span data-stu-id="ff887-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="ff887-123">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="ff887-123">Access token scopes</span></span>

<span data-ttu-id="ff887-124">BlazorŠablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="ff887-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="ff887-125">Pro zřízení přístupového tokenu v rámci procesu přihlašování přidejte obor do výchozího oboru tokenu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="ff887-125">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="ff887-126">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="ff887-126">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="ff887-127">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="ff887-127">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="ff887-128">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="ff887-128">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="ff887-129">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="ff887-129">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="ff887-130">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="ff887-130">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="ff887-131">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="ff887-131">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="ff887-132">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="ff887-132">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="ff887-133">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="ff887-133">LoginDisplay component</span></span>

<span data-ttu-id="ff887-134">`LoginDisplay`Součást ( `Shared/LoginDisplay.razor` ) je vykreslena v `MainLayout` komponentě ( `Shared/MainLayout.razor` ) a spravuje následující chování:</span><span class="sxs-lookup"><span data-stu-id="ff887-134">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="ff887-135">Pro ověřené uživatele:</span><span class="sxs-lookup"><span data-stu-id="ff887-135">For authenticated users:</span></span>
  * <span data-ttu-id="ff887-136">Zobrazí aktuální uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="ff887-136">Displays the current username.</span></span>
  * <span data-ttu-id="ff887-137">Nabízí tlačítko pro odhlášení od aplikace.</span><span class="sxs-lookup"><span data-stu-id="ff887-137">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="ff887-138">Pro anonymní uživatele nabízí možnost přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="ff887-138">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="ff887-139">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="ff887-139">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ff887-140">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ff887-140">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="ff887-141">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="ff887-141">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
