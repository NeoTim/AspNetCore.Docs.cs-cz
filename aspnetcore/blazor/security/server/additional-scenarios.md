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
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor Server Další scénáře zabezpečení

[Javier Calvarro Nelson](https://github.com/javiercn)

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Předání tokenů do Blazor Server aplikace</h2>

Tokeny, které jsou k dispozici mimo Razor součásti v Blazor Server aplikaci, mohou být předány komponentám s přístupem popsaným v této části.

Ověřte Blazor Server aplikaci stejně, jako byste použili běžné Razor stránky nebo aplikace MVC. Zřídí a uloží tokeny do ověřování cookie . Příklad:

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

Volitelně se přidávají další obory `options.Scope.Add("{SCOPE}");` , kde zástupný symbol `{SCOPE}` je další obor, který chcete přidat.

Definujte službu **oboru** poskytovatele tokenů, kterou lze v Blazor aplikaci použít k vyřešení tokenů z [Injektáže vkládání závislostí (di)](xref:blazor/fundamentals/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

V nástroji `Startup.ConfigureServices` přidejte služby pro:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Definujte třídu, která bude předána počátečnímu stavu aplikace pomocí tokenů přístupu a aktualizace:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

V `_Host.cshtml` souboru vytvořte instanci a `InitialApplicationState` a předejte ji jako parametr do aplikace:

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

V `App` součásti ( `App.razor` ) vyřešte službu a inicializujte ji s daty z parametru:

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

Přidejte odkaz na balíček do aplikace pro [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) balíček NuGet.

Ve službě, která vytváří požadavek na zabezpečený rozhraní API, zajistěte vložení poskytovatele tokenu a načtěte token pro požadavek rozhraní API:

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

<h2 id="set-the-authentication-scheme">Nastavení schématu ověřování</h2>

Pro aplikaci, která používá více než jeden middleware ověřování a má tedy více než jedno schéma ověřování, Blazor může být používané schéma explicitně nastaveno v konfiguraci koncového bodu systému `Startup.Configure` . Následující příklad nastaví Azure Active Directory schéma:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Předání tokenů do Blazor Server aplikace</h2>

Tokeny, které jsou k dispozici mimo Razor součásti v Blazor Server aplikaci, mohou být předány komponentám s přístupem popsaným v této části.

Ověřte Blazor Server aplikaci stejně, jako byste použili běžné Razor stránky nebo aplikace MVC. Zřídí a uloží tokeny do ověřování cookie . Příklad:

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

Volitelně se přidávají další obory `options.Scope.Add("{SCOPE}");` , kde zástupný symbol `{SCOPE}` je další obor, který chcete přidat.

Volitelně je prostředek určen pomocí `options.Resource = "{RESOURCE}";` , kde zástupný symbol `{RESOURCE}` je prostředek. Příklad:

```csharp
options.Resource = "https://graph.microsoft.com";
```

Definujte třídu, která bude předána počátečnímu stavu aplikace pomocí tokenů přístupu a aktualizace:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Definujte službu **oboru** poskytovatele tokenů, kterou lze v Blazor aplikaci použít k vyřešení tokenů z [Injektáže vkládání závislostí (di)](xref:blazor/fundamentals/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

V nástroji `Startup.ConfigureServices` přidejte služby pro:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

V `_Host.cshtml` souboru vytvořte instanci a `InitialApplicationState` a předejte ji jako parametr do aplikace:

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

V `App` součásti ( `App.razor` ) vyřešte službu a inicializujte ji s daty z parametru:

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

Přidejte odkaz na balíček do aplikace pro [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) balíček NuGet.

Ve službě, která vytváří požadavek na zabezpečený rozhraní API, zajistěte vložení poskytovatele tokenu a načtěte token pro požadavek rozhraní API:

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

<h2 id="set-the-authentication-scheme">Nastavení schématu ověřování</h2>

Pro aplikaci, která používá více než jeden middleware ověřování a má tedy více než jedno schéma ověřování, Blazor může být používané schéma explicitně nastaveno v konfiguraci koncového bodu systému `Startup.Configure` . Následující příklad nastaví Azure Active Directory schéma:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Použití koncových bodů OpenID Connect (OIDC) v 2.0

Ve verzích ASP.NET Core starších než 5,0 používá knihovna a Blazor šablony ověřování koncové body OpenID Connect (OIDC) v 1.0. Chcete-li použít koncový bod v 2.0 s verzemi ASP.NET Core před 5,0, nakonfigurujte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> možnost v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Případně může být nastavení provedeno v souboru nastavení aplikace ( `appsettings.json` ):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Pokud se označení segmentu pro autoritu nehodí pro poskytovatele OIDC aplikace, jako je třeba u jiných poskytovatelů než AAD, nastavte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> vlastnost přímo. Buď nastavte vlastnost v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> nebo v souboru nastavení aplikace s <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> klíčem.

### <a name="code-changes"></a>Změny kódu

* Seznam deklarací identity v tokenu ID se mění pro koncové body verze 2.0. Další informace najdete v tématu [Proč aktualizace pro Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) v dokumentaci k Azure.
* Vzhledem k tomu, že jsou prostředky zadány v oboru identifikátorů URI pro koncové body verze 2.0, odeberte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> nastavení vlastnosti v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  Další informace najdete v tématu [obory, nikoli prostředky](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) v dokumentaci k Azure.

### <a name="app-id-uri"></a>Identifikátor URI ID aplikace

* Při použití koncových bodů v 2.0 definuje rozhraní API *`App ID URI`* , což představuje jedinečný identifikátor rozhraní API.
* Všechny obory obsahují identifikátor URI ID aplikace jako předponu a koncové body v 2.0 generují přístupové tokeny s identifikátorem URI ID aplikace jako cílovou skupinu.
* Při použití koncových bodů V 2.0 se ID klienta nakonfigurované v rozhraní API serveru změní z ID aplikace API (ID klienta) na identifikátor URI ID aplikace.

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

Identifikátor URI ID aplikace můžete najít v popisu registrace aplikace poskytovatele OIDC.

::: moniker-end
