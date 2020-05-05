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
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>Další Blazor scénáře zabezpečení ASP.NET Core serveru

[Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Předávání tokenů Blazor serverové aplikaci

Tokeny dostupné mimo Razor součásti v Blazor serverové aplikaci je možné předat komponentám s přístupem popsaným v této části. Vzorový kód, včetně kompletního `Startup.ConfigureServices` příkladu, najdete v tématu [předávání tokenů do aplikace na straně Blazor serveru](https://github.com/javiercn/blazor-server-aad-sample).

Ověřte aplikaci Blazor serveru stejně, jako byste použili běžné Razor stránky nebo aplikace MVC. Zajistěte a uložte tokeny do souboru cookie pro ověřování. Příklad:

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

Definujte třídu, která bude předána počátečnímu stavu aplikace pomocí tokenů přístupu a aktualizace:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Definujte službu **oboru** poskytovatele tokenů, kterou lze v Blazor aplikaci použít k vyřešení tokenů z [INJEKTÁŽE vkládání závislostí (di)](xref:blazor/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

V `Startup.ConfigureServices`nástroji přidejte služby pro:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

V souboru *_Host. cshtml* vytvořte a instanci `InitialApplicationState` a předejte ji jako parametr do aplikace:

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

V `App` součásti (*App. Razor*) vyřešte službu a inicializujte ji s daty z parametru:

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

V rámci služby, která vytváří požadavek na zabezpečený rozhraní API, zajistěte vložení poskytovatele tokenu a načtěte token pro volání rozhraní API:

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
