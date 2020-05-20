---
<span data-ttu-id="33f8e-101">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-103">'Blazor'</span></span>
- <span data-ttu-id="33f8e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-104">'Identity'</span></span>
- <span data-ttu-id="33f8e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-106">'Razor'</span></span>
- <span data-ttu-id="33f8e-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="33f8e-108">ASP.NET Core Blazor Další scénáře zabezpečení pro WebAssembly</span><span class="sxs-lookup"><span data-stu-id="33f8e-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="33f8e-109">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="33f8e-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="33f8e-110">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="33f8e-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="33f8e-111">`AuthorizationMessageHandler`Službu lze použít s nástrojem `HttpClient` k připojení přístupových tokenů k odchozím žádostem.</span><span class="sxs-lookup"><span data-stu-id="33f8e-111">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="33f8e-112">Tokeny se získávají pomocí existující `IAccessTokenProvider` služby.</span><span class="sxs-lookup"><span data-stu-id="33f8e-112">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="33f8e-113">Pokud token nelze získat, `AccessTokenNotAvailableException` je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="33f8e-113">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="33f8e-114">`AccessTokenNotAvailableException`má `Redirect` metodu, která se dá použít k navigaci uživatele na poskytovatele identity za účelem získání nového tokenu.</span><span class="sxs-lookup"><span data-stu-id="33f8e-114">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="33f8e-115">`AuthorizationMessageHandler`Pomocí metody lze konfigurovat pomocí autorizovaných adres URL, oborů a návratové adresy URL `ConfigureHandler` .</span><span class="sxs-lookup"><span data-stu-id="33f8e-115">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="33f8e-116">V následujícím příkladu `AuthorizationMessageHandler` nakonfiguruje `HttpClient` v `Program.Main` (*program.cs*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-116">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="33f8e-117">Pro usnadnění práce `BaseAddressAuthorizationMessageHandler` je součástí předem nakonfigurovaná základní adresa aplikace jako autorizovaná adresa URL.</span><span class="sxs-lookup"><span data-stu-id="33f8e-117">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="33f8e-118">Šablony WebAssembly s povoleným ověřováním Blazor teď používají <xref:System.Net.Http.IHttpClientFactory> v projektu rozhraní API serveru k nastavení <xref:System.Net.Http.HttpClient> pomocí `BaseAddressAuthorizationMessageHandler` :</span><span class="sxs-lookup"><span data-stu-id="33f8e-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

<span data-ttu-id="33f8e-119">Tam, kde je klient vytvořen pomocí `CreateClient` v předchozím příkladu, se <xref:System.Net.Http.HttpClient> dodávají instance, které zahrnují přístupové tokeny při vytváření požadavků na serverový projekt.</span><span class="sxs-lookup"><span data-stu-id="33f8e-119">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="33f8e-120">Nakonfigurovaná <xref:System.Net.Http.HttpClient> se pak používá k provádění autorizovaných požadavků pomocí jednoduchého `try-catch` vzoru.</span><span class="sxs-lookup"><span data-stu-id="33f8e-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="33f8e-121">`FetchData`součást (*Pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="33f8e-122">Typové HttpClient</span><span class="sxs-lookup"><span data-stu-id="33f8e-122">Typed HttpClient</span></span>

<span data-ttu-id="33f8e-123">Je možné definovat zadaného klienta, který zpracovává všechny aspekty získání HTTP a tokenu v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="33f8e-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="33f8e-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="33f8e-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="33f8e-125">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="33f8e-126">`FetchData`součást (*Pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="33f8e-127">Konfigurace obslužné rutiny HttpClient</span><span class="sxs-lookup"><span data-stu-id="33f8e-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="33f8e-128">Obslužná rutina může být dále nakonfigurována <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="33f8e-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="33f8e-129">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="33f8e-130">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="33f8e-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="33f8e-131">Pokud Blazor aplikace WebAssembly obvykle používá zabezpečené výchozí nastavení <xref:System.Net.Http.HttpClient> , může aplikace také provést neověřené nebo neautorizované požadavky webového rozhraní API konfigurací pojmenovaného <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="33f8e-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="33f8e-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="33f8e-133">Předchozí registrace je kromě existující zabezpečené výchozí <xref:System.Net.Http.HttpClient> registrace.</span><span class="sxs-lookup"><span data-stu-id="33f8e-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="33f8e-134">Komponenta vytvoří <xref:System.Net.Http.HttpClient> z <xref:System.Net.Http.IHttpClientFactory> k provedení neověřených nebo neautorizovaných požadavků:</span><span class="sxs-lookup"><span data-stu-id="33f8e-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="33f8e-135">Řadič v rozhraní API serveru `WeatherForecastNoAuthenticationController` pro předchozí příklad není označený [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="33f8e-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="33f8e-136">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="33f8e-136">Request additional access tokens</span></span>

<span data-ttu-id="33f8e-137">Přístupové tokeny lze získat ručně voláním `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="33f8e-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="33f8e-138">V následujícím příkladu jsou vyžadovány další Azure Active Directory (AAD) Microsoft Graph obory rozhraní API pro čtení uživatelských dat a odesílání e-mailů.</span><span class="sxs-lookup"><span data-stu-id="33f8e-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="33f8e-139">Po přidání oprávnění rozhraní API Microsoft Graph na portálu Azure AAD jsou další obory nakonfigurované v klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="33f8e-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="33f8e-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="33f8e-141">`IAccessTokenProvider.RequestToken`Metoda poskytuje přetížení, které umožňuje aplikaci zřídit přístupový token s danou sadou oborů.</span><span class="sxs-lookup"><span data-stu-id="33f8e-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="33f8e-142">V Razor součásti:</span><span class="sxs-lookup"><span data-stu-id="33f8e-142">In a Razor component:</span></span>

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

<span data-ttu-id="33f8e-143">`TryGetToken`Vrátí</span><span class="sxs-lookup"><span data-stu-id="33f8e-143">`TryGetToken` returns:</span></span>

* <span data-ttu-id="33f8e-144">`true`s nástrojem `token` pro použití.</span><span class="sxs-lookup"><span data-stu-id="33f8e-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="33f8e-145">`false`Pokud se token nenačte.</span><span class="sxs-lookup"><span data-stu-id="33f8e-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="33f8e-146">HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení</span><span class="sxs-lookup"><span data-stu-id="33f8e-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="33f8e-147">Při spuštění na WebAssembly v Blazor aplikaci WebAssembly [HttpClient](xref:fundamentals/http-requests) a dá se <xref:System.Net.Http.HttpRequestMessage> použít k přizpůsobení požadavků.</span><span class="sxs-lookup"><span data-stu-id="33f8e-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="33f8e-148">Můžete například zadat metodu HTTP a hlavičku požadavku.</span><span class="sxs-lookup"><span data-stu-id="33f8e-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="33f8e-149">Následující komponenta vytvoří požadavek na `POST` koncový bod rozhraní API seznamu na serveru a zobrazí tělo odpovědi:</span><span class="sxs-lookup"><span data-stu-id="33f8e-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="33f8e-150">Implementace rozhraní .NET WebAssembly pro `HttpClient` používá [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="33f8e-150">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="33f8e-151">Načtení umožňuje nakonfigurovat několik [možností specifických pro požadavky](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="33f8e-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="33f8e-152">Možnosti požadavku HTTP Fetch lze konfigurovat pomocí `HttpRequestMessage` rozšiřujících metod, které jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="33f8e-152">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="33f8e-153">`HttpRequestMessage`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="33f8e-153">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="33f8e-154">Načíst vlastnost žádosti</span><span class="sxs-lookup"><span data-stu-id="33f8e-154">Fetch request property</span></span> |
| ---
<span data-ttu-id="33f8e-155">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-155">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-156">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-157">'Blazor'</span></span>
- <span data-ttu-id="33f8e-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-158">'Identity'</span></span>
- <span data-ttu-id="33f8e-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-160">'Razor'</span></span>
- <span data-ttu-id="33f8e-161">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-162">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-162">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-163">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-164">'Blazor'</span></span>
- <span data-ttu-id="33f8e-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-165">'Identity'</span></span>
- <span data-ttu-id="33f8e-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-167">'Razor'</span></span>
- <span data-ttu-id="33f8e-168">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-169">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-169">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-170">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-171">'Blazor'</span></span>
- <span data-ttu-id="33f8e-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-172">'Identity'</span></span>
- <span data-ttu-id="33f8e-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-174">'Razor'</span></span>
- <span data-ttu-id="33f8e-175">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-176">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-176">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-177">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-178">'Blazor'</span></span>
- <span data-ttu-id="33f8e-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-179">'Identity'</span></span>
- <span data-ttu-id="33f8e-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-181">'Razor'</span></span>
- <span data-ttu-id="33f8e-182">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-183">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-183">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-184">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-185">'Blazor'</span></span>
- <span data-ttu-id="33f8e-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-186">'Identity'</span></span>
- <span data-ttu-id="33f8e-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-188">'Razor'</span></span>
- <span data-ttu-id="33f8e-189">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-190">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-190">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-191">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-192">'Blazor'</span></span>
- <span data-ttu-id="33f8e-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-193">'Identity'</span></span>
- <span data-ttu-id="33f8e-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-195">'Razor'</span></span>
- <span data-ttu-id="33f8e-196">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-197">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-197">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-198">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-199">'Blazor'</span></span>
- <span data-ttu-id="33f8e-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-200">'Identity'</span></span>
- <span data-ttu-id="33f8e-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-202">'Razor'</span></span>
- <span data-ttu-id="33f8e-203">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-204">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-204">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-205">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-206">'Blazor'</span></span>
- <span data-ttu-id="33f8e-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-207">'Identity'</span></span>
- <span data-ttu-id="33f8e-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-209">'Razor'</span></span>
- <span data-ttu-id="33f8e-210">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-211">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-211">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-212">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-213">'Blazor'</span></span>
- <span data-ttu-id="33f8e-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-214">'Identity'</span></span>
- <span data-ttu-id="33f8e-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-216">'Razor'</span></span>
- <span data-ttu-id="33f8e-217">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-218">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-218">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-219">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-220">'Blazor'</span></span>
- <span data-ttu-id="33f8e-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-221">'Identity'</span></span>
- <span data-ttu-id="33f8e-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-223">'Razor'</span></span>
- <span data-ttu-id="33f8e-224">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-225">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-225">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-226">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-227">'Blazor'</span></span>
- <span data-ttu-id="33f8e-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-228">'Identity'</span></span>
- <span data-ttu-id="33f8e-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-230">'Razor'</span></span>
- <span data-ttu-id="33f8e-231">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-232">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-232">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-233">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-234">'Blazor'</span></span>
- <span data-ttu-id="33f8e-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-235">'Identity'</span></span>
- <span data-ttu-id="33f8e-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-237">'Razor'</span></span>
- <span data-ttu-id="33f8e-238">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-239">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-239">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-240">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-241">'Blazor'</span></span>
- <span data-ttu-id="33f8e-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-242">'Identity'</span></span>
- <span data-ttu-id="33f8e-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-244">'Razor'</span></span>
- <span data-ttu-id="33f8e-245">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-246">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-246">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-247">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-248">'Blazor'</span></span>
- <span data-ttu-id="33f8e-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-249">'Identity'</span></span>
- <span data-ttu-id="33f8e-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-251">'Razor'</span></span>
- <span data-ttu-id="33f8e-252">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-253">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-253">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-254">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-255">'Blazor'</span></span>
- <span data-ttu-id="33f8e-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-256">'Identity'</span></span>
- <span data-ttu-id="33f8e-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-258">'Razor'</span></span>
- <span data-ttu-id="33f8e-259">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-260">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-260">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-261">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-262">'Blazor'</span></span>
- <span data-ttu-id="33f8e-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-263">'Identity'</span></span>
- <span data-ttu-id="33f8e-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-265">'Razor'</span></span>
- <span data-ttu-id="33f8e-266">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-266">'SignalR' uid:</span></span> 

<span data-ttu-id="33f8e-267">------------------- | ---Název: ' ASP.NET Core Blazor Další scénáře zabezpečení pro WebAssembly ' Autor: Description: ' Naučte se nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-267">------------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-268">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-269">'Blazor'</span></span>
- <span data-ttu-id="33f8e-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-270">'Identity'</span></span>
- <span data-ttu-id="33f8e-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-272">'Razor'</span></span>
- <span data-ttu-id="33f8e-273">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-274">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-274">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-275">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-276">'Blazor'</span></span>
- <span data-ttu-id="33f8e-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-277">'Identity'</span></span>
- <span data-ttu-id="33f8e-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-279">'Razor'</span></span>
- <span data-ttu-id="33f8e-280">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-281">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-281">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-282">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-283">'Blazor'</span></span>
- <span data-ttu-id="33f8e-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-284">'Identity'</span></span>
- <span data-ttu-id="33f8e-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-286">'Razor'</span></span>
- <span data-ttu-id="33f8e-287">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-288">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-288">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-289">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-290">'Blazor'</span></span>
- <span data-ttu-id="33f8e-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-291">'Identity'</span></span>
- <span data-ttu-id="33f8e-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-293">'Razor'</span></span>
- <span data-ttu-id="33f8e-294">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-295">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-295">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-296">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-297">'Blazor'</span></span>
- <span data-ttu-id="33f8e-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-298">'Identity'</span></span>
- <span data-ttu-id="33f8e-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-300">'Razor'</span></span>
- <span data-ttu-id="33f8e-301">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-302">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-302">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-303">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-304">'Blazor'</span></span>
- <span data-ttu-id="33f8e-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-305">'Identity'</span></span>
- <span data-ttu-id="33f8e-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-307">'Razor'</span></span>
- <span data-ttu-id="33f8e-308">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-309">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-309">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-310">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-311">'Blazor'</span></span>
- <span data-ttu-id="33f8e-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-312">'Identity'</span></span>
- <span data-ttu-id="33f8e-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-314">'Razor'</span></span>
- <span data-ttu-id="33f8e-315">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-316">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-316">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-317">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-318">'Blazor'</span></span>
- <span data-ttu-id="33f8e-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-319">'Identity'</span></span>
- <span data-ttu-id="33f8e-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-321">'Razor'</span></span>
- <span data-ttu-id="33f8e-322">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-323">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-323">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-324">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-325">'Blazor'</span></span>
- <span data-ttu-id="33f8e-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-326">'Identity'</span></span>
- <span data-ttu-id="33f8e-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-328">'Razor'</span></span>
- <span data-ttu-id="33f8e-329">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-329">'SignalR' uid:</span></span> 

<span data-ttu-id="33f8e-330">----------- | | `SetBrowserRequestCredentials`         |  [přihlašovací údaje](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`               |  [mezipaměť](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`                |  [režim](https://developer.mozilla.org/docs/Web/API/Request/mode) | `SetBrowserRequestIntegrity` |           |  [Integrita](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span><span class="sxs-lookup"><span data-stu-id="33f8e-330">----------- | | `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | | `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span></span>

<span data-ttu-id="33f8e-331">Další možnosti můžete nastavit pomocí obecnější `SetBrowserRequestOption` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="33f8e-331">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="33f8e-332">Odpověď HTTP je obvykle ukládána do vyrovnávací paměti v Blazor aplikaci WebAssembly, aby umožnila podporu pro čtení v obsahu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="33f8e-332">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="33f8e-333">Pokud chcete povolit podporu pro streamování odpovědí, použijte `SetBrowserResponseStreamingEnabled` metodu rozšíření v žádosti.</span><span class="sxs-lookup"><span data-stu-id="33f8e-333">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="33f8e-334">Pokud chcete do žádosti o více zdrojů zahrnout přihlašovací údaje, použijte `SetBrowserRequestCredentials` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="33f8e-334">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="33f8e-335">Další informace o možnostech načtení rozhraní API naleznete v tématu [MDN web Docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="33f8e-335">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="33f8e-336">Při odesílání přihlašovacích údajů (souborů cookie autorizace/hlaviček) na žádostech CORS `Authorization` musí být záhlaví povoleno zásadami CORS.</span><span class="sxs-lookup"><span data-stu-id="33f8e-336">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="33f8e-337">Následující zásady zahrnují konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="33f8e-337">The following policy includes configuration for:</span></span>

* <span data-ttu-id="33f8e-338">Původ žádosti ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="33f8e-338">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="33f8e-339">Libovolná metoda (příkaz).</span><span class="sxs-lookup"><span data-stu-id="33f8e-339">Any method (verb).</span></span>
* <span data-ttu-id="33f8e-340">`Content-Type`a `Authorization` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="33f8e-340">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="33f8e-341">Chcete-li pro vlastní hlavičku (například `x-custom-header` ), uveďte záhlaví při volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="33f8e-341">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="33f8e-342">Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta ( `credentials` vlastnost je nastavena na hodnotu `include` ).</span><span class="sxs-lookup"><span data-stu-id="33f8e-342">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="33f8e-343">Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="33f8e-343">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="33f8e-344">Zpracování chyb požadavků na tokeny</span><span class="sxs-lookup"><span data-stu-id="33f8e-344">Handle token request errors</span></span>

<span data-ttu-id="33f8e-345">Když jedna stránková aplikace (SPA) ověřuje uživatele pomocí funkce Open ID Connect (OIDC), je stav ověřování udržován místně v zabezpečeném uživatelském rozhraní (SPA) a ve Identity zprostředkovateli (IP) ve formě souboru cookie relace, který je nastaven jako výsledek uživatele, který poskytuje své přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="33f8e-345">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="33f8e-346">Tokeny, které jsou pro uživatele vysílané, jsou obvykle platné po krátkou dobu přibližně jedna hodina, takže klientská aplikace musí pravidelně načítat nové tokeny.</span><span class="sxs-lookup"><span data-stu-id="33f8e-346">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="33f8e-347">V opačném případě se uživatel odhlásí po vypršení platnosti udělených tokenů.</span><span class="sxs-lookup"><span data-stu-id="33f8e-347">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="33f8e-348">Ve většině případů můžou klienti OIDC zřizovat nové tokeny, aniž by museli znovu ověřovat uživatele díky stavu ověřování nebo "relaci", která se udržuje v rámci IP adresy.</span><span class="sxs-lookup"><span data-stu-id="33f8e-348">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="33f8e-349">V některých případech může klient získat token bez zásahu uživatele, například pokud z nějakého důvodu se uživatel výslovně odhlásí z IP adresy.</span><span class="sxs-lookup"><span data-stu-id="33f8e-349">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="33f8e-350">K tomuto scénáři dochází, pokud uživatel navštíví `https://login.microsoftonline.com` a odhlásí. V těchto scénářích aplikace neví hned, že se uživatel odhlásil. Libovolný token, který může klient obsahovat, již nemusí být platný.</span><span class="sxs-lookup"><span data-stu-id="33f8e-350">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="33f8e-351">Klient navíc nemůže zřídit nový token bez zásahu uživatele po vypršení platnosti tohoto tokenu.</span><span class="sxs-lookup"><span data-stu-id="33f8e-351">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="33f8e-352">Tyto scénáře nejsou specifické pro ověřování založené na tokenech.</span><span class="sxs-lookup"><span data-stu-id="33f8e-352">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="33f8e-353">Jsou součástí charakteru jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="33f8e-353">They are part of the nature of SPAs.</span></span> <span data-ttu-id="33f8e-354">ZABEZPEČENÉ ověřování pomocí souborů cookie také nedokáže volat rozhraní API serveru, pokud je soubor cookie ověření odebrán.</span><span class="sxs-lookup"><span data-stu-id="33f8e-354">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="33f8e-355">Když aplikace provede volání rozhraní API k chráněným prostředkům, musíte mít na paměti následující informace:</span><span class="sxs-lookup"><span data-stu-id="33f8e-355">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="33f8e-356">Pro zřízení nového přístupového tokenu pro volání rozhraní API může být uživatel požádán o ověření znovu.</span><span class="sxs-lookup"><span data-stu-id="33f8e-356">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="33f8e-357">I v případě, že má klient token, který je pravděpodobně platný, volání serveru může selhat, protože byl token odvolán uživatelem.</span><span class="sxs-lookup"><span data-stu-id="33f8e-357">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="33f8e-358">Když aplikace požaduje token, existují dva možné výsledky:</span><span class="sxs-lookup"><span data-stu-id="33f8e-358">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="33f8e-359">Požadavek je úspěšný a aplikace má platný token.</span><span class="sxs-lookup"><span data-stu-id="33f8e-359">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="33f8e-360">Požadavek se nezdařil a aplikace musí znovu ověřit uživatele, aby získal nový token.</span><span class="sxs-lookup"><span data-stu-id="33f8e-360">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="33f8e-361">Pokud se žádost o token nezdařila, musíte se rozhodnout, zda chcete před provedením přesměrování Uložit aktuální stav.</span><span class="sxs-lookup"><span data-stu-id="33f8e-361">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="33f8e-362">Existuje několik přístupů se zvýšenými úrovněmi složitosti:</span><span class="sxs-lookup"><span data-stu-id="33f8e-362">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="33f8e-363">Uloží aktuální stav stránky do úložiště relace.</span><span class="sxs-lookup"><span data-stu-id="33f8e-363">Store the current page state in session storage.</span></span> <span data-ttu-id="33f8e-364">Během této operace `OnInitializeAsync` Ověřte, zda je možné obnovit stav, než budete pokračovat.</span><span class="sxs-lookup"><span data-stu-id="33f8e-364">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="33f8e-365">Přidejte parametr řetězce dotazu a použijte ho jako způsob, jak aplikaci signalizovat, že potřebuje znovu Hydrate dříve uložený stav.</span><span class="sxs-lookup"><span data-stu-id="33f8e-365">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="33f8e-366">Přidejte parametr řetězce dotazu s jedinečným identifikátorem pro ukládání dat v úložišti relace bez rizikových kolizí s ostatními položkami.</span><span class="sxs-lookup"><span data-stu-id="33f8e-366">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="33f8e-367">Následující příklad ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="33f8e-367">The following example shows how to:</span></span>

* <span data-ttu-id="33f8e-368">Zachovat stav před přesměrováním na přihlašovací stránku</span><span class="sxs-lookup"><span data-stu-id="33f8e-368">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="33f8e-369">Obnovte předchozí stav po ověření pomocí parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="33f8e-369">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="33f8e-370">Uložení stavu aplikace před operací ověřování</span><span class="sxs-lookup"><span data-stu-id="33f8e-370">Save app state before an authentication operation</span></span>

<span data-ttu-id="33f8e-371">Během operace ověřování existují případy, kdy chcete uložit stav aplikace, než se prohlížeč přesměruje na IP adresu.</span><span class="sxs-lookup"><span data-stu-id="33f8e-371">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="33f8e-372">To může být případ, kdy používáte něco jako kontejner stavu a chcete obnovit stav po úspěšném ověření.</span><span class="sxs-lookup"><span data-stu-id="33f8e-372">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="33f8e-373">Vlastní objekt stavu ověřování můžete použít k zachování stavu specifického pro aplikaci nebo odkaz na něj a obnovení tohoto stavu po úspěšném dokončení operace ověřování.</span><span class="sxs-lookup"><span data-stu-id="33f8e-373">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="33f8e-374">`Authentication`součást (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-374">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a><span data-ttu-id="33f8e-375">Přizpůsobení směrování aplikací</span><span class="sxs-lookup"><span data-stu-id="33f8e-375">Customize app routes</span></span>

<span data-ttu-id="33f8e-376">Ve výchozím nastavení `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Knihovna používá trasy, které jsou uvedeny v následující tabulce, pro reprezentace různých stavů ověřování.</span><span class="sxs-lookup"><span data-stu-id="33f8e-376">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="33f8e-377">Trasa</span><span class="sxs-lookup"><span data-stu-id="33f8e-377">Route</span></span>                            | <span data-ttu-id="33f8e-378">Účel</span><span class="sxs-lookup"><span data-stu-id="33f8e-378">Purpose</span></span> |
| ---
<span data-ttu-id="33f8e-379">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-379">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-380">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-381">'Blazor'</span></span>
- <span data-ttu-id="33f8e-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-382">'Identity'</span></span>
- <span data-ttu-id="33f8e-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-384">'Razor'</span></span>
- <span data-ttu-id="33f8e-385">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-386">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-386">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-387">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-388">'Blazor'</span></span>
- <span data-ttu-id="33f8e-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-389">'Identity'</span></span>
- <span data-ttu-id="33f8e-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-391">'Razor'</span></span>
- <span data-ttu-id="33f8e-392">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-393">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-393">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-394">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-395">'Blazor'</span></span>
- <span data-ttu-id="33f8e-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-396">'Identity'</span></span>
- <span data-ttu-id="33f8e-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-398">'Razor'</span></span>
- <span data-ttu-id="33f8e-399">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-400">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-400">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-401">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-402">'Blazor'</span></span>
- <span data-ttu-id="33f8e-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-403">'Identity'</span></span>
- <span data-ttu-id="33f8e-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-405">'Razor'</span></span>
- <span data-ttu-id="33f8e-406">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-407">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-407">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-408">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-409">'Blazor'</span></span>
- <span data-ttu-id="33f8e-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-410">'Identity'</span></span>
- <span data-ttu-id="33f8e-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-412">'Razor'</span></span>
- <span data-ttu-id="33f8e-413">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-414">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-414">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-415">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-416">'Blazor'</span></span>
- <span data-ttu-id="33f8e-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-417">'Identity'</span></span>
- <span data-ttu-id="33f8e-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-419">'Razor'</span></span>
- <span data-ttu-id="33f8e-420">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-421">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-421">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-422">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-423">'Blazor'</span></span>
- <span data-ttu-id="33f8e-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-424">'Identity'</span></span>
- <span data-ttu-id="33f8e-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-426">'Razor'</span></span>
- <span data-ttu-id="33f8e-427">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-428">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-428">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-429">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-430">'Blazor'</span></span>
- <span data-ttu-id="33f8e-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-431">'Identity'</span></span>
- <span data-ttu-id="33f8e-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-433">'Razor'</span></span>
- <span data-ttu-id="33f8e-434">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-435">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-435">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-436">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-437">'Blazor'</span></span>
- <span data-ttu-id="33f8e-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-438">'Identity'</span></span>
- <span data-ttu-id="33f8e-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-440">'Razor'</span></span>
- <span data-ttu-id="33f8e-441">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-442">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-442">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-443">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-444">'Blazor'</span></span>
- <span data-ttu-id="33f8e-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-445">'Identity'</span></span>
- <span data-ttu-id="33f8e-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-447">'Razor'</span></span>
- <span data-ttu-id="33f8e-448">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-449">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-449">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-450">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-451">'Blazor'</span></span>
- <span data-ttu-id="33f8e-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-452">'Identity'</span></span>
- <span data-ttu-id="33f8e-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-454">'Razor'</span></span>
- <span data-ttu-id="33f8e-455">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-456">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-456">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-457">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-458">'Blazor'</span></span>
- <span data-ttu-id="33f8e-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-459">'Identity'</span></span>
- <span data-ttu-id="33f8e-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-461">'Razor'</span></span>
- <span data-ttu-id="33f8e-462">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-463">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-463">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-464">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-465">'Blazor'</span></span>
- <span data-ttu-id="33f8e-466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-466">'Identity'</span></span>
- <span data-ttu-id="33f8e-467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-467">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-468">'Razor'</span></span>
- <span data-ttu-id="33f8e-469">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-470">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-470">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-471">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-472">'Blazor'</span></span>
- <span data-ttu-id="33f8e-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-473">'Identity'</span></span>
- <span data-ttu-id="33f8e-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-475">'Razor'</span></span>
- <span data-ttu-id="33f8e-476">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-476">'SignalR' uid:</span></span> 

<span data-ttu-id="33f8e-477">---------------- | ---Název: ' ASP.NET Core Blazor Další scénáře zabezpečení pro WebAssembly ' Autor: Description: ' Naučte se nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-477">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-478">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-479">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-479">'Blazor'</span></span>
- <span data-ttu-id="33f8e-480">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-480">'Identity'</span></span>
- <span data-ttu-id="33f8e-481">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-481">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-482">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-482">'Razor'</span></span>
- <span data-ttu-id="33f8e-483">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-483">'SignalR' uid:</span></span> 

<span data-ttu-id="33f8e-484">---- | | `authentication/login`           | Spustí operaci přihlášení.</span><span class="sxs-lookup"><span data-stu-id="33f8e-484">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="33f8e-485">| | `authentication/login-callback`  | Zpracovává výsledek jakékoli operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="33f8e-485">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="33f8e-486">| | `authentication/login-failed`    | Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="33f8e-486">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="33f8e-487">| | `authentication/logout`          | Spustí operaci odhlášení.</span><span class="sxs-lookup"><span data-stu-id="33f8e-487">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="33f8e-488">| | `authentication/logout-callback` | Zpracovává výsledek operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="33f8e-488">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="33f8e-489">| | `authentication/logout-failed`   | Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="33f8e-489">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="33f8e-490">| | `authentication/logged-out`      | Indikuje, že se uživatel úspěšně odhlásit.</span><span class="sxs-lookup"><span data-stu-id="33f8e-490">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="33f8e-491">| | `authentication/profile`         | Aktivuje operaci pro úpravu profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="33f8e-491">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="33f8e-492">| | `authentication/register`        | Aktivuje operaci pro registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="33f8e-492">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="33f8e-493">Trasy zobrazené v předchozí tabulce lze konfigurovat prostřednictvím `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` .</span><span class="sxs-lookup"><span data-stu-id="33f8e-493">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="33f8e-494">Při nastavování možností pro poskytování vlastních tras potvrďte, že aplikace má trasu, která zpracovává jednotlivé cesty.</span><span class="sxs-lookup"><span data-stu-id="33f8e-494">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="33f8e-495">V následujícím příkladu jsou všechny cesty s předponou `/security` .</span><span class="sxs-lookup"><span data-stu-id="33f8e-495">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="33f8e-496">`Authentication`součást (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-496">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="33f8e-497">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-497">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="33f8e-498">Pokud požadavek volá zcela různé cesty, nastavte trasy popsané dříve a vykreslete `RemoteAuthenticatorView` s parametrem explicitní akce:</span><span class="sxs-lookup"><span data-stu-id="33f8e-498">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="33f8e-499">Pokud se rozhodnete tak učinit, můžete uživatelské rozhraní přerušit na jiné stránky.</span><span class="sxs-lookup"><span data-stu-id="33f8e-499">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="33f8e-500">Přizpůsobení uživatelského rozhraní ověřování</span><span class="sxs-lookup"><span data-stu-id="33f8e-500">Customize the authentication user interface</span></span>

<span data-ttu-id="33f8e-501">`RemoteAuthenticatorView`obsahuje výchozí sadu částí uživatelského rozhraní pro každý stav ověřování.</span><span class="sxs-lookup"><span data-stu-id="33f8e-501">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="33f8e-502">Každý stav lze přizpůsobit předáním vlastního `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="33f8e-502">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="33f8e-503">K přizpůsobení zobrazeného textu během procesu prvotního přihlášení může změnit následující postup `RemoteAuthenticatorView` .</span><span class="sxs-lookup"><span data-stu-id="33f8e-503">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="33f8e-504">`Authentication`součást (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-504">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="33f8e-505">`RemoteAuthenticatorView`Má jeden fragment, který se dá použít pro jednu trasu ověřování, jak je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="33f8e-505">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="33f8e-506">Trasa</span><span class="sxs-lookup"><span data-stu-id="33f8e-506">Route</span></span>                            | <span data-ttu-id="33f8e-507">Fragment</span><span class="sxs-lookup"><span data-stu-id="33f8e-507">Fragment</span></span>                |
| ---
<span data-ttu-id="33f8e-508">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-508">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-509">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-510">'Blazor'</span></span>
- <span data-ttu-id="33f8e-511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-511">'Identity'</span></span>
- <span data-ttu-id="33f8e-512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-512">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-513">'Razor'</span></span>
- <span data-ttu-id="33f8e-514">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-515">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-515">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-516">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-517">'Blazor'</span></span>
- <span data-ttu-id="33f8e-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-518">'Identity'</span></span>
- <span data-ttu-id="33f8e-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-520">'Razor'</span></span>
- <span data-ttu-id="33f8e-521">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-522">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-522">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-523">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-524">'Blazor'</span></span>
- <span data-ttu-id="33f8e-525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-525">'Identity'</span></span>
- <span data-ttu-id="33f8e-526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-526">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-527">'Razor'</span></span>
- <span data-ttu-id="33f8e-528">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-529">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-529">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-530">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-531">'Blazor'</span></span>
- <span data-ttu-id="33f8e-532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-532">'Identity'</span></span>
- <span data-ttu-id="33f8e-533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-533">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-534">'Razor'</span></span>
- <span data-ttu-id="33f8e-535">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-536">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-536">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-537">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-538">'Blazor'</span></span>
- <span data-ttu-id="33f8e-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-539">'Identity'</span></span>
- <span data-ttu-id="33f8e-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-541">'Razor'</span></span>
- <span data-ttu-id="33f8e-542">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-543">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-543">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-544">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-545">'Blazor'</span></span>
- <span data-ttu-id="33f8e-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-546">'Identity'</span></span>
- <span data-ttu-id="33f8e-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-548">'Razor'</span></span>
- <span data-ttu-id="33f8e-549">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-550">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-550">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-551">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-552">'Blazor'</span></span>
- <span data-ttu-id="33f8e-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-553">'Identity'</span></span>
- <span data-ttu-id="33f8e-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-555">'Razor'</span></span>
- <span data-ttu-id="33f8e-556">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-557">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-557">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-558">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-559">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-559">'Blazor'</span></span>
- <span data-ttu-id="33f8e-560">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-560">'Identity'</span></span>
- <span data-ttu-id="33f8e-561">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-561">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-562">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-562">'Razor'</span></span>
- <span data-ttu-id="33f8e-563">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-563">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-564">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-564">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-565">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-566">'Blazor'</span></span>
- <span data-ttu-id="33f8e-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-567">'Identity'</span></span>
- <span data-ttu-id="33f8e-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-569">'Razor'</span></span>
- <span data-ttu-id="33f8e-570">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-571">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-571">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-572">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-573">'Blazor'</span></span>
- <span data-ttu-id="33f8e-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-574">'Identity'</span></span>
- <span data-ttu-id="33f8e-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-576">'Razor'</span></span>
- <span data-ttu-id="33f8e-577">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-578">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-578">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-579">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-580">'Blazor'</span></span>
- <span data-ttu-id="33f8e-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-581">'Identity'</span></span>
- <span data-ttu-id="33f8e-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-583">'Razor'</span></span>
- <span data-ttu-id="33f8e-584">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-585">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-585">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-586">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-587">'Blazor'</span></span>
- <span data-ttu-id="33f8e-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-588">'Identity'</span></span>
- <span data-ttu-id="33f8e-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-590">'Razor'</span></span>
- <span data-ttu-id="33f8e-591">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-592">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-592">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-593">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-594">'Blazor'</span></span>
- <span data-ttu-id="33f8e-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-595">'Identity'</span></span>
- <span data-ttu-id="33f8e-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-597">'Razor'</span></span>
- <span data-ttu-id="33f8e-598">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-599">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-599">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-600">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-601">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-601">'Blazor'</span></span>
- <span data-ttu-id="33f8e-602">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-602">'Identity'</span></span>
- <span data-ttu-id="33f8e-603">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-603">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-604">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-604">'Razor'</span></span>
- <span data-ttu-id="33f8e-605">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-605">'SignalR' uid:</span></span> 

<span data-ttu-id="33f8e-606">---------------- | ---Název: ' ASP.NET Core Blazor Další scénáře zabezpečení pro WebAssembly ' Autor: Description: ' Naučte se nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-606">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-607">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-608">'Blazor'</span></span>
- <span data-ttu-id="33f8e-609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-609">'Identity'</span></span>
- <span data-ttu-id="33f8e-610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-610">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-611">'Razor'</span></span>
- <span data-ttu-id="33f8e-612">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-613">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-613">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-614">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-615">'Blazor'</span></span>
- <span data-ttu-id="33f8e-616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-616">'Identity'</span></span>
- <span data-ttu-id="33f8e-617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-617">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-618">'Razor'</span></span>
- <span data-ttu-id="33f8e-619">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-620">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-620">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-621">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-622">'Blazor'</span></span>
- <span data-ttu-id="33f8e-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-623">'Identity'</span></span>
- <span data-ttu-id="33f8e-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-625">'Razor'</span></span>
- <span data-ttu-id="33f8e-626">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-627">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-627">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-628">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-629">'Blazor'</span></span>
- <span data-ttu-id="33f8e-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-630">'Identity'</span></span>
- <span data-ttu-id="33f8e-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-632">'Razor'</span></span>
- <span data-ttu-id="33f8e-633">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-634">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-634">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-635">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-636">'Blazor'</span></span>
- <span data-ttu-id="33f8e-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-637">'Identity'</span></span>
- <span data-ttu-id="33f8e-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-639">'Razor'</span></span>
- <span data-ttu-id="33f8e-640">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-641">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-641">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-642">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-643">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-643">'Blazor'</span></span>
- <span data-ttu-id="33f8e-644">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-644">'Identity'</span></span>
- <span data-ttu-id="33f8e-645">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-645">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-646">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-646">'Razor'</span></span>
- <span data-ttu-id="33f8e-647">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-647">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-648">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-648">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-649">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-650">'Blazor'</span></span>
- <span data-ttu-id="33f8e-651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-651">'Identity'</span></span>
- <span data-ttu-id="33f8e-652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-652">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-653">'Razor'</span></span>
- <span data-ttu-id="33f8e-654">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-655">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-655">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-656">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-657">'Blazor'</span></span>
- <span data-ttu-id="33f8e-658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-658">'Identity'</span></span>
- <span data-ttu-id="33f8e-659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-659">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-660">'Razor'</span></span>
- <span data-ttu-id="33f8e-661">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="33f8e-662">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="33f8e-662">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="33f8e-663">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="33f8e-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33f8e-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-664">'Blazor'</span></span>
- <span data-ttu-id="33f8e-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33f8e-665">'Identity'</span></span>
- <span data-ttu-id="33f8e-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33f8e-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="33f8e-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33f8e-667">'Razor'</span></span>
- <span data-ttu-id="33f8e-668">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="33f8e-668">'SignalR' uid:</span></span> 

<span data-ttu-id="33f8e-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="33f8e-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="33f8e-670">Přizpůsobení uživatele</span><span class="sxs-lookup"><span data-stu-id="33f8e-670">Customize the user</span></span>

<span data-ttu-id="33f8e-671">Uživatele navázané na aplikaci je možné přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="33f8e-671">Users bound to the app can be customized.</span></span> <span data-ttu-id="33f8e-672">V následujícím příkladu obdrží všichni ověření uživatelé `amr` deklaraci identity pro každou metodu ověřování uživatele.</span><span class="sxs-lookup"><span data-stu-id="33f8e-672">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="33f8e-673">Vytvořte třídu, která rozšiřuje `RemoteUserAccount` třídu:</span><span class="sxs-lookup"><span data-stu-id="33f8e-673">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="33f8e-674">Vytvořte objekt pro vytváření, který rozšiřuje `AccountClaimsPrincipalFactory<TAccount>` :</span><span class="sxs-lookup"><span data-stu-id="33f8e-674">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="33f8e-675">Zaregistrujte zprostředkovatele ověřování, který se `CustomAccountFactory` používá.</span><span class="sxs-lookup"><span data-stu-id="33f8e-675">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="33f8e-676">Platná jsou následující registrace:</span><span class="sxs-lookup"><span data-stu-id="33f8e-676">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="33f8e-677">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="33f8e-677">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="33f8e-678">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="33f8e-678">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="33f8e-679">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="33f8e-679">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="33f8e-680">Podpora předběžného vykreslování s ověřováním</span><span class="sxs-lookup"><span data-stu-id="33f8e-680">Support prerendering with authentication</span></span>

<span data-ttu-id="33f8e-681">Po použití pokynů v některém z hostovaných Blazor témat aplikace WebAssembly použijte následující pokyny k vytvoření aplikace, která:</span><span class="sxs-lookup"><span data-stu-id="33f8e-681">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="33f8e-682">Předem vykreslí cesty, pro které není nutná autorizace.</span><span class="sxs-lookup"><span data-stu-id="33f8e-682">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="33f8e-683">Nejedná se o cesty PreRender, pro které se vyžaduje autorizace.</span><span class="sxs-lookup"><span data-stu-id="33f8e-683">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="33f8e-684">V rámci třídy klientské aplikace `Program` (*program.cs*) se služba Factoring Common registruje do samostatné metody (například `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="33f8e-684">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="33f8e-685">V serverové aplikaci `Startup.ConfigureServices` Zaregistrujte následující další služby:</span><span class="sxs-lookup"><span data-stu-id="33f8e-685">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="33f8e-686">V metodě serverové aplikace `Startup.Configure` nahraďte `endpoints.MapFallbackToFile("index.html")` `endpoints.MapFallbackToPage("/_Host")` :</span><span class="sxs-lookup"><span data-stu-id="33f8e-686">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="33f8e-687">V serverové aplikaci vytvořte složku *stránky* , pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="33f8e-687">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="33f8e-688">Vytvořte stránku *_Host. cshtml* ve složce *stránek* serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="33f8e-688">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="33f8e-689">Vložte obsah ze souboru *wwwroot/index.html* klientské aplikace do souboru *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="33f8e-689">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="33f8e-690">Aktualizujte obsah souboru:</span><span class="sxs-lookup"><span data-stu-id="33f8e-690">Update the file's contents:</span></span>

* <span data-ttu-id="33f8e-691">Přidejte `@page "_Host"` na začátek souboru.</span><span class="sxs-lookup"><span data-stu-id="33f8e-691">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="33f8e-692">Značku nahraďte `<app>Loading...</app>` následujícím:</span><span class="sxs-lookup"><span data-stu-id="33f8e-692">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="33f8e-693">Možnosti pro hostované aplikace a poskytovatele přihlašovacích údajů třetích stran</span><span class="sxs-lookup"><span data-stu-id="33f8e-693">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="33f8e-694">Při ověřování a autorizaci hostované Blazor aplikace WebAssembly s poskytovatelem třetí strany je k dispozici několik možností pro ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="33f8e-694">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="33f8e-695">Kterou zvolíte, závisí na vašem scénáři.</span><span class="sxs-lookup"><span data-stu-id="33f8e-695">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="33f8e-696">Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="33f8e-696">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="33f8e-697">Ověřování uživatelů pro volání rozhraní API chráněných třetích stran</span><span class="sxs-lookup"><span data-stu-id="33f8e-697">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="33f8e-698">Ověřit uživatele pomocí toku OAuth na straně klienta proti poskytovateli rozhraní API třetí strany:</span><span class="sxs-lookup"><span data-stu-id="33f8e-698">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="33f8e-699">V tomto scénáři:</span><span class="sxs-lookup"><span data-stu-id="33f8e-699">In this scenario:</span></span>

* <span data-ttu-id="33f8e-700">Server, který je hostitelem aplikace, nehraje roli.</span><span class="sxs-lookup"><span data-stu-id="33f8e-700">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="33f8e-701">Rozhraní API na serveru nejde chránit.</span><span class="sxs-lookup"><span data-stu-id="33f8e-701">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="33f8e-702">Aplikace může volat jenom chráněná rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="33f8e-702">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="33f8e-703">Ověřování uživatelů pomocí poskytovatele třetí strany a volání chráněných rozhraní API na hostitelském serveru a třetí straně</span><span class="sxs-lookup"><span data-stu-id="33f8e-703">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="33f8e-704">Nakonfigurujte Identity pomocí poskytovatele přihlášení třetí strany.</span><span class="sxs-lookup"><span data-stu-id="33f8e-704">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="33f8e-705">Získejte tokeny vyžadované pro přístup k rozhraní API třetích stran a uložte je.</span><span class="sxs-lookup"><span data-stu-id="33f8e-705">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="33f8e-706">Když se uživatel přihlásí, Identity shromažďuje v rámci procesu ověřování tokeny a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="33f8e-706">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="33f8e-707">V tomto okamžiku je k dispozici několik přístupů pro volání rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="33f8e-707">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="33f8e-708">Použití přístupového tokenu serveru k načtení přístupového tokenu třetí strany</span><span class="sxs-lookup"><span data-stu-id="33f8e-708">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="33f8e-709">K načtení přístupového tokenu třetí strany z koncového bodu rozhraní API serveru použijte přístupový token vygenerovaný na serveru.</span><span class="sxs-lookup"><span data-stu-id="33f8e-709">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="33f8e-710">Odtud můžete pomocí přístupového tokenu třetí strany volat prostředky rozhraní API třetích stran přímo z Identity klienta.</span><span class="sxs-lookup"><span data-stu-id="33f8e-710">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="33f8e-711">Tento postup nedoporučujeme.</span><span class="sxs-lookup"><span data-stu-id="33f8e-711">We don't recommend this approach.</span></span> <span data-ttu-id="33f8e-712">Tento přístup vyžaduje ošetření přístupového tokenu třetí strany, jako kdyby byl vygenerován pro veřejného klienta.</span><span class="sxs-lookup"><span data-stu-id="33f8e-712">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="33f8e-713">V případech OAuth nemá veřejná aplikace tajný klíč klienta, protože nemůže být důvěryhodná pro bezpečné ukládání tajných kódů a přístupového tokenu se vytvoří pro důvěrného klienta.</span><span class="sxs-lookup"><span data-stu-id="33f8e-713">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="33f8e-714">Důvěrný klient je klient, který má tajný klíč klienta a předpokládá, že bude moci bezpečně ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="33f8e-714">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="33f8e-715">Přístupovému tokenu třetí strany se můžou udělit další obory, které budou provádět citlivé operace na základě skutečnosti, že třetí strana vygenerovala token pro více důvěryhodných klientů.</span><span class="sxs-lookup"><span data-stu-id="33f8e-715">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="33f8e-716">Podobně by se aktualizační tokeny neměly vystavit klientovi, který není důvěryhodný, protože by to tak měl klienta bez omezení přístupu, pokud se na ně neukládají jiná omezení.</span><span class="sxs-lookup"><span data-stu-id="33f8e-716">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="33f8e-717">Volání rozhraní API z klienta na rozhraní API serveru za účelem volání rozhraní API třetích stran</span><span class="sxs-lookup"><span data-stu-id="33f8e-717">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="33f8e-718">Naplňte volání rozhraní API z klienta na serverové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="33f8e-718">Make an API call from the client to the server API.</span></span> <span data-ttu-id="33f8e-719">Ze serveru načtěte přístupový token pro prostředek rozhraní API třetí strany a vydejte jakékoli volání, které je potřeba.</span><span class="sxs-lookup"><span data-stu-id="33f8e-719">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="33f8e-720">I když tento přístup vyžaduje další síťové směrování prostřednictvím serveru za účelem volání rozhraní API třetí strany, má za následek bezpečnější prostředí:</span><span class="sxs-lookup"><span data-stu-id="33f8e-720">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="33f8e-721">Server může ukládat aktualizační tokeny a zajistit, aby aplikace neztratila přístup k prostředkům třetích stran.</span><span class="sxs-lookup"><span data-stu-id="33f8e-721">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="33f8e-722">Aplikace nemůže zajímat přístupové tokeny ze serveru, který může obsahovat citlivá oprávnění.</span><span class="sxs-lookup"><span data-stu-id="33f8e-722">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="33f8e-723">Použití koncových bodů Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="33f8e-723">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="33f8e-724">Knihovna a šablony ověřování Blazor používají koncové body Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="33f8e-724">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="33f8e-725">Pokud chcete použít koncový bod v 2.0, nakonfigurujte možnost nosiče JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="33f8e-725">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="33f8e-726">V následujícím příkladu je AAD nakonfigurováno v 2.0 připojením `v2.0` segmentu k `Authority` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="33f8e-726">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the `Authority` property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="33f8e-727">Případně může být nastavení provedeno v souboru nastavení aplikace (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="33f8e-727">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="33f8e-728">Pokud se označení segmentu pro autoritu nehodí pro poskytovatele OIDC aplikace, jako je třeba u jiných poskytovatelů než AAD, nastavte `Authority` vlastnost přímo.</span><span class="sxs-lookup"><span data-stu-id="33f8e-728">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="33f8e-729">Buď nastavte vlastnost v `JwtBearerOptions` nebo v souboru nastavení aplikace s `Authority` klíčem.</span><span class="sxs-lookup"><span data-stu-id="33f8e-729">Either set the property in `JwtBearerOptions` or in the app settings file with the `Authority` key.</span></span>

<span data-ttu-id="33f8e-730">Seznam deklarací identity v tokenu ID se mění pro koncové body verze 2.0.</span><span class="sxs-lookup"><span data-stu-id="33f8e-730">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="33f8e-731">Další informace najdete v tématu [Proč aktualizace pro Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="33f8e-731">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
