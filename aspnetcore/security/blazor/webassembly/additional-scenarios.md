---
<span data-ttu-id="cdc10-101">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-103">'Blazor'</span></span>
- <span data-ttu-id="cdc10-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-104">'Identity'</span></span>
- <span data-ttu-id="cdc10-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-106">'Razor'</span></span>
- <span data-ttu-id="cdc10-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="cdc10-108">ASP.NET Core Blazor Další scénáře zabezpečení pro WebAssembly</span><span class="sxs-lookup"><span data-stu-id="cdc10-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="cdc10-109">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="cdc10-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="cdc10-110">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="cdc10-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="cdc10-111"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Službu lze použít s nástrojem <xref:System.Net.Http.HttpClient> k připojení přístupových tokenů k odchozím žádostem.</span><span class="sxs-lookup"><span data-stu-id="cdc10-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="cdc10-112">Tokeny se získávají pomocí existující <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> služby.</span><span class="sxs-lookup"><span data-stu-id="cdc10-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="cdc10-113">Pokud token nelze získat, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="cdc10-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="cdc10-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>má <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> metodu, která se dá použít k navigaci uživatele na poskytovatele identity za účelem získání nového tokenu.</span><span class="sxs-lookup"><span data-stu-id="cdc10-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="cdc10-115"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Pomocí metody lze konfigurovat pomocí autorizovaných adres URL, oborů a návratové adresy URL <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="cdc10-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="cdc10-116">V následujícím příkladu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> nakonfiguruje <xref:System.Net.Http.HttpClient> v `Program.Main` (*program.cs*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="cdc10-117">Pro usnadnění práce <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> je součástí předem nakonfigurovaná základní adresa aplikace jako autorizovaná adresa URL.</span><span class="sxs-lookup"><span data-stu-id="cdc10-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="cdc10-118">Šablony WebAssembly s povoleným ověřováním Blazor teď používají <xref:System.Net.Http.IHttpClientFactory> v projektu rozhraní API serveru k nastavení <xref:System.Net.Http.HttpClient> pomocí <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="cdc10-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="cdc10-119">Tam, kde je klient vytvořen pomocí <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> v předchozím příkladu, se <xref:System.Net.Http.HttpClient> dodávají instance, které zahrnují přístupové tokeny při vytváření požadavků na serverový projekt.</span><span class="sxs-lookup"><span data-stu-id="cdc10-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="cdc10-120">Nakonfigurovaná <xref:System.Net.Http.HttpClient> se pak používá k provádění autorizovaných požadavků pomocí jednoduchého vzoru [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="cdc10-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="cdc10-121">`FetchData`součást (*Pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="cdc10-122">Typové HttpClient</span><span class="sxs-lookup"><span data-stu-id="cdc10-122">Typed HttpClient</span></span>

<span data-ttu-id="cdc10-123">Je možné definovat zadaného klienta, který zpracovává všechny aspekty získání HTTP a tokenu v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="cdc10-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="cdc10-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="cdc10-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="cdc10-125">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="cdc10-126">`FetchData`součást (*Pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="cdc10-127">Konfigurace obslužné rutiny HttpClient</span><span class="sxs-lookup"><span data-stu-id="cdc10-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="cdc10-128">Obslužná rutina může být dále nakonfigurována <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="cdc10-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="cdc10-129">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="cdc10-130">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="cdc10-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="cdc10-131">Pokud Blazor aplikace WebAssembly obvykle používá zabezpečené výchozí nastavení <xref:System.Net.Http.HttpClient> , může aplikace také provést neověřené nebo neautorizované požadavky webového rozhraní API konfigurací pojmenovaného <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="cdc10-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="cdc10-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="cdc10-133">Předchozí registrace je kromě existující zabezpečené výchozí <xref:System.Net.Http.HttpClient> registrace.</span><span class="sxs-lookup"><span data-stu-id="cdc10-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="cdc10-134">Komponenta vytvoří <xref:System.Net.Http.HttpClient> z <xref:System.Net.Http.IHttpClientFactory> k provedení neověřených nebo neautorizovaných požadavků:</span><span class="sxs-lookup"><span data-stu-id="cdc10-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="cdc10-135">Řadič v rozhraní API serveru `WeatherForecastNoAuthenticationController` pro předchozí příklad není označený [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="cdc10-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="cdc10-136">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="cdc10-136">Request additional access tokens</span></span>

<span data-ttu-id="cdc10-137">Přístupové tokeny lze získat ručně voláním `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="cdc10-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="cdc10-138">V následujícím příkladu jsou vyžadovány další Azure Active Directory (AAD) Microsoft Graph obory rozhraní API pro čtení uživatelských dat a odesílání e-mailů.</span><span class="sxs-lookup"><span data-stu-id="cdc10-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="cdc10-139">Po přidání oprávnění rozhraní API Microsoft Graph na portálu Azure AAD jsou další obory nakonfigurované v klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cdc10-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="cdc10-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="cdc10-141">`IAccessTokenProvider.RequestToken`Metoda poskytuje přetížení, které umožňuje aplikaci zřídit přístupový token s danou sadou oborů.</span><span class="sxs-lookup"><span data-stu-id="cdc10-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="cdc10-142">V Razor součásti:</span><span class="sxs-lookup"><span data-stu-id="cdc10-142">In a Razor component:</span></span>

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

<span data-ttu-id="cdc10-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Vrátí</span><span class="sxs-lookup"><span data-stu-id="cdc10-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="cdc10-144">`true`s nástrojem `token` pro použití.</span><span class="sxs-lookup"><span data-stu-id="cdc10-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="cdc10-145">`false`Pokud se token nenačte.</span><span class="sxs-lookup"><span data-stu-id="cdc10-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="cdc10-146">HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení</span><span class="sxs-lookup"><span data-stu-id="cdc10-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="cdc10-147">Při spuštění na WebAssembly v Blazor aplikaci WebAssembly [HttpClient](xref:fundamentals/http-requests) a dá se <xref:System.Net.Http.HttpRequestMessage> použít k přizpůsobení požadavků.</span><span class="sxs-lookup"><span data-stu-id="cdc10-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="cdc10-148">Můžete například zadat metodu HTTP a hlavičku požadavku.</span><span class="sxs-lookup"><span data-stu-id="cdc10-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="cdc10-149">Následující komponenta vytvoří požadavek na `POST` koncový bod rozhraní API seznamu na serveru a zobrazí tělo odpovědi:</span><span class="sxs-lookup"><span data-stu-id="cdc10-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="cdc10-150">Implementace rozhraní .NET WebAssembly pro <xref:System.Net.Http.HttpClient> používá [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="cdc10-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="cdc10-151">Načtení umožňuje nakonfigurovat několik [možností specifických pro požadavky](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="cdc10-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="cdc10-152">Možnosti požadavku HTTP Fetch lze konfigurovat pomocí <xref:System.Net.Http.HttpRequestMessage> rozšiřujících metod, které jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="cdc10-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="cdc10-153">Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="cdc10-153">Extension method</span></span> | <span data-ttu-id="cdc10-154">Načíst vlastnost žádosti</span><span class="sxs-lookup"><span data-stu-id="cdc10-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="cdc10-155">přihlašovací údaje</span><span class="sxs-lookup"><span data-stu-id="cdc10-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="cdc10-156">uchovávat</span><span class="sxs-lookup"><span data-stu-id="cdc10-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="cdc10-157">Mode</span><span class="sxs-lookup"><span data-stu-id="cdc10-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="cdc10-158">způsobilost</span><span class="sxs-lookup"><span data-stu-id="cdc10-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="cdc10-159">Další možnosti můžete nastavit pomocí obecnější <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="cdc10-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="cdc10-160">Odpověď HTTP je obvykle ukládána do vyrovnávací paměti v Blazor aplikaci WebAssembly, aby umožnila podporu pro čtení v obsahu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="cdc10-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="cdc10-161">Pokud chcete povolit podporu pro streamování odpovědí, použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metodu rozšíření v žádosti.</span><span class="sxs-lookup"><span data-stu-id="cdc10-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="cdc10-162">Pokud chcete do žádosti o více zdrojů zahrnout přihlašovací údaje, použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="cdc10-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="cdc10-163">Další informace o možnostech načtení rozhraní API naleznete v tématu [MDN web Docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="cdc10-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="cdc10-164">Při odesílání přihlašovacích údajů (souborů cookie autorizace/hlaviček) na žádostech CORS `Authorization` musí být záhlaví povoleno zásadami CORS.</span><span class="sxs-lookup"><span data-stu-id="cdc10-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="cdc10-165">Následující zásady zahrnují konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="cdc10-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="cdc10-166">Původ žádosti ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="cdc10-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="cdc10-167">Libovolná metoda (příkaz).</span><span class="sxs-lookup"><span data-stu-id="cdc10-167">Any method (verb).</span></span>
* <span data-ttu-id="cdc10-168">`Content-Type`a `Authorization` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="cdc10-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="cdc10-169">Chcete-li pro vlastní hlavičku (například `x-custom-header` ), uveďte záhlaví při volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="cdc10-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="cdc10-170">Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta ( `credentials` vlastnost je nastavena na hodnotu `include` ).</span><span class="sxs-lookup"><span data-stu-id="cdc10-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="cdc10-171">Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="cdc10-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="cdc10-172">Zpracování chyb požadavků na tokeny</span><span class="sxs-lookup"><span data-stu-id="cdc10-172">Handle token request errors</span></span>

<span data-ttu-id="cdc10-173">Když jedna stránková aplikace (SPA) ověřuje uživatele pomocí funkce Open ID Connect (OIDC), je stav ověřování udržován místně v zabezpečeném uživatelském rozhraní (SPA) a ve Identity zprostředkovateli (IP) ve formě souboru cookie relace, který je nastaven jako výsledek uživatele, který poskytuje své přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="cdc10-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="cdc10-174">Tokeny, které jsou pro uživatele vysílané, jsou obvykle platné po krátkou dobu přibližně jedna hodina, takže klientská aplikace musí pravidelně načítat nové tokeny.</span><span class="sxs-lookup"><span data-stu-id="cdc10-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="cdc10-175">V opačném případě se uživatel odhlásí po vypršení platnosti udělených tokenů.</span><span class="sxs-lookup"><span data-stu-id="cdc10-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="cdc10-176">Ve většině případů můžou klienti OIDC zřizovat nové tokeny, aniž by museli znovu ověřovat uživatele díky stavu ověřování nebo "relaci", která se udržuje v rámci IP adresy.</span><span class="sxs-lookup"><span data-stu-id="cdc10-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="cdc10-177">V některých případech může klient získat token bez zásahu uživatele, například pokud z nějakého důvodu se uživatel výslovně odhlásí z IP adresy.</span><span class="sxs-lookup"><span data-stu-id="cdc10-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="cdc10-178">K tomuto scénáři dochází, pokud uživatel navštíví `https://login.microsoftonline.com` a odhlásí. V těchto scénářích aplikace neví hned, že se uživatel odhlásil. Libovolný token, který může klient obsahovat, již nemusí být platný.</span><span class="sxs-lookup"><span data-stu-id="cdc10-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="cdc10-179">Klient navíc nemůže zřídit nový token bez zásahu uživatele po vypršení platnosti tohoto tokenu.</span><span class="sxs-lookup"><span data-stu-id="cdc10-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="cdc10-180">Tyto scénáře nejsou specifické pro ověřování založené na tokenech.</span><span class="sxs-lookup"><span data-stu-id="cdc10-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="cdc10-181">Jsou součástí charakteru jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="cdc10-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="cdc10-182">ZABEZPEČENÉ ověřování pomocí souborů cookie také nedokáže volat rozhraní API serveru, pokud je soubor cookie ověření odebrán.</span><span class="sxs-lookup"><span data-stu-id="cdc10-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="cdc10-183">Když aplikace provede volání rozhraní API k chráněným prostředkům, musíte mít na paměti následující informace:</span><span class="sxs-lookup"><span data-stu-id="cdc10-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="cdc10-184">Pro zřízení nového přístupového tokenu pro volání rozhraní API může být uživatel požádán o ověření znovu.</span><span class="sxs-lookup"><span data-stu-id="cdc10-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="cdc10-185">I v případě, že má klient token, který je pravděpodobně platný, volání serveru může selhat, protože byl token odvolán uživatelem.</span><span class="sxs-lookup"><span data-stu-id="cdc10-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="cdc10-186">Když aplikace požaduje token, existují dva možné výsledky:</span><span class="sxs-lookup"><span data-stu-id="cdc10-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="cdc10-187">Požadavek je úspěšný a aplikace má platný token.</span><span class="sxs-lookup"><span data-stu-id="cdc10-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="cdc10-188">Požadavek se nezdařil a aplikace musí znovu ověřit uživatele, aby získal nový token.</span><span class="sxs-lookup"><span data-stu-id="cdc10-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="cdc10-189">Pokud se žádost o token nezdařila, musíte se rozhodnout, zda chcete před provedením přesměrování Uložit aktuální stav.</span><span class="sxs-lookup"><span data-stu-id="cdc10-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="cdc10-190">Existuje několik přístupů se zvýšenými úrovněmi složitosti:</span><span class="sxs-lookup"><span data-stu-id="cdc10-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="cdc10-191">Uloží aktuální stav stránky do úložiště relace.</span><span class="sxs-lookup"><span data-stu-id="cdc10-191">Store the current page state in session storage.</span></span> <span data-ttu-id="cdc10-192">Během [události životního cyklu OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) ověřte, zda je možné obnovit stav, než budete pokračovat.</span><span class="sxs-lookup"><span data-stu-id="cdc10-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="cdc10-193">Přidejte parametr řetězce dotazu a použijte ho jako způsob, jak aplikaci signalizovat, že potřebuje znovu Hydrate dříve uložený stav.</span><span class="sxs-lookup"><span data-stu-id="cdc10-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="cdc10-194">Přidejte parametr řetězce dotazu s jedinečným identifikátorem pro ukládání dat v úložišti relace bez rizikových kolizí s ostatními položkami.</span><span class="sxs-lookup"><span data-stu-id="cdc10-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="cdc10-195">Následující příklad ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="cdc10-195">The following example shows how to:</span></span>

* <span data-ttu-id="cdc10-196">Zachovat stav před přesměrováním na přihlašovací stránku</span><span class="sxs-lookup"><span data-stu-id="cdc10-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="cdc10-197">Obnovte předchozí stav po ověření pomocí parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="cdc10-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="cdc10-198">Uložení stavu aplikace před operací ověřování</span><span class="sxs-lookup"><span data-stu-id="cdc10-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="cdc10-199">Během operace ověřování existují případy, kdy chcete uložit stav aplikace, než se prohlížeč přesměruje na IP adresu.</span><span class="sxs-lookup"><span data-stu-id="cdc10-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="cdc10-200">To může být případ, kdy používáte něco jako kontejner stavu a chcete obnovit stav po úspěšném ověření.</span><span class="sxs-lookup"><span data-stu-id="cdc10-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="cdc10-201">Vlastní objekt stavu ověřování můžete použít k zachování stavu specifického pro aplikaci nebo odkaz na něj a obnovení tohoto stavu po úspěšném dokončení operace ověřování.</span><span class="sxs-lookup"><span data-stu-id="cdc10-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="cdc10-202">`Authentication`součást (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

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

## <a name="customize-app-routes"></a><span data-ttu-id="cdc10-203">Přizpůsobení směrování aplikací</span><span class="sxs-lookup"><span data-stu-id="cdc10-203">Customize app routes</span></span>

<span data-ttu-id="cdc10-204">Ve výchozím nastavení používá knihovna [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) Library trasy, které jsou uvedeny v následující tabulce, pro reprezentaci různých stavů ověřování.</span><span class="sxs-lookup"><span data-stu-id="cdc10-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="cdc10-205">Trasa</span><span class="sxs-lookup"><span data-stu-id="cdc10-205">Route</span></span>                            | <span data-ttu-id="cdc10-206">Účel</span><span class="sxs-lookup"><span data-stu-id="cdc10-206">Purpose</span></span> |
| ---
<span data-ttu-id="cdc10-207">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-208">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-209">'Blazor'</span></span>
- <span data-ttu-id="cdc10-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-210">'Identity'</span></span>
- <span data-ttu-id="cdc10-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-212">'Razor'</span></span>
- <span data-ttu-id="cdc10-213">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-214">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-215">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-216">'Blazor'</span></span>
- <span data-ttu-id="cdc10-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-217">'Identity'</span></span>
- <span data-ttu-id="cdc10-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-219">'Razor'</span></span>
- <span data-ttu-id="cdc10-220">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-221">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-222">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-223">'Blazor'</span></span>
- <span data-ttu-id="cdc10-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-224">'Identity'</span></span>
- <span data-ttu-id="cdc10-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-226">'Razor'</span></span>
- <span data-ttu-id="cdc10-227">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-228">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-229">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-230">'Blazor'</span></span>
- <span data-ttu-id="cdc10-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-231">'Identity'</span></span>
- <span data-ttu-id="cdc10-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-233">'Razor'</span></span>
- <span data-ttu-id="cdc10-234">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-235">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-236">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-237">'Blazor'</span></span>
- <span data-ttu-id="cdc10-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-238">'Identity'</span></span>
- <span data-ttu-id="cdc10-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-240">'Razor'</span></span>
- <span data-ttu-id="cdc10-241">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-242">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-243">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-244">'Blazor'</span></span>
- <span data-ttu-id="cdc10-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-245">'Identity'</span></span>
- <span data-ttu-id="cdc10-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-247">'Razor'</span></span>
- <span data-ttu-id="cdc10-248">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-249">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-250">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-251">'Blazor'</span></span>
- <span data-ttu-id="cdc10-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-252">'Identity'</span></span>
- <span data-ttu-id="cdc10-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-254">'Razor'</span></span>
- <span data-ttu-id="cdc10-255">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-256">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-257">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-258">'Blazor'</span></span>
- <span data-ttu-id="cdc10-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-259">'Identity'</span></span>
- <span data-ttu-id="cdc10-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-261">'Razor'</span></span>
- <span data-ttu-id="cdc10-262">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-263">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-264">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-265">'Blazor'</span></span>
- <span data-ttu-id="cdc10-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-266">'Identity'</span></span>
- <span data-ttu-id="cdc10-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-268">'Razor'</span></span>
- <span data-ttu-id="cdc10-269">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-270">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-271">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-272">'Blazor'</span></span>
- <span data-ttu-id="cdc10-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-273">'Identity'</span></span>
- <span data-ttu-id="cdc10-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-275">'Razor'</span></span>
- <span data-ttu-id="cdc10-276">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-277">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-278">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-279">'Blazor'</span></span>
- <span data-ttu-id="cdc10-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-280">'Identity'</span></span>
- <span data-ttu-id="cdc10-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-282">'Razor'</span></span>
- <span data-ttu-id="cdc10-283">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-284">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-285">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-286">'Blazor'</span></span>
- <span data-ttu-id="cdc10-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-287">'Identity'</span></span>
- <span data-ttu-id="cdc10-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-289">'Razor'</span></span>
- <span data-ttu-id="cdc10-290">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-291">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-292">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-293">'Blazor'</span></span>
- <span data-ttu-id="cdc10-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-294">'Identity'</span></span>
- <span data-ttu-id="cdc10-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-296">'Razor'</span></span>
- <span data-ttu-id="cdc10-297">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-298">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-299">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-300">'Blazor'</span></span>
- <span data-ttu-id="cdc10-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-301">'Identity'</span></span>
- <span data-ttu-id="cdc10-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-303">'Razor'</span></span>
- <span data-ttu-id="cdc10-304">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-304">'SignalR' uid:</span></span> 

<span data-ttu-id="cdc10-305">---------------- | ---Název: ' ASP.NET Core Blazor Další scénáře zabezpečení pro WebAssembly ' Autor: Description: ' Naučte se nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-306">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-307">'Blazor'</span></span>
- <span data-ttu-id="cdc10-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-308">'Identity'</span></span>
- <span data-ttu-id="cdc10-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-310">'Razor'</span></span>
- <span data-ttu-id="cdc10-311">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-311">'SignalR' uid:</span></span> 

<span data-ttu-id="cdc10-312">---- | | `authentication/login`           | Spustí operaci přihlášení.</span><span class="sxs-lookup"><span data-stu-id="cdc10-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="cdc10-313">| | `authentication/login-callback`  | Zpracovává výsledek jakékoli operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="cdc10-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="cdc10-314">| | `authentication/login-failed`    | Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="cdc10-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="cdc10-315">| | `authentication/logout`          | Spustí operaci odhlášení.</span><span class="sxs-lookup"><span data-stu-id="cdc10-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="cdc10-316">| | `authentication/logout-callback` | Zpracovává výsledek operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="cdc10-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="cdc10-317">| | `authentication/logout-failed`   | Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="cdc10-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="cdc10-318">| | `authentication/logged-out`      | Indikuje, že se uživatel úspěšně odhlásit.</span><span class="sxs-lookup"><span data-stu-id="cdc10-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="cdc10-319">| | `authentication/profile`         | Aktivuje operaci pro úpravu profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="cdc10-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="cdc10-320">| | `authentication/register`        | Aktivuje operaci pro registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="cdc10-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="cdc10-321">Trasy zobrazené v předchozí tabulce lze konfigurovat prostřednictvím <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="cdc10-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="cdc10-322">Při nastavování možností pro poskytování vlastních tras potvrďte, že aplikace má trasu, která zpracovává jednotlivé cesty.</span><span class="sxs-lookup"><span data-stu-id="cdc10-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="cdc10-323">V následujícím příkladu jsou všechny cesty s předponou `/security` .</span><span class="sxs-lookup"><span data-stu-id="cdc10-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="cdc10-324">`Authentication`součást (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="cdc10-325">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-325">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="cdc10-326">Pokud požadavek volá zcela různé cesty, nastavte trasy popsané dříve a vykreslete <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> s parametrem explicitní akce:</span><span class="sxs-lookup"><span data-stu-id="cdc10-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="cdc10-327">Pokud se rozhodnete tak učinit, můžete uživatelské rozhraní přerušit na jiné stránky.</span><span class="sxs-lookup"><span data-stu-id="cdc10-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="cdc10-328">Přizpůsobení uživatelského rozhraní ověřování</span><span class="sxs-lookup"><span data-stu-id="cdc10-328">Customize the authentication user interface</span></span>

<span data-ttu-id="cdc10-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>obsahuje výchozí sadu částí uživatelského rozhraní pro každý stav ověřování.</span><span class="sxs-lookup"><span data-stu-id="cdc10-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="cdc10-330">Každý stav lze přizpůsobit předáním vlastního <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="cdc10-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="cdc10-331">K přizpůsobení zobrazeného textu během procesu prvotního přihlášení může změnit následující postup <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> .</span><span class="sxs-lookup"><span data-stu-id="cdc10-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="cdc10-332">`Authentication`součást (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="cdc10-333"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Má jeden fragment, který se dá použít pro jednu trasu ověřování, jak je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="cdc10-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="cdc10-334">Trasa</span><span class="sxs-lookup"><span data-stu-id="cdc10-334">Route</span></span>                            | <span data-ttu-id="cdc10-335">Fragment</span><span class="sxs-lookup"><span data-stu-id="cdc10-335">Fragment</span></span>                |
| ---
<span data-ttu-id="cdc10-336">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-337">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-338">'Blazor'</span></span>
- <span data-ttu-id="cdc10-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-339">'Identity'</span></span>
- <span data-ttu-id="cdc10-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-341">'Razor'</span></span>
- <span data-ttu-id="cdc10-342">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-343">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-344">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-345">'Blazor'</span></span>
- <span data-ttu-id="cdc10-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-346">'Identity'</span></span>
- <span data-ttu-id="cdc10-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-348">'Razor'</span></span>
- <span data-ttu-id="cdc10-349">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-350">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-351">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-352">'Blazor'</span></span>
- <span data-ttu-id="cdc10-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-353">'Identity'</span></span>
- <span data-ttu-id="cdc10-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-355">'Razor'</span></span>
- <span data-ttu-id="cdc10-356">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-357">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-358">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-359">'Blazor'</span></span>
- <span data-ttu-id="cdc10-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-360">'Identity'</span></span>
- <span data-ttu-id="cdc10-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-362">'Razor'</span></span>
- <span data-ttu-id="cdc10-363">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-364">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-365">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-366">'Blazor'</span></span>
- <span data-ttu-id="cdc10-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-367">'Identity'</span></span>
- <span data-ttu-id="cdc10-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-369">'Razor'</span></span>
- <span data-ttu-id="cdc10-370">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-371">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-372">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-373">'Blazor'</span></span>
- <span data-ttu-id="cdc10-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-374">'Identity'</span></span>
- <span data-ttu-id="cdc10-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-376">'Razor'</span></span>
- <span data-ttu-id="cdc10-377">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-378">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-379">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-380">'Blazor'</span></span>
- <span data-ttu-id="cdc10-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-381">'Identity'</span></span>
- <span data-ttu-id="cdc10-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-383">'Razor'</span></span>
- <span data-ttu-id="cdc10-384">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-385">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-386">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-387">'Blazor'</span></span>
- <span data-ttu-id="cdc10-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-388">'Identity'</span></span>
- <span data-ttu-id="cdc10-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-390">'Razor'</span></span>
- <span data-ttu-id="cdc10-391">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-392">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-393">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-394">'Blazor'</span></span>
- <span data-ttu-id="cdc10-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-395">'Identity'</span></span>
- <span data-ttu-id="cdc10-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-397">'Razor'</span></span>
- <span data-ttu-id="cdc10-398">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-399">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-400">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-401">'Blazor'</span></span>
- <span data-ttu-id="cdc10-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-402">'Identity'</span></span>
- <span data-ttu-id="cdc10-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-404">'Razor'</span></span>
- <span data-ttu-id="cdc10-405">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-406">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-407">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-408">'Blazor'</span></span>
- <span data-ttu-id="cdc10-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-409">'Identity'</span></span>
- <span data-ttu-id="cdc10-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-411">'Razor'</span></span>
- <span data-ttu-id="cdc10-412">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-413">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-414">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-415">'Blazor'</span></span>
- <span data-ttu-id="cdc10-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-416">'Identity'</span></span>
- <span data-ttu-id="cdc10-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-418">'Razor'</span></span>
- <span data-ttu-id="cdc10-419">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-420">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-421">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-422">'Blazor'</span></span>
- <span data-ttu-id="cdc10-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-423">'Identity'</span></span>
- <span data-ttu-id="cdc10-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-425">'Razor'</span></span>
- <span data-ttu-id="cdc10-426">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-427">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-428">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-429">'Blazor'</span></span>
- <span data-ttu-id="cdc10-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-430">'Identity'</span></span>
- <span data-ttu-id="cdc10-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-432">'Razor'</span></span>
- <span data-ttu-id="cdc10-433">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-433">'SignalR' uid:</span></span> 

<span data-ttu-id="cdc10-434">---------------- | ---Název: ' ASP.NET Core Blazor Další scénáře zabezpečení pro WebAssembly ' Autor: Description: ' Naučte se nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-435">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-436">'Blazor'</span></span>
- <span data-ttu-id="cdc10-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-437">'Identity'</span></span>
- <span data-ttu-id="cdc10-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-439">'Razor'</span></span>
- <span data-ttu-id="cdc10-440">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-441">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-442">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-443">'Blazor'</span></span>
- <span data-ttu-id="cdc10-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-444">'Identity'</span></span>
- <span data-ttu-id="cdc10-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-446">'Razor'</span></span>
- <span data-ttu-id="cdc10-447">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-448">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-449">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-450">'Blazor'</span></span>
- <span data-ttu-id="cdc10-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-451">'Identity'</span></span>
- <span data-ttu-id="cdc10-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-453">'Razor'</span></span>
- <span data-ttu-id="cdc10-454">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-455">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-456">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-457">'Blazor'</span></span>
- <span data-ttu-id="cdc10-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-458">'Identity'</span></span>
- <span data-ttu-id="cdc10-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-460">'Razor'</span></span>
- <span data-ttu-id="cdc10-461">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-462">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-463">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-464">'Blazor'</span></span>
- <span data-ttu-id="cdc10-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-465">'Identity'</span></span>
- <span data-ttu-id="cdc10-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-467">'Razor'</span></span>
- <span data-ttu-id="cdc10-468">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-469">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-470">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-471">'Blazor'</span></span>
- <span data-ttu-id="cdc10-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-472">'Identity'</span></span>
- <span data-ttu-id="cdc10-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-474">'Razor'</span></span>
- <span data-ttu-id="cdc10-475">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-476">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-477">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-478">'Blazor'</span></span>
- <span data-ttu-id="cdc10-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-479">'Identity'</span></span>
- <span data-ttu-id="cdc10-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-481">'Razor'</span></span>
- <span data-ttu-id="cdc10-482">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-483">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-484">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-485">'Blazor'</span></span>
- <span data-ttu-id="cdc10-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-486">'Identity'</span></span>
- <span data-ttu-id="cdc10-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-488">'Razor'</span></span>
- <span data-ttu-id="cdc10-489">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdc10-490">title: ' ASP.NET Core Blazor dalších scénářů zabezpečení pro WebAssembly ' Autor: Description: ' Zjistěte, jak nakonfigurovat Blazor WebAssembly pro další scénáře zabezpečení. '</span><span class="sxs-lookup"><span data-stu-id="cdc10-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="cdc10-491">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdc10-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdc10-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-492">'Blazor'</span></span>
- <span data-ttu-id="cdc10-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdc10-493">'Identity'</span></span>
- <span data-ttu-id="cdc10-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdc10-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdc10-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdc10-495">'Razor'</span></span>
- <span data-ttu-id="cdc10-496">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="cdc10-496">'SignalR' uid:</span></span> 

<span data-ttu-id="cdc10-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="cdc10-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="cdc10-498">Přizpůsobení uživatele</span><span class="sxs-lookup"><span data-stu-id="cdc10-498">Customize the user</span></span>

<span data-ttu-id="cdc10-499">Uživatele navázané na aplikaci je možné přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="cdc10-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="cdc10-500">V následujícím příkladu obdrží všichni ověření uživatelé `amr` deklaraci identity pro každou metodu ověřování uživatele.</span><span class="sxs-lookup"><span data-stu-id="cdc10-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="cdc10-501">Vytvořte třídu, která rozšiřuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> třídu:</span><span class="sxs-lookup"><span data-stu-id="cdc10-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="cdc10-502">Vytvořte objekt pro vytváření, který rozšiřuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="cdc10-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="cdc10-503">Zaregistrujte zprostředkovatele ověřování, který se `CustomAccountFactory` používá.</span><span class="sxs-lookup"><span data-stu-id="cdc10-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="cdc10-504">Platná jsou následující registrace:</span><span class="sxs-lookup"><span data-stu-id="cdc10-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="cdc10-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="cdc10-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="cdc10-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="cdc10-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="cdc10-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="cdc10-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="cdc10-508">Podpora předběžného vykreslování s ověřováním</span><span class="sxs-lookup"><span data-stu-id="cdc10-508">Support prerendering with authentication</span></span>

<span data-ttu-id="cdc10-509">Po použití pokynů v některém z hostovaných Blazor témat aplikace WebAssembly použijte následující pokyny k vytvoření aplikace, která:</span><span class="sxs-lookup"><span data-stu-id="cdc10-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="cdc10-510">Předem vykreslí cesty, pro které není nutná autorizace.</span><span class="sxs-lookup"><span data-stu-id="cdc10-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="cdc10-511">Nejedná se o cesty PreRender, pro které se vyžaduje autorizace.</span><span class="sxs-lookup"><span data-stu-id="cdc10-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="cdc10-512">V rámci třídy klientské aplikace `Program` (*program.cs*) se služba Factoring Common registruje do samostatné metody (například `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="cdc10-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="cdc10-513">V serverové aplikaci `Startup.ConfigureServices` Zaregistrujte následující další služby:</span><span class="sxs-lookup"><span data-stu-id="cdc10-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="cdc10-514">V metodě serverové aplikace `Startup.Configure` nahraďte [koncové body. MapFallbackToFile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) s [koncovými body. MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="cdc10-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="cdc10-515">V serverové aplikaci vytvořte složku *stránky* , pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="cdc10-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="cdc10-516">Vytvořte stránku *_Host. cshtml* ve složce *stránek* serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="cdc10-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="cdc10-517">Vložte obsah ze souboru *wwwroot/index.html* klientské aplikace do souboru *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cdc10-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="cdc10-518">Aktualizujte obsah souboru:</span><span class="sxs-lookup"><span data-stu-id="cdc10-518">Update the file's contents:</span></span>

* <span data-ttu-id="cdc10-519">Přidejte `@page "_Host"` na začátek souboru.</span><span class="sxs-lookup"><span data-stu-id="cdc10-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="cdc10-520">Značku nahraďte `<app>Loading...</app>` následujícím:</span><span class="sxs-lookup"><span data-stu-id="cdc10-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="cdc10-521">Možnosti pro hostované aplikace a poskytovatele přihlašovacích údajů třetích stran</span><span class="sxs-lookup"><span data-stu-id="cdc10-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="cdc10-522">Při ověřování a autorizaci hostované Blazor aplikace WebAssembly s poskytovatelem třetí strany je k dispozici několik možností pro ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="cdc10-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="cdc10-523">Kterou zvolíte, závisí na vašem scénáři.</span><span class="sxs-lookup"><span data-stu-id="cdc10-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="cdc10-524">Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="cdc10-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="cdc10-525">Ověřování uživatelů pro volání rozhraní API chráněných třetích stran</span><span class="sxs-lookup"><span data-stu-id="cdc10-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="cdc10-526">Ověřit uživatele pomocí toku OAuth na straně klienta proti poskytovateli rozhraní API třetí strany:</span><span class="sxs-lookup"><span data-stu-id="cdc10-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="cdc10-527">V tomto scénáři:</span><span class="sxs-lookup"><span data-stu-id="cdc10-527">In this scenario:</span></span>

* <span data-ttu-id="cdc10-528">Server, který je hostitelem aplikace, nehraje roli.</span><span class="sxs-lookup"><span data-stu-id="cdc10-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="cdc10-529">Rozhraní API na serveru nejde chránit.</span><span class="sxs-lookup"><span data-stu-id="cdc10-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="cdc10-530">Aplikace může volat jenom chráněná rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="cdc10-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="cdc10-531">Ověřování uživatelů pomocí poskytovatele třetí strany a volání chráněných rozhraní API na hostitelském serveru a třetí straně</span><span class="sxs-lookup"><span data-stu-id="cdc10-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="cdc10-532">Nakonfigurujte Identity pomocí poskytovatele přihlášení třetí strany.</span><span class="sxs-lookup"><span data-stu-id="cdc10-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="cdc10-533">Získejte tokeny vyžadované pro přístup k rozhraní API třetích stran a uložte je.</span><span class="sxs-lookup"><span data-stu-id="cdc10-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="cdc10-534">Když se uživatel přihlásí, Identity shromažďuje v rámci procesu ověřování tokeny a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="cdc10-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="cdc10-535">V tomto okamžiku je k dispozici několik přístupů pro volání rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="cdc10-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="cdc10-536">Použití přístupového tokenu serveru k načtení přístupového tokenu třetí strany</span><span class="sxs-lookup"><span data-stu-id="cdc10-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="cdc10-537">K načtení přístupového tokenu třetí strany z koncového bodu rozhraní API serveru použijte přístupový token vygenerovaný na serveru.</span><span class="sxs-lookup"><span data-stu-id="cdc10-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="cdc10-538">Odtud můžete pomocí přístupového tokenu třetí strany volat prostředky rozhraní API třetích stran přímo z Identity klienta.</span><span class="sxs-lookup"><span data-stu-id="cdc10-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="cdc10-539">Tento postup nedoporučujeme.</span><span class="sxs-lookup"><span data-stu-id="cdc10-539">We don't recommend this approach.</span></span> <span data-ttu-id="cdc10-540">Tento přístup vyžaduje ošetření přístupového tokenu třetí strany, jako kdyby byl vygenerován pro veřejného klienta.</span><span class="sxs-lookup"><span data-stu-id="cdc10-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="cdc10-541">V případech OAuth nemá veřejná aplikace tajný klíč klienta, protože nemůže být důvěryhodná pro bezpečné ukládání tajných kódů a přístupového tokenu se vytvoří pro důvěrného klienta.</span><span class="sxs-lookup"><span data-stu-id="cdc10-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="cdc10-542">Důvěrný klient je klient, který má tajný klíč klienta a předpokládá, že bude moci bezpečně ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="cdc10-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="cdc10-543">Přístupovému tokenu třetí strany se můžou udělit další obory, které budou provádět citlivé operace na základě skutečnosti, že třetí strana vygenerovala token pro více důvěryhodných klientů.</span><span class="sxs-lookup"><span data-stu-id="cdc10-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="cdc10-544">Podobně by se aktualizační tokeny neměly vystavit klientovi, který není důvěryhodný, protože by to tak měl klienta bez omezení přístupu, pokud se na ně neukládají jiná omezení.</span><span class="sxs-lookup"><span data-stu-id="cdc10-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="cdc10-545">Volání rozhraní API z klienta na rozhraní API serveru za účelem volání rozhraní API třetích stran</span><span class="sxs-lookup"><span data-stu-id="cdc10-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="cdc10-546">Naplňte volání rozhraní API z klienta na serverové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="cdc10-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="cdc10-547">Ze serveru načtěte přístupový token pro prostředek rozhraní API třetí strany a vydejte jakékoli volání, které je potřeba.</span><span class="sxs-lookup"><span data-stu-id="cdc10-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="cdc10-548">I když tento přístup vyžaduje další síťové směrování prostřednictvím serveru za účelem volání rozhraní API třetí strany, má za následek bezpečnější prostředí:</span><span class="sxs-lookup"><span data-stu-id="cdc10-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="cdc10-549">Server může ukládat aktualizační tokeny a zajistit, aby aplikace neztratila přístup k prostředkům třetích stran.</span><span class="sxs-lookup"><span data-stu-id="cdc10-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="cdc10-550">Aplikace nemůže zajímat přístupové tokeny ze serveru, který může obsahovat citlivá oprávnění.</span><span class="sxs-lookup"><span data-stu-id="cdc10-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="cdc10-551">Použití koncových bodů Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="cdc10-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="cdc10-552">Knihovna a šablony ověřování Blazor používají koncové body Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="cdc10-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="cdc10-553">Pokud chcete použít koncový bod v 2.0, nakonfigurujte možnost nosiče JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="cdc10-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="cdc10-554">V následujícím příkladu je AAD nakonfigurováno v 2.0 připojením `v2.0` segmentu k <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="cdc10-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="cdc10-555">Případně může být nastavení provedeno v souboru nastavení aplikace (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="cdc10-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="cdc10-556">Pokud se označení segmentu pro autoritu nehodí pro poskytovatele OIDC aplikace, jako je třeba u jiných poskytovatelů než AAD, nastavte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> vlastnost přímo.</span><span class="sxs-lookup"><span data-stu-id="cdc10-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="cdc10-557">Buď nastavte vlastnost v <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> souboru nastavení aplikace (*appSettings. JSON*) pomocí `Authority` klíče.</span><span class="sxs-lookup"><span data-stu-id="cdc10-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="cdc10-558">Seznam deklarací identity v tokenu ID se mění pro koncové body verze 2.0.</span><span class="sxs-lookup"><span data-stu-id="cdc10-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="cdc10-559">Další informace najdete v tématu [Proč aktualizace pro Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="cdc10-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
