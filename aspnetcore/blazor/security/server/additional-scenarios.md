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
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor Server Další scénáře zabezpečení

[Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Předání tokenů do Blazor Server aplikace

Tokeny, které jsou k dispozici mimo Razor součásti v Blazor Server aplikaci, mohou být předány komponentám s přístupem popsaným v této části. Vzorový kód, včetně kompletního `Startup.ConfigureServices` příkladu, najdete v tématu [předávání tokenů do Blazor aplikace na straně serveru](https://github.com/javiercn/blazor-server-aad-sample).

Ověřte Blazor Server aplikaci stejně, jako byste použili běžné Razor stránky nebo aplikace MVC. Zajistěte a uložte tokeny do souboru cookie pro ověřování. Například:

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

## <a name="set-the-authentication-scheme"></a>Nastavení schématu ověřování

Pro aplikaci, která používá více než jeden middleware ověřování a má tedy více než jedno schéma ověřování, Blazor může být používané schéma explicitně nastaveno v konfiguraci koncového bodu systému `Startup.Configure` . Následující příklad nastaví Azure Active Directory schéma:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>Použití koncových bodů Open ID Connect (OIDC) v 2.0

Knihovna a šablony ověřování Blazor používají koncové body Open ID Connect (OIDC) v 1.0. Pokud chcete použít koncový bod v 2.0, nakonfigurujte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> možnost v <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :

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

Identifikátor URI ID aplikace můžete najít v popisu registrace aplikace poskytovatele OIDC.
