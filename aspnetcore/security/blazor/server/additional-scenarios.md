---
title: Další Blazor scénáře zabezpečení ASP.NET Core serveru
author: guardrex
description: Přečtěte si, Blazor jak nakonfigurovat server pro další scénáře zabezpečení.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 95e9e57889fdbb5270f895874c9b8148ae4ca48d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772801"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="bb6ab-103">Další Blazor scénáře zabezpečení ASP.NET Core serveru</span><span class="sxs-lookup"><span data-stu-id="bb6ab-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="bb6ab-104">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="bb6ab-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="bb6ab-105">Předávání tokenů Blazor serverové aplikaci</span><span class="sxs-lookup"><span data-stu-id="bb6ab-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="bb6ab-106">Tokeny dostupné mimo Razor součásti v Blazor serverové aplikaci je možné předat komponentám s přístupem popsaným v této části.</span><span class="sxs-lookup"><span data-stu-id="bb6ab-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="bb6ab-107">Vzorový kód, včetně kompletního `Startup.ConfigureServices` příkladu, najdete v tématu [předávání tokenů do aplikace na straně Blazor serveru](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="bb6ab-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="bb6ab-108">Ověřte aplikaci Blazor serveru stejně, jako byste použili běžné Razor stránky nebo aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="bb6ab-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="bb6ab-109">Zajistěte a uložte tokeny do souboru cookie pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="bb6ab-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="bb6ab-110">Příklad:</span><span class="sxs-lookup"><span data-stu-id="bb6ab-110">For example:</span></span>

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

<span data-ttu-id="bb6ab-111">Definujte třídu, která bude předána počátečnímu stavu aplikace pomocí tokenů přístupu a aktualizace:</span><span class="sxs-lookup"><span data-stu-id="bb6ab-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="bb6ab-112">Definujte službu **oboru** poskytovatele tokenů, kterou lze v Blazor aplikaci použít k vyřešení tokenů z [INJEKTÁŽE vkládání závislostí (di)](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="bb6ab-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="bb6ab-113">V `Startup.ConfigureServices`nástroji přidejte služby pro:</span><span class="sxs-lookup"><span data-stu-id="bb6ab-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="bb6ab-114">V souboru *_Host. cshtml* vytvořte a instanci `InitialApplicationState` a předejte ji jako parametr do aplikace:</span><span class="sxs-lookup"><span data-stu-id="bb6ab-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="bb6ab-115">V `App` součásti (*App. Razor*) vyřešte službu a inicializujte ji s daty z parametru:</span><span class="sxs-lookup"><span data-stu-id="bb6ab-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="bb6ab-116">V rámci služby, která vytváří požadavek na zabezpečený rozhraní API, zajistěte vložení poskytovatele tokenu a načtěte token pro volání rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="bb6ab-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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
