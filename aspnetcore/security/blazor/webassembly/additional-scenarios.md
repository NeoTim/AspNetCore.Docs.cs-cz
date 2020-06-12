---
title: ASP.NET Core Blazor Další scénáře zabezpečení pro WebAssembly
author: guardrex
description: Přečtěte si, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 35038cb7b96afd7c009f1210251e38273aa4aad8
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679654"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="483de-103">ASP.NET Core Blazor Další scénáře zabezpečení pro WebAssembly</span><span class="sxs-lookup"><span data-stu-id="483de-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="483de-104">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="483de-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="483de-105">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="483de-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="483de-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Službu lze použít s nástrojem <xref:System.Net.Http.HttpClient> k připojení přístupových tokenů k odchozím žádostem.</span><span class="sxs-lookup"><span data-stu-id="483de-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="483de-107">Tokeny se získávají pomocí existující <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> služby.</span><span class="sxs-lookup"><span data-stu-id="483de-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="483de-108">Pokud token nelze získat, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="483de-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="483de-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>má <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> metodu, která se dá použít k navigaci uživatele na poskytovatele identity za účelem získání nového tokenu.</span><span class="sxs-lookup"><span data-stu-id="483de-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="483de-110"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Pomocí metody lze konfigurovat pomocí autorizovaných adres URL, oborů a návratové adresy URL <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="483de-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="483de-111">Ke konfiguraci obslužné rutiny zpráv pro odchozí požadavky použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="483de-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="483de-112">[Vlastní třída AuthorizationMessageHandler](#custom-authorizationmessagehandler-class) (*doporučeno*)</span><span class="sxs-lookup"><span data-stu-id="483de-112">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="483de-113">Konfigurace AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="483de-113">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="483de-114">Vlastní třída AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="483de-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="483de-115">V následujícím příkladu vlastní třída rozšiřuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , která může být použita ke konfiguraci <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="483de-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="483de-116">V `Program.Main` (*program.cs*) <xref:System.Net.Http.HttpClient> je nakonfigurován s vlastní obslužnou rutinou ověřovací zprávy:</span><span class="sxs-lookup"><span data-stu-id="483de-116">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="483de-117">Konfigurace <xref:System.Net.Http.HttpClient> se používá k provádění autorizovaných požadavků pomocí vzoru [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="483de-117">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="483de-118">V případě, že je klient vytvořen pomocí nástroje <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (balíček[Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ), <xref:System.Net.Http.HttpClient> jsou zadány instance, které zahrnují přístupové tokeny při vytváření požadavků na rozhraní API serveru:</span><span class="sxs-lookup"><span data-stu-id="483de-118">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="483de-119">Konfigurace AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="483de-119">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="483de-120">V následujícím příkladu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> nakonfiguruje <xref:System.Net.Http.HttpClient> v `Program.Main` (*program.cs*):</span><span class="sxs-lookup"><span data-stu-id="483de-120">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="483de-121">Pro usnadnění práce <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> je součástí předem nakonfigurovaná základní adresa aplikace jako autorizovaná adresa URL.</span><span class="sxs-lookup"><span data-stu-id="483de-121">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="483de-122">Šablony WebAssembly s povoleným ověřováním Blazor teď používají <xref:System.Net.Http.IHttpClientFactory> (balíček[Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ) v projektu rozhraní API serveru pro nastavení <xref:System.Net.Http.HttpClient> pomocí <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="483de-122">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="483de-123">Tam, kde je klient vytvořen pomocí <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> v předchozím příkladu, se <xref:System.Net.Http.HttpClient> dodávají instance, které zahrnují přístupové tokeny při vytváření požadavků na serverový projekt.</span><span class="sxs-lookup"><span data-stu-id="483de-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="483de-124">Konfigurace <xref:System.Net.Http.HttpClient> se používá k provádění autorizovaných požadavků pomocí vzoru [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) :</span><span class="sxs-lookup"><span data-stu-id="483de-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="483de-125">Typové HttpClient</span><span class="sxs-lookup"><span data-stu-id="483de-125">Typed HttpClient</span></span>

<span data-ttu-id="483de-126">Je možné definovat zadaného klienta, který zpracovává všechny aspekty získání HTTP a tokenu v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="483de-126">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="483de-127">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="483de-127">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="483de-128">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="483de-128">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="483de-129">`FetchData`součást (*Pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="483de-129">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="483de-130">Konfigurace obslužné rutiny HttpClient</span><span class="sxs-lookup"><span data-stu-id="483de-130">Configure the HttpClient handler</span></span>

<span data-ttu-id="483de-131">Obslužná rutina může být dále nakonfigurována <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="483de-131">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="483de-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="483de-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="483de-133">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="483de-133">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="483de-134">Pokud Blazor aplikace WebAssembly obvykle používá zabezpečené výchozí nastavení <xref:System.Net.Http.HttpClient> , může aplikace také provést neověřené nebo neautorizované požadavky webového rozhraní API konfigurací pojmenovaného <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="483de-134">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="483de-135">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="483de-135">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="483de-136">Předchozí registrace je kromě existující zabezpečené výchozí <xref:System.Net.Http.HttpClient> registrace.</span><span class="sxs-lookup"><span data-stu-id="483de-136">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="483de-137">Komponenta vytvoří <xref:System.Net.Http.HttpClient> z <xref:System.Net.Http.IHttpClientFactory> balíčku ([Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ), aby provedl neověřené nebo neautorizované požadavky:</span><span class="sxs-lookup"><span data-stu-id="483de-137">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="483de-138">Řadič v rozhraní API serveru `WeatherForecastNoAuthenticationController` pro předchozí příklad není označený [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="483de-138">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="483de-139">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="483de-139">Request additional access tokens</span></span>

<span data-ttu-id="483de-140">Přístupové tokeny lze získat ručně voláním `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="483de-140">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="483de-141">V následujícím příkladu jsou vyžadovány další Azure Active Directory (AAD) Microsoft Graph obory rozhraní API pro čtení uživatelských dat a odesílání e-mailů.</span><span class="sxs-lookup"><span data-stu-id="483de-141">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="483de-142">Po přidání oprávnění rozhraní API Microsoft Graph na portálu Azure AAD jsou další obory nakonfigurované v klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="483de-142">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="483de-143">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="483de-143">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="483de-144">`IAccessTokenProvider.RequestToken`Metoda poskytuje přetížení, které umožňuje aplikaci zřídit přístupový token s danou sadou oborů.</span><span class="sxs-lookup"><span data-stu-id="483de-144">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="483de-145">V Razor součásti:</span><span class="sxs-lookup"><span data-stu-id="483de-145">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="483de-146"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Vrátí</span><span class="sxs-lookup"><span data-stu-id="483de-146"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="483de-147">`true`s nástrojem `token` pro použití.</span><span class="sxs-lookup"><span data-stu-id="483de-147">`true` with the `token` for use.</span></span>
* <span data-ttu-id="483de-148">`false`Pokud se token nenačte.</span><span class="sxs-lookup"><span data-stu-id="483de-148">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="483de-149">HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení</span><span class="sxs-lookup"><span data-stu-id="483de-149">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="483de-150">Při spuštění na WebAssembly v Blazor aplikaci WebAssembly [HttpClient](xref:fundamentals/http-requests) a dá se <xref:System.Net.Http.HttpRequestMessage> použít k přizpůsobení požadavků.</span><span class="sxs-lookup"><span data-stu-id="483de-150">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="483de-151">Můžete například zadat metodu HTTP a hlavičku požadavku.</span><span class="sxs-lookup"><span data-stu-id="483de-151">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="483de-152">Následující komponenta vytvoří požadavek na `POST` koncový bod rozhraní API seznamu na serveru a zobrazí tělo odpovědi:</span><span class="sxs-lookup"><span data-stu-id="483de-152">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@_responseBody</p>

@code {
    private string _responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            _responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="483de-153">Implementace rozhraní .NET WebAssembly pro <xref:System.Net.Http.HttpClient> používá [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="483de-153">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="483de-154">Načtení umožňuje nakonfigurovat několik [možností specifických pro požadavky](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="483de-154">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="483de-155">Možnosti požadavku HTTP Fetch lze konfigurovat pomocí <xref:System.Net.Http.HttpRequestMessage> rozšiřujících metod, které jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="483de-155">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="483de-156">Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="483de-156">Extension method</span></span> | <span data-ttu-id="483de-157">Načíst vlastnost žádosti</span><span class="sxs-lookup"><span data-stu-id="483de-157">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="483de-158">přihlašovací údaje</span><span class="sxs-lookup"><span data-stu-id="483de-158">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="483de-159">uchovávat</span><span class="sxs-lookup"><span data-stu-id="483de-159">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="483de-160">Mode</span><span class="sxs-lookup"><span data-stu-id="483de-160">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="483de-161">způsobilost</span><span class="sxs-lookup"><span data-stu-id="483de-161">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="483de-162">Další možnosti můžete nastavit pomocí obecnější <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="483de-162">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="483de-163">Odpověď HTTP je obvykle ukládána do vyrovnávací paměti v Blazor aplikaci WebAssembly, aby umožnila podporu pro čtení v obsahu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="483de-163">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="483de-164">Pokud chcete povolit podporu pro streamování odpovědí, použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metodu rozšíření v žádosti.</span><span class="sxs-lookup"><span data-stu-id="483de-164">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="483de-165">Pokud chcete do žádosti o více zdrojů zahrnout přihlašovací údaje, použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="483de-165">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="483de-166">Další informace o možnostech načtení rozhraní API naleznete v tématu [MDN web Docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="483de-166">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="483de-167">Při odesílání přihlašovacích údajů (souborů cookie autorizace/hlaviček) na žádostech CORS `Authorization` musí být záhlaví povoleno zásadami CORS.</span><span class="sxs-lookup"><span data-stu-id="483de-167">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="483de-168">Následující zásady zahrnují konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="483de-168">The following policy includes configuration for:</span></span>

* <span data-ttu-id="483de-169">Původ žádosti ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="483de-169">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="483de-170">Libovolná metoda (příkaz).</span><span class="sxs-lookup"><span data-stu-id="483de-170">Any method (verb).</span></span>
* <span data-ttu-id="483de-171">`Content-Type`a `Authorization` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="483de-171">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="483de-172">Chcete-li pro vlastní hlavičku (například `x-custom-header` ), uveďte záhlaví při volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="483de-172">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="483de-173">Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta ( `credentials` vlastnost je nastavena na hodnotu `include` ).</span><span class="sxs-lookup"><span data-stu-id="483de-173">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="483de-174">Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="483de-174">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="483de-175">Zpracování chyb požadavků na tokeny</span><span class="sxs-lookup"><span data-stu-id="483de-175">Handle token request errors</span></span>

<span data-ttu-id="483de-176">Když jedna stránková aplikace (SPA) ověřuje uživatele pomocí funkce Open ID Connect (OIDC), je stav ověřování udržován místně v zabezpečeném uživatelském rozhraní (SPA) a ve Identity zprostředkovateli (IP) ve formě souboru cookie relace, který je nastaven jako výsledek uživatele, který poskytuje své přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="483de-176">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="483de-177">Tokeny, které jsou pro uživatele vysílané, jsou obvykle platné po krátkou dobu přibližně jedna hodina, takže klientská aplikace musí pravidelně načítat nové tokeny.</span><span class="sxs-lookup"><span data-stu-id="483de-177">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="483de-178">V opačném případě se uživatel odhlásí po vypršení platnosti udělených tokenů.</span><span class="sxs-lookup"><span data-stu-id="483de-178">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="483de-179">Ve většině případů můžou klienti OIDC zřizovat nové tokeny, aniž by museli znovu ověřovat uživatele díky stavu ověřování nebo "relaci", která se udržuje v rámci IP adresy.</span><span class="sxs-lookup"><span data-stu-id="483de-179">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="483de-180">V některých případech může klient získat token bez zásahu uživatele, například pokud z nějakého důvodu se uživatel výslovně odhlásí z IP adresy.</span><span class="sxs-lookup"><span data-stu-id="483de-180">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="483de-181">K tomuto scénáři dochází, pokud uživatel navštíví `https://login.microsoftonline.com` a odhlásí. V těchto scénářích aplikace neví hned, že se uživatel odhlásil. Libovolný token, který může klient obsahovat, již nemusí být platný.</span><span class="sxs-lookup"><span data-stu-id="483de-181">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="483de-182">Klient navíc nemůže zřídit nový token bez zásahu uživatele po vypršení platnosti tohoto tokenu.</span><span class="sxs-lookup"><span data-stu-id="483de-182">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="483de-183">Tyto scénáře nejsou specifické pro ověřování založené na tokenech.</span><span class="sxs-lookup"><span data-stu-id="483de-183">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="483de-184">Jsou součástí charakteru jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="483de-184">They are part of the nature of SPAs.</span></span> <span data-ttu-id="483de-185">ZABEZPEČENÉ ověřování pomocí souborů cookie také nedokáže volat rozhraní API serveru, pokud je soubor cookie ověření odebrán.</span><span class="sxs-lookup"><span data-stu-id="483de-185">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="483de-186">Když aplikace provede volání rozhraní API k chráněným prostředkům, musíte mít na paměti následující informace:</span><span class="sxs-lookup"><span data-stu-id="483de-186">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="483de-187">Pro zřízení nového přístupového tokenu pro volání rozhraní API může být uživatel požádán o ověření znovu.</span><span class="sxs-lookup"><span data-stu-id="483de-187">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="483de-188">I v případě, že má klient token, který je pravděpodobně platný, volání serveru může selhat, protože byl token odvolán uživatelem.</span><span class="sxs-lookup"><span data-stu-id="483de-188">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="483de-189">Když aplikace požaduje token, existují dva možné výsledky:</span><span class="sxs-lookup"><span data-stu-id="483de-189">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="483de-190">Požadavek je úspěšný a aplikace má platný token.</span><span class="sxs-lookup"><span data-stu-id="483de-190">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="483de-191">Požadavek se nezdařil a aplikace musí znovu ověřit uživatele, aby získal nový token.</span><span class="sxs-lookup"><span data-stu-id="483de-191">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="483de-192">Pokud se žádost o token nezdařila, musíte se rozhodnout, zda chcete před provedením přesměrování Uložit aktuální stav.</span><span class="sxs-lookup"><span data-stu-id="483de-192">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="483de-193">Existuje několik přístupů se zvýšenými úrovněmi složitosti:</span><span class="sxs-lookup"><span data-stu-id="483de-193">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="483de-194">Uloží aktuální stav stránky do úložiště relace.</span><span class="sxs-lookup"><span data-stu-id="483de-194">Store the current page state in session storage.</span></span> <span data-ttu-id="483de-195">Během [události životního cyklu OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) ověřte, zda je možné obnovit stav, než budete pokračovat.</span><span class="sxs-lookup"><span data-stu-id="483de-195">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="483de-196">Přidejte parametr řetězce dotazu a použijte ho jako způsob, jak aplikaci signalizovat, že potřebuje znovu Hydrate dříve uložený stav.</span><span class="sxs-lookup"><span data-stu-id="483de-196">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="483de-197">Přidejte parametr řetězce dotazu s jedinečným identifikátorem pro ukládání dat v úložišti relace bez rizikových kolizí s ostatními položkami.</span><span class="sxs-lookup"><span data-stu-id="483de-197">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="483de-198">Následující příklad ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="483de-198">The following example shows how to:</span></span>

* <span data-ttu-id="483de-199">Zachovat stav před přesměrováním na přihlašovací stránku</span><span class="sxs-lookup"><span data-stu-id="483de-199">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="483de-200">Obnovte předchozí stav po ověření pomocí parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="483de-200">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="483de-201">Uložení stavu aplikace před operací ověřování</span><span class="sxs-lookup"><span data-stu-id="483de-201">Save app state before an authentication operation</span></span>

<span data-ttu-id="483de-202">Během operace ověřování existují případy, kdy chcete uložit stav aplikace, než se prohlížeč přesměruje na IP adresu.</span><span class="sxs-lookup"><span data-stu-id="483de-202">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="483de-203">To může být případ, kdy používáte kontejner stavu a chcete obnovit stav po úspěšném ověření.</span><span class="sxs-lookup"><span data-stu-id="483de-203">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="483de-204">Vlastní objekt stavu ověřování můžete použít k zachování stavu specifického pro aplikaci nebo odkaz na něj a obnovení tohoto stavu po úspěšném dokončení operace ověřování.</span><span class="sxs-lookup"><span data-stu-id="483de-204">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="483de-205">Následující příklad demonstruje přístup.</span><span class="sxs-lookup"><span data-stu-id="483de-205">The following example demonstrates the approach.</span></span>

<span data-ttu-id="483de-206">V aplikaci se vytvoří třída kontejneru stavů s vlastnostmi, které uchovávají hodnoty stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="483de-206">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="483de-207">V následujícím příkladu se kontejner používá k údržbě hodnoty čítače výchozí `Counter` součásti šablony (*Pages/Counter. Razor*).</span><span class="sxs-lookup"><span data-stu-id="483de-207">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (*Pages/Counter.razor*).</span></span> <span data-ttu-id="483de-208">Metody pro serializaci a deserializaci kontejneru jsou založeny na <xref:System.Text.Json> .</span><span class="sxs-lookup"><span data-stu-id="483de-208">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="483de-209">`Counter`Komponenta pomocí kontejneru stavů udržuje `currentCount` hodnotu mimo součást:</span><span class="sxs-lookup"><span data-stu-id="483de-209">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="483de-210">Vytvořte `ApplicationAuthenticationState` z <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> .</span><span class="sxs-lookup"><span data-stu-id="483de-210">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="483de-211">Zadejte `Id` vlastnost, která slouží jako identifikátor místně uloženého stavu.</span><span class="sxs-lookup"><span data-stu-id="483de-211">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="483de-212">*ApplicationAuthenticationState.cs*:</span><span class="sxs-lookup"><span data-stu-id="483de-212">*ApplicationAuthenticationState.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="483de-213">`Authentication`Součást (*Pages/Authentication. Razor*) ukládá a obnovuje stav aplikace pomocí úložiště místních relací s `StateContainer` metodami serializace a deserializace `GetStateForLocalStorage` a `SetStateFromLocalStorage` :</span><span class="sxs-lookup"><span data-stu-id="483de-213">The `Authentication` component (*Pages/Authentication.razor*) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="483de-214">V tomto příkladu se pro ověřování používá Azure Active Directory (AAD).</span><span class="sxs-lookup"><span data-stu-id="483de-214">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="483de-215">V `Program.Main` (*program.cs*):</span><span class="sxs-lookup"><span data-stu-id="483de-215">In `Program.Main` (*Program.cs*):</span></span>

* <span data-ttu-id="483de-216">`ApplicationAuthenticationState`Je nakonfigurovaný jako typ knihovny Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` .</span><span class="sxs-lookup"><span data-stu-id="483de-216">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="483de-217">Kontejner stavů je zaregistrován v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="483de-217">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="483de-218">Přizpůsobení směrování aplikací</span><span class="sxs-lookup"><span data-stu-id="483de-218">Customize app routes</span></span>

<span data-ttu-id="483de-219">Ve výchozím nastavení používá knihovna [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) Library trasy, které jsou uvedeny v následující tabulce, pro reprezentaci různých stavů ověřování.</span><span class="sxs-lookup"><span data-stu-id="483de-219">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="483de-220">Trasa</span><span class="sxs-lookup"><span data-stu-id="483de-220">Route</span></span>                            | <span data-ttu-id="483de-221">Účel</span><span class="sxs-lookup"><span data-stu-id="483de-221">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="483de-222">Spustí operaci přihlášení.</span><span class="sxs-lookup"><span data-stu-id="483de-222">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="483de-223">Zpracovává výsledek jakékoli operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="483de-223">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="483de-224">Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="483de-224">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="483de-225">Spustí operaci odhlášení.</span><span class="sxs-lookup"><span data-stu-id="483de-225">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="483de-226">Zpracovává výsledek operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="483de-226">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="483de-227">Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="483de-227">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="483de-228">Indikuje, že se uživatel úspěšně odhlásit.</span><span class="sxs-lookup"><span data-stu-id="483de-228">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="483de-229">Aktivuje operaci pro úpravu profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="483de-229">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="483de-230">Aktivuje operaci pro registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="483de-230">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="483de-231">Trasy zobrazené v předchozí tabulce lze konfigurovat prostřednictvím <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="483de-231">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="483de-232">Při nastavování možností pro poskytování vlastních tras potvrďte, že aplikace má trasu, která zpracovává jednotlivé cesty.</span><span class="sxs-lookup"><span data-stu-id="483de-232">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="483de-233">V následujícím příkladu jsou všechny cesty s předponou `/security` .</span><span class="sxs-lookup"><span data-stu-id="483de-233">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="483de-234">`Authentication`součást (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="483de-234">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="483de-235">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="483de-235">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="483de-236">Pokud požadavek volá zcela různé cesty, nastavte trasy popsané dříve a vykreslete <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> s parametrem explicitní akce:</span><span class="sxs-lookup"><span data-stu-id="483de-236">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="483de-237">Pokud se rozhodnete tak učinit, můžete uživatelské rozhraní přerušit na jiné stránky.</span><span class="sxs-lookup"><span data-stu-id="483de-237">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="483de-238">Přizpůsobení uživatelského rozhraní ověřování</span><span class="sxs-lookup"><span data-stu-id="483de-238">Customize the authentication user interface</span></span>

<span data-ttu-id="483de-239"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>obsahuje výchozí sadu částí uživatelského rozhraní pro každý stav ověřování.</span><span class="sxs-lookup"><span data-stu-id="483de-239"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="483de-240">Každý stav lze přizpůsobit předáním vlastního <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="483de-240">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="483de-241">K přizpůsobení zobrazeného textu během procesu prvotního přihlášení může změnit následující postup <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> .</span><span class="sxs-lookup"><span data-stu-id="483de-241">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="483de-242">`Authentication`součást (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="483de-242">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="483de-243"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Má jeden fragment, který se dá použít pro jednu trasu ověřování, jak je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="483de-243">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="483de-244">Trasa</span><span class="sxs-lookup"><span data-stu-id="483de-244">Route</span></span>                            | <span data-ttu-id="483de-245">Fragment</span><span class="sxs-lookup"><span data-stu-id="483de-245">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="483de-246">Přizpůsobení uživatele</span><span class="sxs-lookup"><span data-stu-id="483de-246">Customize the user</span></span>

<span data-ttu-id="483de-247">Uživatele navázané na aplikaci je možné přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="483de-247">Users bound to the app can be customized.</span></span> <span data-ttu-id="483de-248">V následujícím příkladu obdrží všichni ověření uživatelé `amr` deklaraci identity pro každou metodu ověřování uživatele.</span><span class="sxs-lookup"><span data-stu-id="483de-248">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="483de-249">Vytvořte třídu, která rozšiřuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> třídu:</span><span class="sxs-lookup"><span data-stu-id="483de-249">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="483de-250">Vytvořte objekt pro vytváření, který rozšiřuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="483de-250">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="483de-251">Zaregistrujte zprostředkovatele ověřování, který se `CustomAccountFactory` používá.</span><span class="sxs-lookup"><span data-stu-id="483de-251">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="483de-252">Platná jsou následující registrace:</span><span class="sxs-lookup"><span data-stu-id="483de-252">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="483de-253"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="483de-253"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="483de-254"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="483de-254"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="483de-255"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="483de-255"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="483de-256">Podpora předběžného vykreslování s ověřováním</span><span class="sxs-lookup"><span data-stu-id="483de-256">Support prerendering with authentication</span></span>

<span data-ttu-id="483de-257">Po použití pokynů v některém z hostovaných Blazor témat aplikace WebAssembly použijte následující pokyny k vytvoření aplikace, která:</span><span class="sxs-lookup"><span data-stu-id="483de-257">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="483de-258">Předem vykreslí cesty, pro které není nutná autorizace.</span><span class="sxs-lookup"><span data-stu-id="483de-258">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="483de-259">Nejedná se o cesty PreRender, pro které se vyžaduje autorizace.</span><span class="sxs-lookup"><span data-stu-id="483de-259">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="483de-260">V rámci třídy klientské aplikace `Program` (*program.cs*) se služba Factoring Common registruje do samostatné metody (například `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="483de-260">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="483de-261">V serverové aplikaci `Startup.ConfigureServices` Zaregistrujte následující další služby:</span><span class="sxs-lookup"><span data-stu-id="483de-261">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="483de-262">V metodě serverové aplikace `Startup.Configure` nahraďte [koncové body. MapFallbackToFile ("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) s [koncovými body. MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="483de-262">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="483de-263">V serverové aplikaci vytvořte složku *stránky* , pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="483de-263">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="483de-264">Vytvořte stránku *_Host. cshtml* ve složce *stránek* serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="483de-264">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="483de-265">Obsah souboru *wwwroot/index.html* klientské aplikace vložte do souboru *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="483de-265">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="483de-266">Aktualizujte obsah souboru:</span><span class="sxs-lookup"><span data-stu-id="483de-266">Update the file's contents:</span></span>

* <span data-ttu-id="483de-267">Přidejte `@page "_Host"` na začátek souboru.</span><span class="sxs-lookup"><span data-stu-id="483de-267">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="483de-268">Značku nahraďte `<app>Loading...</app>` následujícím:</span><span class="sxs-lookup"><span data-stu-id="483de-268">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="483de-269">Možnosti pro hostované aplikace a poskytovatele přihlašovacích údajů třetích stran</span><span class="sxs-lookup"><span data-stu-id="483de-269">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="483de-270">Při ověřování a autorizaci hostované Blazor aplikace WebAssembly s poskytovatelem třetí strany je k dispozici několik možností pro ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="483de-270">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="483de-271">Kterou zvolíte, závisí na vašem scénáři.</span><span class="sxs-lookup"><span data-stu-id="483de-271">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="483de-272">Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="483de-272">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="483de-273">Ověřování uživatelů pro volání rozhraní API chráněných třetích stran</span><span class="sxs-lookup"><span data-stu-id="483de-273">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="483de-274">Ověřit uživatele pomocí toku OAuth na straně klienta proti poskytovateli rozhraní API třetí strany:</span><span class="sxs-lookup"><span data-stu-id="483de-274">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="483de-275">V tomto scénáři:</span><span class="sxs-lookup"><span data-stu-id="483de-275">In this scenario:</span></span>

* <span data-ttu-id="483de-276">Server, který je hostitelem aplikace, nehraje roli.</span><span class="sxs-lookup"><span data-stu-id="483de-276">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="483de-277">Rozhraní API na serveru nejde chránit.</span><span class="sxs-lookup"><span data-stu-id="483de-277">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="483de-278">Aplikace může volat jenom chráněná rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="483de-278">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="483de-279">Ověřování uživatelů pomocí poskytovatele třetí strany a volání chráněných rozhraní API na hostitelském serveru a třetí straně</span><span class="sxs-lookup"><span data-stu-id="483de-279">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="483de-280">Nakonfigurujte Identity pomocí poskytovatele přihlášení třetí strany.</span><span class="sxs-lookup"><span data-stu-id="483de-280">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="483de-281">Získejte tokeny vyžadované pro přístup k rozhraní API třetích stran a uložte je.</span><span class="sxs-lookup"><span data-stu-id="483de-281">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="483de-282">Když se uživatel přihlásí, Identity shromažďuje v rámci procesu ověřování tokeny a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="483de-282">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="483de-283">V tomto okamžiku je k dispozici několik přístupů pro volání rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="483de-283">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="483de-284">Použití přístupového tokenu serveru k načtení přístupového tokenu třetí strany</span><span class="sxs-lookup"><span data-stu-id="483de-284">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="483de-285">K načtení přístupového tokenu třetí strany z koncového bodu rozhraní API serveru použijte přístupový token vygenerovaný na serveru.</span><span class="sxs-lookup"><span data-stu-id="483de-285">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="483de-286">Odtud můžete pomocí přístupového tokenu třetí strany volat prostředky rozhraní API třetích stran přímo z Identity klienta.</span><span class="sxs-lookup"><span data-stu-id="483de-286">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="483de-287">Tento postup nedoporučujeme.</span><span class="sxs-lookup"><span data-stu-id="483de-287">We don't recommend this approach.</span></span> <span data-ttu-id="483de-288">Tento přístup vyžaduje ošetření přístupového tokenu třetí strany, jako kdyby byl vygenerován pro veřejného klienta.</span><span class="sxs-lookup"><span data-stu-id="483de-288">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="483de-289">V případech OAuth nemá veřejná aplikace tajný klíč klienta, protože nemůže být důvěryhodná pro bezpečné ukládání tajných kódů a přístupového tokenu se vytvoří pro důvěrného klienta.</span><span class="sxs-lookup"><span data-stu-id="483de-289">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="483de-290">Důvěrný klient je klient, který má tajný klíč klienta a předpokládá, že bude moci bezpečně ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="483de-290">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="483de-291">Přístupovému tokenu třetí strany se můžou udělit další obory, které budou provádět citlivé operace na základě skutečnosti, že třetí strana vygenerovala token pro více důvěryhodných klientů.</span><span class="sxs-lookup"><span data-stu-id="483de-291">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="483de-292">Podobně by se aktualizační tokeny neměly vystavit klientovi, který není důvěryhodný, protože by to tak měl klienta bez omezení přístupu, pokud se na ně neukládají jiná omezení.</span><span class="sxs-lookup"><span data-stu-id="483de-292">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="483de-293">Volání rozhraní API z klienta na rozhraní API serveru za účelem volání rozhraní API třetích stran</span><span class="sxs-lookup"><span data-stu-id="483de-293">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="483de-294">Naplňte volání rozhraní API z klienta na serverové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="483de-294">Make an API call from the client to the server API.</span></span> <span data-ttu-id="483de-295">Ze serveru načtěte přístupový token pro prostředek rozhraní API třetí strany a vydejte jakékoli volání, které je potřeba.</span><span class="sxs-lookup"><span data-stu-id="483de-295">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="483de-296">I když tento přístup vyžaduje další síťové směrování prostřednictvím serveru za účelem volání rozhraní API třetí strany, má za následek bezpečnější prostředí:</span><span class="sxs-lookup"><span data-stu-id="483de-296">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="483de-297">Server může ukládat aktualizační tokeny a zajistit, aby aplikace neztratila přístup k prostředkům třetích stran.</span><span class="sxs-lookup"><span data-stu-id="483de-297">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="483de-298">Aplikace nemůže zajímat přístupové tokeny ze serveru, který může obsahovat citlivá oprávnění.</span><span class="sxs-lookup"><span data-stu-id="483de-298">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="483de-299">Použití koncových bodů Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="483de-299">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="483de-300">Knihovna a šablony ověřování Blazor používají koncové body Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="483de-300">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="483de-301">Pokud chcete použít koncový bod v 2.0, nakonfigurujte možnost nosiče JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="483de-301">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="483de-302">V následujícím příkladu je AAD nakonfigurováno v 2.0 připojením `v2.0` segmentu k <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="483de-302">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="483de-303">Případně je možné nastavení vytvořit v souboru nastavení aplikace (*appsettings.jszapnuto*):</span><span class="sxs-lookup"><span data-stu-id="483de-303">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="483de-304">Pokud se označení segmentu pro autoritu nehodí pro poskytovatele OIDC aplikace, jako je třeba u jiných poskytovatelů než AAD, nastavte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> vlastnost přímo.</span><span class="sxs-lookup"><span data-stu-id="483de-304">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="483de-305">Buď nastavte vlastnost v <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> nebo v souboru nastavení aplikace (*appsettings.jszapnuto*) s `Authority` klíčem.</span><span class="sxs-lookup"><span data-stu-id="483de-305">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="483de-306">Seznam deklarací identity v tokenu ID se mění pro koncové body verze 2.0.</span><span class="sxs-lookup"><span data-stu-id="483de-306">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="483de-307">Další informace najdete v tématu [Proč aktualizace pro Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="483de-307">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
