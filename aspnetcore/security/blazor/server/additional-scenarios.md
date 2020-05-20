---
title: BlazorDalší scénáře zabezpečení ASP.NET Core serveru
author: guardrex
description: Přečtěte si, jak nakonfigurovat Blazor Server pro další scénáře zabezpečení.
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
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 9d26cde4d8964a8285241bb0158d8e6f8d5f8dbc
ms.sourcegitcommit: 16b3abec1ed70f9a206f0cfa7cf6404eebaf693d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2020
ms.locfileid: "83444071"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="73e5b-103">BlazorDalší scénáře zabezpečení ASP.NET Core serveru</span><span class="sxs-lookup"><span data-stu-id="73e5b-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="73e5b-104">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="73e5b-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="73e5b-105">Předávání tokenů Blazor serverové aplikaci</span><span class="sxs-lookup"><span data-stu-id="73e5b-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="73e5b-106">Tokeny dostupné mimo Razor součásti v Blazor serverové aplikaci je možné předat komponentám s přístupem popsaným v této části.</span><span class="sxs-lookup"><span data-stu-id="73e5b-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="73e5b-107">Vzorový kód, včetně kompletního `Startup.ConfigureServices` příkladu, najdete v tématu [předávání tokenů do Blazor aplikace na straně serveru](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="73e5b-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="73e5b-108">Ověřte Blazor aplikaci serveru stejně, jako byste použili běžné Razor stránky nebo aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="73e5b-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="73e5b-109">Zajistěte a uložte tokeny do souboru cookie pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="73e5b-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="73e5b-110">Například:</span><span class="sxs-lookup"><span data-stu-id="73e5b-110">For example:</span></span>

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

<span data-ttu-id="73e5b-111">Definujte třídu, která bude předána počátečnímu stavu aplikace pomocí tokenů přístupu a aktualizace:</span><span class="sxs-lookup"><span data-stu-id="73e5b-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="73e5b-112">Definujte službu **oboru** poskytovatele tokenů, kterou lze v Blazor aplikaci použít k vyřešení tokenů z [Injektáže vkládání závislostí (di)](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="73e5b-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="73e5b-113">V nástroji `Startup.ConfigureServices` přidejte služby pro:</span><span class="sxs-lookup"><span data-stu-id="73e5b-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="73e5b-114">V souboru *_Host. cshtml* vytvořte a instanci `InitialApplicationState` a předejte ji jako parametr do aplikace:</span><span class="sxs-lookup"><span data-stu-id="73e5b-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="73e5b-115">V `App` součásti (*App. Razor*) vyřešte službu a inicializujte ji s daty z parametru:</span><span class="sxs-lookup"><span data-stu-id="73e5b-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="73e5b-116">V rámci služby, která vytváří požadavek na zabezpečený rozhraní API, zajistěte vložení poskytovatele tokenu a načtěte token pro volání rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="73e5b-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="73e5b-117">Použití koncových bodů Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="73e5b-117">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="73e5b-118">Knihovna a šablony ověřování Blazor používají koncové body Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="73e5b-118">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="73e5b-119">Pokud chcete použít koncový bod v 2.0, nakonfigurujte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> možnost v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="73e5b-119">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="73e5b-120">Případně může být nastavení provedeno v souboru nastavení aplikace (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="73e5b-120">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="73e5b-121">Pokud se označení segmentu pro autoritu nehodí pro poskytovatele OIDC aplikace, jako je třeba u jiných poskytovatelů než AAD, nastavte `Authority` vlastnost přímo.</span><span class="sxs-lookup"><span data-stu-id="73e5b-121">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="73e5b-122">Buď nastavte vlastnost v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> nebo v souboru nastavení aplikace s `Authority` klíčem.</span><span class="sxs-lookup"><span data-stu-id="73e5b-122">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the `Authority` key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="73e5b-123">Změny kódu</span><span class="sxs-lookup"><span data-stu-id="73e5b-123">Code changes</span></span>

* <span data-ttu-id="73e5b-124">Seznam deklarací identity v tokenu ID se mění pro koncové body verze 2.0.</span><span class="sxs-lookup"><span data-stu-id="73e5b-124">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="73e5b-125">Další informace najdete v tématu [Proč aktualizace pro Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="73e5b-125">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="73e5b-126">v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="73e5b-126">in the Azure documentation.</span></span>
* <span data-ttu-id="73e5b-127">Vzhledem k tomu, že jsou prostředky zadány v oboru identifikátorů URI pro koncové body verze 2.0, odeberte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> nastavení vlastnosti v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="73e5b-127">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

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

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="73e5b-128">Identifikátor URI ID aplikace můžete najít v popisu registrace aplikace poskytovatele OIDC.</span><span class="sxs-lookup"><span data-stu-id="73e5b-128">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
