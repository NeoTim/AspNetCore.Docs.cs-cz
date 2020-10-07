---
title: ASP.NET Core Blazor Server Další scénáře zabezpečení
author: guardrex
description: Naučte se konfigurovat Blazor Server pro další scénáře zabezpečení.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 89288f3fce2dbb6f2647693ba8aaf29500b5bb2b
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805489"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="58d77-103">ASP.NET Core Blazor Server Další scénáře zabezpečení</span><span class="sxs-lookup"><span data-stu-id="58d77-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="58d77-104">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="58d77-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="58d77-105">Předání tokenů do Blazor Server aplikace</span><span class="sxs-lookup"><span data-stu-id="58d77-105">Pass tokens to a Blazor Server app</span></span></h2>

<span data-ttu-id="58d77-106">Tokeny, které jsou k dispozici mimo Razor součásti v Blazor Server aplikaci, mohou být předány komponentám s přístupem popsaným v této části.</span><span class="sxs-lookup"><span data-stu-id="58d77-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="58d77-107">Ověřte Blazor Server aplikaci stejně, jako byste použili běžné Razor stránky nebo aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="58d77-107">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="58d77-108">Zřídí a uloží tokeny do ověřování cookie .</span><span class="sxs-lookup"><span data-stu-id="58d77-108">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="58d77-109">Příklad:</span><span class="sxs-lookup"><span data-stu-id="58d77-109">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="58d77-110">Volitelně se přidávají další obory `options.Scope.Add("{SCOPE}");` , kde zástupný symbol `{SCOPE}` je další obor, který chcete přidat.</span><span class="sxs-lookup"><span data-stu-id="58d77-110">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="58d77-111">Definujte službu **oboru** poskytovatele tokenů, kterou lze v Blazor aplikaci použít k vyřešení tokenů z [Injektáže vkládání závislostí (di)](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="58d77-111">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="58d77-112">V nástroji `Startup.ConfigureServices` přidejte služby pro:</span><span class="sxs-lookup"><span data-stu-id="58d77-112">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="58d77-113">Definujte třídu, která bude předána počátečnímu stavu aplikace pomocí tokenů přístupu a aktualizace:</span><span class="sxs-lookup"><span data-stu-id="58d77-113">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="58d77-114">V `_Host.cshtml` souboru vytvořte instanci a `InitialApplicationState` a předejte ji jako parametr do aplikace:</span><span class="sxs-lookup"><span data-stu-id="58d77-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

<span data-ttu-id="58d77-115">V `App` součásti ( `App.razor` ) vyřešte službu a inicializujte ji s daty z parametru:</span><span class="sxs-lookup"><span data-stu-id="58d77-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="58d77-116">Přidejte odkaz na balíček do aplikace pro [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="58d77-116">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="58d77-117">Ve službě, která vytváří požadavek na zabezpečený rozhraní API, zajistěte vložení poskytovatele tokenu a načtěte token pro požadavek rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="58d77-117">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="58d77-118">Nastavení schématu ověřování</span><span class="sxs-lookup"><span data-stu-id="58d77-118">Set the authentication scheme</span></span></h2>

<span data-ttu-id="58d77-119">Pro aplikaci, která používá více než jeden middleware ověřování a má tedy více než jedno schéma ověřování, Blazor může být používané schéma explicitně nastaveno v konfiguraci koncového bodu systému `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="58d77-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="58d77-120">Následující příklad nastaví Azure Active Directory schéma:</span><span class="sxs-lookup"><span data-stu-id="58d77-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="58d77-121">Předání tokenů do Blazor Server aplikace</span><span class="sxs-lookup"><span data-stu-id="58d77-121">Pass tokens to a Blazor Server app</span></span></h2>

<span data-ttu-id="58d77-122">Tokeny, které jsou k dispozici mimo Razor součásti v Blazor Server aplikaci, mohou být předány komponentám s přístupem popsaným v této části.</span><span class="sxs-lookup"><span data-stu-id="58d77-122">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="58d77-123">Ověřte Blazor Server aplikaci stejně, jako byste použili běžné Razor stránky nebo aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="58d77-123">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="58d77-124">Zřídí a uloží tokeny do ověřování cookie .</span><span class="sxs-lookup"><span data-stu-id="58d77-124">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="58d77-125">Příklad:</span><span class="sxs-lookup"><span data-stu-id="58d77-125">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="58d77-126">Volitelně se přidávají další obory `options.Scope.Add("{SCOPE}");` , kde zástupný symbol `{SCOPE}` je další obor, který chcete přidat.</span><span class="sxs-lookup"><span data-stu-id="58d77-126">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="58d77-127">Volitelně je prostředek určen pomocí `options.Resource = "{RESOURCE}";` , kde zástupný symbol `{RESOURCE}` je prostředek.</span><span class="sxs-lookup"><span data-stu-id="58d77-127">Optionally, the resource is specified with `options.Resource = "{RESOURCE}";`, where the placeholder `{RESOURCE}` is the resource.</span></span> <span data-ttu-id="58d77-128">Příklad:</span><span class="sxs-lookup"><span data-stu-id="58d77-128">For example:</span></span>

```csharp
options.Resource = "https://graph.microsoft.com";
```

<span data-ttu-id="58d77-129">Definujte třídu, která bude předána počátečnímu stavu aplikace pomocí tokenů přístupu a aktualizace:</span><span class="sxs-lookup"><span data-stu-id="58d77-129">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="58d77-130">Definujte službu **oboru** poskytovatele tokenů, kterou lze v Blazor aplikaci použít k vyřešení tokenů z [Injektáže vkládání závislostí (di)](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="58d77-130">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="58d77-131">V nástroji `Startup.ConfigureServices` přidejte služby pro:</span><span class="sxs-lookup"><span data-stu-id="58d77-131">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="58d77-132">V `_Host.cshtml` souboru vytvořte instanci a `InitialApplicationState` a předejte ji jako parametr do aplikace:</span><span class="sxs-lookup"><span data-stu-id="58d77-132">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="58d77-133">V `App` součásti ( `App.razor` ) vyřešte službu a inicializujte ji s daty z parametru:</span><span class="sxs-lookup"><span data-stu-id="58d77-133">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="58d77-134">Přidejte odkaz na balíček do aplikace pro [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="58d77-134">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="58d77-135">Ve službě, která vytváří požadavek na zabezpečený rozhraní API, zajistěte vložení poskytovatele tokenu a načtěte token pro požadavek rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="58d77-135">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="58d77-136">Nastavení schématu ověřování</span><span class="sxs-lookup"><span data-stu-id="58d77-136">Set the authentication scheme</span></span></h2>

<span data-ttu-id="58d77-137">Pro aplikaci, která používá více než jeden middleware ověřování a má tedy více než jedno schéma ověřování, Blazor může být používané schéma explicitně nastaveno v konfiguraci koncového bodu systému `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="58d77-137">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="58d77-138">Následující příklad nastaví Azure Active Directory schéma:</span><span class="sxs-lookup"><span data-stu-id="58d77-138">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="58d77-139">Použití koncových bodů OpenID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="58d77-139">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="58d77-140">Ve verzích ASP.NET Core starších než 5,0 používá knihovna a Blazor šablony ověřování koncové body OpenID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="58d77-140">In versions of ASP.NET Core prior to 5.0, the authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="58d77-141">Chcete-li použít koncový bod v 2.0 s verzemi ASP.NET Core před 5,0, nakonfigurujte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> možnost v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="58d77-141">To use a v2.0 endpoint with versions of ASP.NET Core prior to 5.0, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="58d77-142">Případně může být nastavení provedeno v souboru nastavení aplikace ( `appsettings.json` ):</span><span class="sxs-lookup"><span data-stu-id="58d77-142">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="58d77-143">Pokud se označení segmentu pro autoritu nehodí pro poskytovatele OIDC aplikace, jako je třeba u jiných poskytovatelů než AAD, nastavte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> vlastnost přímo.</span><span class="sxs-lookup"><span data-stu-id="58d77-143">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="58d77-144">Buď nastavte vlastnost v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> nebo v souboru nastavení aplikace s <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> klíčem.</span><span class="sxs-lookup"><span data-stu-id="58d77-144">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="58d77-145">Změny kódu</span><span class="sxs-lookup"><span data-stu-id="58d77-145">Code changes</span></span>

* <span data-ttu-id="58d77-146">Seznam deklarací identity v tokenu ID se mění pro koncové body verze 2.0.</span><span class="sxs-lookup"><span data-stu-id="58d77-146">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="58d77-147">Další informace najdete v tématu [Proč aktualizace pro Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="58d77-147">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="58d77-148">v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="58d77-148">in the Azure documentation.</span></span>
* <span data-ttu-id="58d77-149">Vzhledem k tomu, že jsou prostředky zadány v oboru identifikátorů URI pro koncové body verze 2.0, odeberte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> nastavení vlastnosti v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="58d77-149">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="58d77-150">Další informace najdete v tématu [obory, nikoli prostředky](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="58d77-150">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="58d77-151">Identifikátor URI ID aplikace</span><span class="sxs-lookup"><span data-stu-id="58d77-151">App ID URI</span></span>

* <span data-ttu-id="58d77-152">Při použití koncových bodů v 2.0 definuje rozhraní API *`App ID URI`* , což představuje jedinečný identifikátor rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="58d77-152">When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="58d77-153">Všechny obory obsahují identifikátor URI ID aplikace jako předponu a koncové body v 2.0 generují přístupové tokeny s identifikátorem URI ID aplikace jako cílovou skupinu.</span><span class="sxs-lookup"><span data-stu-id="58d77-153">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="58d77-154">Při použití koncových bodů V 2.0 se ID klienta nakonfigurované v rozhraní API serveru změní z ID aplikace API (ID klienta) na identifikátor URI ID aplikace.</span><span class="sxs-lookup"><span data-stu-id="58d77-154">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="58d77-155">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="58d77-155">`appsettings.json`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="58d77-156">Identifikátor URI ID aplikace můžete najít v popisu registrace aplikace poskytovatele OIDC.</span><span class="sxs-lookup"><span data-stu-id="58d77-156">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>

::: moniker-end
