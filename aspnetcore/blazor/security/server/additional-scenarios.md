---
title: ASP.NET Core Blazor Server Další scénáře zabezpečení
author: guardrex
description: Naučte se konfigurovat Blazor Server pro další scénáře zabezpečení.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 46de9a22dec540b8dfda7583b7a3c5c2dcbbc549
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402322"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="4f0f1-103">ASP.NET Core Blazor Server Další scénáře zabezpečení</span><span class="sxs-lookup"><span data-stu-id="4f0f1-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="4f0f1-104">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="4f0f1-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="4f0f1-105">Předání tokenů do Blazor Server aplikace</span><span class="sxs-lookup"><span data-stu-id="4f0f1-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="4f0f1-106">Tokeny, které jsou k dispozici mimo Razor součásti v Blazor Server aplikaci, mohou být předány komponentám s přístupem popsaným v této části.</span><span class="sxs-lookup"><span data-stu-id="4f0f1-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="4f0f1-107">Vzorový kód, včetně kompletního `Startup.ConfigureServices` příkladu, najdete v tématu [předávání tokenů do Blazor aplikace na straně serveru](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="4f0f1-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="4f0f1-108">Ověřte Blazor Server aplikaci stejně, jako byste použili běžné Razor stránky nebo aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="4f0f1-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="4f0f1-109">Zajistěte a uložte tokeny do souboru cookie pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="4f0f1-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="4f0f1-110">Například:</span><span class="sxs-lookup"><span data-stu-id="4f0f1-110">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="4f0f1-111">Definujte třídu, která bude předána počátečnímu stavu aplikace pomocí tokenů přístupu a aktualizace:</span><span class="sxs-lookup"><span data-stu-id="4f0f1-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4f0f1-112">Definujte službu **oboru** poskytovatele tokenů, kterou lze v Blazor aplikaci použít k vyřešení tokenů z [Injektáže vkládání závislostí (di)](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="4f0f1-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4f0f1-113">V nástroji `Startup.ConfigureServices` přidejte služby pro:</span><span class="sxs-lookup"><span data-stu-id="4f0f1-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="4f0f1-114">V `_Host.cshtml` souboru vytvořte instanci a `InitialApplicationState` a předejte ji jako parametr do aplikace:</span><span class="sxs-lookup"><span data-stu-id="4f0f1-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="4f0f1-115">V `App` součásti ( `App.razor` ) vyřešte službu a inicializujte ji s daty z parametru:</span><span class="sxs-lookup"><span data-stu-id="4f0f1-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="4f0f1-116">V rámci služby, která vytváří požadavek na zabezpečený rozhraní API, zajistěte vložení poskytovatele tokenu a načtěte token pro volání rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="4f0f1-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a><span data-ttu-id="4f0f1-117">Nastavení schématu ověřování</span><span class="sxs-lookup"><span data-stu-id="4f0f1-117">Set the authentication scheme</span></span>

<span data-ttu-id="4f0f1-118">Pro aplikaci, která používá více než jeden middleware ověřování a má tedy více než jedno schéma ověřování, Blazor může být používané schéma explicitně nastaveno v konfiguraci koncového bodu systému `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="4f0f1-118">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="4f0f1-119">Následující příklad nastaví Azure Active Directory schéma:</span><span class="sxs-lookup"><span data-stu-id="4f0f1-119">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="4f0f1-120">Použití koncových bodů Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="4f0f1-120">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="4f0f1-121">Knihovna a šablony ověřování Blazor používají koncové body Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="4f0f1-121">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="4f0f1-122">Pokud chcete použít koncový bod v 2.0, nakonfigurujte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> možnost v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="4f0f1-122">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="4f0f1-123">Případně může být nastavení provedeno v souboru nastavení aplikace ( `appsettings.json` ):</span><span class="sxs-lookup"><span data-stu-id="4f0f1-123">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="4f0f1-124">Pokud se označení segmentu pro autoritu nehodí pro poskytovatele OIDC aplikace, jako je třeba u jiných poskytovatelů než AAD, nastavte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> vlastnost přímo.</span><span class="sxs-lookup"><span data-stu-id="4f0f1-124">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="4f0f1-125">Buď nastavte vlastnost v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> nebo v souboru nastavení aplikace s <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> klíčem.</span><span class="sxs-lookup"><span data-stu-id="4f0f1-125">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="4f0f1-126">Změny kódu</span><span class="sxs-lookup"><span data-stu-id="4f0f1-126">Code changes</span></span>

* <span data-ttu-id="4f0f1-127">Seznam deklarací identity v tokenu ID se mění pro koncové body verze 2.0.</span><span class="sxs-lookup"><span data-stu-id="4f0f1-127">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="4f0f1-128">Další informace najdete v tématu [Proč aktualizace pro Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="4f0f1-128">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="4f0f1-129">v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="4f0f1-129">in the Azure documentation.</span></span>
* <span data-ttu-id="4f0f1-130">Vzhledem k tomu, že jsou prostředky zadány v oboru identifikátorů URI pro koncové body verze 2.0, odeberte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> nastavení vlastnosti v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="4f0f1-130">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="4f0f1-131">Identifikátor URI ID aplikace můžete najít v popisu registrace aplikace poskytovatele OIDC.</span><span class="sxs-lookup"><span data-stu-id="4f0f1-131">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>