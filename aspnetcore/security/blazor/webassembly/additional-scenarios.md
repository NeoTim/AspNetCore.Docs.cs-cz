---
title: ASP.NET Core Blazor další scénáře zabezpečení pro WebAssembly
author: guardrex
description: Přečtěte si, Blazor jak nakonfigurovat WebAssembly pro další scénáře zabezpečení.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: e8a088b3f1a4e0eb7d5d1c5c09ef53c4a2bd3628
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976789"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="732d9-103">Další scénáře zabezpečení ASP.NET Core Blazor pro WebAssembly</span><span class="sxs-lookup"><span data-stu-id="732d9-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="732d9-104">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="732d9-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="732d9-105">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="732d9-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="732d9-106">`AuthorizationMessageHandler` Službu lze použít s nástrojem `HttpClient` k připojení přístupových tokenů k odchozím žádostem.</span><span class="sxs-lookup"><span data-stu-id="732d9-106">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="732d9-107">Tokeny se získávají pomocí existující `IAccessTokenProvider` služby.</span><span class="sxs-lookup"><span data-stu-id="732d9-107">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="732d9-108">Pokud token nelze získat, `AccessTokenNotAvailableException` je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="732d9-108">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="732d9-109">`AccessTokenNotAvailableException`má `Redirect` metodu, která se dá použít k navigaci uživatele na poskytovatele identity za účelem získání nového tokenu.</span><span class="sxs-lookup"><span data-stu-id="732d9-109">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="732d9-110">Pomocí `AuthorizationMessageHandler` `ConfigureHandler` metody lze konfigurovat pomocí autorizovaných adres URL, oborů a návratové adresy URL.</span><span class="sxs-lookup"><span data-stu-id="732d9-110">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="732d9-111">V následujícím `AuthorizationMessageHandler` příkladu nakonfiguruje `HttpClient` v `Program.Main` (*program.cs*):</span><span class="sxs-lookup"><span data-stu-id="732d9-111">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="732d9-112">Pro usnadnění práce `BaseAddressAuthorizationMessageHandler` je součástí předem nakonfigurovaná základní adresa aplikace jako autorizovaná adresa URL.</span><span class="sxs-lookup"><span data-stu-id="732d9-112">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="732d9-113">Šablony WebAssembly Blazor <xref:System.Net.Http.IHttpClientFactory> <xref:System.Net.Http.HttpClient> s povoleným ověřováním teď používají v projektu rozhraní API serveru k nastavení pomocí `BaseAddressAuthorizationMessageHandler`:</span><span class="sxs-lookup"><span data-stu-id="732d9-113">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="732d9-114">Tam, kde je klient vytvořen `CreateClient` pomocí v předchozím příkladu, <xref:System.Net.Http.HttpClient> se dodávají instance, které zahrnují přístupové tokeny při vytváření požadavků na serverový projekt.</span><span class="sxs-lookup"><span data-stu-id="732d9-114">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="732d9-115">Nakonfigurovaná <xref:System.Net.Http.HttpClient> se pak používá k provádění autorizovaných požadavků pomocí jednoduchého `try-catch` vzoru.</span><span class="sxs-lookup"><span data-stu-id="732d9-115">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="732d9-116">Následující `FetchData` součást požaduje data předpovědi počasí:</span><span class="sxs-lookup"><span data-stu-id="732d9-116">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

<span data-ttu-id="732d9-117">Případně můžete definovat typového klienta, který zpracovává všechny aspekty získání HTTP a tokenu v rámci jedné třídy:</span><span class="sxs-lookup"><span data-stu-id="732d9-117">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="732d9-118">*WeatherClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="732d9-118">*WeatherClient.cs*:</span></span>

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="732d9-119">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="732d9-119">*Program.cs*:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="732d9-120">*FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="732d9-120">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="request-additional-access-tokens"></a><span data-ttu-id="732d9-121">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="732d9-121">Request additional access tokens</span></span>

<span data-ttu-id="732d9-122">Přístupové tokeny lze získat ručně voláním `IAccessTokenProvider.RequestAccessToken`.</span><span class="sxs-lookup"><span data-stu-id="732d9-122">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="732d9-123">V následujícím příkladu jsou vyžadovány další Azure Active Directory (AAD) Microsoft Graph obory rozhraní API pro čtení uživatelských dat a odesílání e-mailů.</span><span class="sxs-lookup"><span data-stu-id="732d9-123">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="732d9-124">Po přidání oprávnění Microsoft Graph API na portálu Azure AAD jsou další obory nakonfigurované v klientské aplikaci (`Program.Main` *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="732d9-124">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="732d9-125">`IAccessTokenProvider.RequestToken` Metoda poskytuje přetížení, které umožňuje aplikaci zřídit přístupový token s danou sadou oborů, jak je vidět v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="732d9-125">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes, as seen in the following example:</span></span>

```csharp
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

<span data-ttu-id="732d9-126">`TryGetToken`Vrátí</span><span class="sxs-lookup"><span data-stu-id="732d9-126">`TryGetToken` returns:</span></span>

* <span data-ttu-id="732d9-127">`true`s nástrojem `token` pro použití.</span><span class="sxs-lookup"><span data-stu-id="732d9-127">`true` with the `token` for use.</span></span>
* <span data-ttu-id="732d9-128">`false`Pokud se token nenačte.</span><span class="sxs-lookup"><span data-stu-id="732d9-128">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="732d9-129">HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení</span><span class="sxs-lookup"><span data-stu-id="732d9-129">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="732d9-130">Při spuštění na WebAssembly v Blazor aplikaci WebAssembly [HttpClient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage> dá se použít k přizpůsobení požadavků.</span><span class="sxs-lookup"><span data-stu-id="732d9-130">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="732d9-131">Můžete například zadat metodu HTTP a hlavičku požadavku.</span><span class="sxs-lookup"><span data-stu-id="732d9-131">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="732d9-132">Následující příklad vytvoří `POST` požadavek na koncový bod rozhraní API seznamu na serveru a zobrazí tělo odpovědi:</span><span class="sxs-lookup"><span data-stu-id="732d9-132">The following example makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="732d9-133">Implementace rozhraní .NET WebAssembly pro `HttpClient` používá [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="732d9-133">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="732d9-134">Načtení umožňuje nakonfigurovat několik [možností specifických pro požadavky](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="732d9-134">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="732d9-135">Možnosti požadavku HTTP Fetch lze konfigurovat pomocí `HttpRequestMessage` rozšiřujících metod, které jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="732d9-135">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="732d9-136">`HttpRequestMessage`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="732d9-136">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="732d9-137">Načíst vlastnost žádosti</span><span class="sxs-lookup"><span data-stu-id="732d9-137">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="732d9-138">přihlašovací údaje</span><span class="sxs-lookup"><span data-stu-id="732d9-138">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="732d9-139">uchovávat</span><span class="sxs-lookup"><span data-stu-id="732d9-139">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="732d9-140">Mode</span><span class="sxs-lookup"><span data-stu-id="732d9-140">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="732d9-141">způsobilost</span><span class="sxs-lookup"><span data-stu-id="732d9-141">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="732d9-142">Další možnosti můžete nastavit pomocí obecnější metody `SetBrowserRequestOption` rozšíření.</span><span class="sxs-lookup"><span data-stu-id="732d9-142">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="732d9-143">Odpověď HTTP je obvykle ukládána do vyrovnávací paměti v Blazor aplikaci WebAssembly, aby umožnila podporu pro čtení v obsahu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="732d9-143">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="732d9-144">Pokud chcete povolit podporu pro streamování odpovědí, `SetBrowserResponseStreamingEnabled` použijte metodu rozšíření v žádosti.</span><span class="sxs-lookup"><span data-stu-id="732d9-144">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="732d9-145">Pokud chcete do žádosti o více zdrojů zahrnout přihlašovací údaje, použijte `SetBrowserRequestCredentials` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="732d9-145">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="732d9-146">Další informace o možnostech načtení rozhraní API naleznete v tématu [MDN web Docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="732d9-146">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="732d9-147">Při odesílání přihlašovacích údajů (souborů cookie autorizace/hlaviček) na žádostech CORS musí být `Authorization` záhlaví povoleno zásadami CORS.</span><span class="sxs-lookup"><span data-stu-id="732d9-147">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="732d9-148">Následující zásady zahrnují konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="732d9-148">The following policy includes configuration for:</span></span>

* <span data-ttu-id="732d9-149">Původ žádosti (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="732d9-149">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="732d9-150">Libovolná metoda (příkaz).</span><span class="sxs-lookup"><span data-stu-id="732d9-150">Any method (verb).</span></span>
* <span data-ttu-id="732d9-151">`Content-Type`a `Authorization` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="732d9-151">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="732d9-152">Chcete-li pro vlastní hlavičku (například `x-custom-header`), uveďte záhlaví při volání. <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="732d9-152">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="732d9-153">Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta`credentials` (vlastnost je `include`nastavena na hodnotu).</span><span class="sxs-lookup"><span data-stu-id="732d9-153">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="732d9-154">Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="732d9-154">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="732d9-155">Zpracování chyb požadavků na tokeny</span><span class="sxs-lookup"><span data-stu-id="732d9-155">Handle token request errors</span></span>

<span data-ttu-id="732d9-156">Když jedna stránková aplikace (SPA) ověřuje uživatele pomocí funkce Open ID Connect (OIDC), je stav ověřování udržován místně v zabezpečeném uživatelském rozhraní (SPA) a ve zprostředkovateli identity (IP) ve formě souboru cookie relace, který je nastaven v důsledku zadání přihlašovacích údajů uživatelem.</span><span class="sxs-lookup"><span data-stu-id="732d9-156">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="732d9-157">Tokeny, které jsou pro uživatele vysílané, jsou obvykle platné po krátkou dobu přibližně jedna hodina, takže klientská aplikace musí pravidelně načítat nové tokeny.</span><span class="sxs-lookup"><span data-stu-id="732d9-157">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="732d9-158">V opačném případě se uživatel odhlásí po vypršení platnosti udělených tokenů.</span><span class="sxs-lookup"><span data-stu-id="732d9-158">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="732d9-159">Ve většině případů můžou klienti OIDC zřizovat nové tokeny, aniž by museli znovu ověřovat uživatele díky stavu ověřování nebo "relaci", která se udržuje v rámci IP adresy.</span><span class="sxs-lookup"><span data-stu-id="732d9-159">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="732d9-160">V některých případech může klient získat token bez zásahu uživatele, například pokud z nějakého důvodu se uživatel výslovně odhlásí z IP adresy.</span><span class="sxs-lookup"><span data-stu-id="732d9-160">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="732d9-161">K tomuto scénáři dochází, pokud uživatel `https://login.microsoftonline.com` navštíví a odhlásí. V těchto scénářích aplikace neví hned, že se uživatel odhlásil. Libovolný token, který může klient obsahovat, již nemusí být platný.</span><span class="sxs-lookup"><span data-stu-id="732d9-161">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="732d9-162">Klient navíc nemůže zřídit nový token bez zásahu uživatele po vypršení platnosti tohoto tokenu.</span><span class="sxs-lookup"><span data-stu-id="732d9-162">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="732d9-163">Tyto scénáře nejsou specifické pro ověřování založené na tokenech.</span><span class="sxs-lookup"><span data-stu-id="732d9-163">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="732d9-164">Jsou součástí charakteru jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="732d9-164">They are part of the nature of SPAs.</span></span> <span data-ttu-id="732d9-165">ZABEZPEČENÉ ověřování pomocí souborů cookie také nedokáže volat rozhraní API serveru, pokud je soubor cookie ověření odebrán.</span><span class="sxs-lookup"><span data-stu-id="732d9-165">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="732d9-166">Když aplikace provede volání rozhraní API k chráněným prostředkům, musíte mít na paměti následující informace:</span><span class="sxs-lookup"><span data-stu-id="732d9-166">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="732d9-167">Pro zřízení nového přístupového tokenu pro volání rozhraní API může být uživatel požádán o ověření znovu.</span><span class="sxs-lookup"><span data-stu-id="732d9-167">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="732d9-168">I v případě, že má klient token, který je pravděpodobně platný, volání serveru může selhat, protože byl token odvolán uživatelem.</span><span class="sxs-lookup"><span data-stu-id="732d9-168">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="732d9-169">Když aplikace požaduje token, existují dva možné výsledky:</span><span class="sxs-lookup"><span data-stu-id="732d9-169">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="732d9-170">Požadavek je úspěšný a aplikace má platný token.</span><span class="sxs-lookup"><span data-stu-id="732d9-170">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="732d9-171">Požadavek se nezdařil a aplikace musí znovu ověřit uživatele, aby získal nový token.</span><span class="sxs-lookup"><span data-stu-id="732d9-171">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="732d9-172">Pokud se žádost o token nezdařila, musíte se rozhodnout, zda chcete před provedením přesměrování Uložit aktuální stav.</span><span class="sxs-lookup"><span data-stu-id="732d9-172">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="732d9-173">Existuje několik přístupů se zvýšenými úrovněmi složitosti:</span><span class="sxs-lookup"><span data-stu-id="732d9-173">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="732d9-174">Uloží aktuální stav stránky do úložiště relace.</span><span class="sxs-lookup"><span data-stu-id="732d9-174">Store the current page state in session storage.</span></span> <span data-ttu-id="732d9-175">Během `OnInitializeAsync`této operace ověřte, zda je možné obnovit stav, než budete pokračovat.</span><span class="sxs-lookup"><span data-stu-id="732d9-175">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="732d9-176">Přidejte parametr řetězce dotazu a použijte ho jako způsob, jak aplikaci signalizovat, že potřebuje znovu Hydrate dříve uložený stav.</span><span class="sxs-lookup"><span data-stu-id="732d9-176">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="732d9-177">Přidejte parametr řetězce dotazu s jedinečným identifikátorem pro ukládání dat v úložišti relace bez rizikových kolizí s ostatními položkami.</span><span class="sxs-lookup"><span data-stu-id="732d9-177">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="732d9-178">Následující příklad ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="732d9-178">The following example shows how to:</span></span>

* <span data-ttu-id="732d9-179">Zachovat stav před přesměrováním na přihlašovací stránku</span><span class="sxs-lookup"><span data-stu-id="732d9-179">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="732d9-180">Obnovte předchozí stav po ověření pomocí parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="732d9-180">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="732d9-181">Uložení stavu aplikace před operací ověřování</span><span class="sxs-lookup"><span data-stu-id="732d9-181">Save app state before an authentication operation</span></span>

<span data-ttu-id="732d9-182">Během operace ověřování existují případy, kdy chcete uložit stav aplikace, než se prohlížeč přesměruje na IP adresu.</span><span class="sxs-lookup"><span data-stu-id="732d9-182">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="732d9-183">To může být případ, kdy používáte něco jako kontejner stavu a chcete obnovit stav po úspěšném ověření.</span><span class="sxs-lookup"><span data-stu-id="732d9-183">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="732d9-184">Vlastní objekt stavu ověřování můžete použít k zachování stavu specifického pro aplikaci nebo odkaz na něj a obnovení tohoto stavu po úspěšném dokončení operace ověřování.</span><span class="sxs-lookup"><span data-stu-id="732d9-184">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="732d9-185">`Authentication`součást (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="732d9-185">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="732d9-186">Přizpůsobení směrování aplikací</span><span class="sxs-lookup"><span data-stu-id="732d9-186">Customize app routes</span></span>

<span data-ttu-id="732d9-187">Ve výchozím nastavení `Microsoft.AspNetCore.Components.WebAssembly.Authentication` knihovna používá trasy, které jsou uvedeny v následující tabulce, pro reprezentace různých stavů ověřování.</span><span class="sxs-lookup"><span data-stu-id="732d9-187">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="732d9-188">Trasa</span><span class="sxs-lookup"><span data-stu-id="732d9-188">Route</span></span>                            | <span data-ttu-id="732d9-189">Účel</span><span class="sxs-lookup"><span data-stu-id="732d9-189">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="732d9-190">Spustí operaci přihlášení.</span><span class="sxs-lookup"><span data-stu-id="732d9-190">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="732d9-191">Zpracovává výsledek jakékoli operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="732d9-191">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="732d9-192">Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="732d9-192">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="732d9-193">Spustí operaci odhlášení.</span><span class="sxs-lookup"><span data-stu-id="732d9-193">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="732d9-194">Zpracovává výsledek operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="732d9-194">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="732d9-195">Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="732d9-195">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="732d9-196">Indikuje, že se uživatel úspěšně odhlásit.</span><span class="sxs-lookup"><span data-stu-id="732d9-196">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="732d9-197">Aktivuje operaci pro úpravu profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="732d9-197">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="732d9-198">Aktivuje operaci pro registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="732d9-198">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="732d9-199">Trasy zobrazené v předchozí tabulce lze konfigurovat prostřednictvím `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span><span class="sxs-lookup"><span data-stu-id="732d9-199">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="732d9-200">Při nastavování možností pro poskytování vlastních tras potvrďte, že aplikace má trasu, která zpracovává jednotlivé cesty.</span><span class="sxs-lookup"><span data-stu-id="732d9-200">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="732d9-201">V následujícím příkladu jsou všechny cesty s `/security`předponou.</span><span class="sxs-lookup"><span data-stu-id="732d9-201">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="732d9-202">`Authentication`součást (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="732d9-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="732d9-203">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="732d9-203">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="732d9-204">Pokud požadavek volá zcela různé cesty, nastavte trasy popsané dříve a vykreslete `RemoteAuthenticatorView` s parametrem explicitní akce:</span><span class="sxs-lookup"><span data-stu-id="732d9-204">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="732d9-205">Pokud se rozhodnete tak učinit, můžete uživatelské rozhraní přerušit na jiné stránky.</span><span class="sxs-lookup"><span data-stu-id="732d9-205">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="732d9-206">Přizpůsobení uživatelského rozhraní ověřování</span><span class="sxs-lookup"><span data-stu-id="732d9-206">Customize the authentication user interface</span></span>

<span data-ttu-id="732d9-207">`RemoteAuthenticatorView`obsahuje výchozí sadu částí uživatelského rozhraní pro každý stav ověřování.</span><span class="sxs-lookup"><span data-stu-id="732d9-207">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="732d9-208">Každý stav lze přizpůsobit předáním vlastního `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="732d9-208">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="732d9-209">K přizpůsobení zobrazeného textu během procesu prvotního přihlášení může změnit `RemoteAuthenticatorView` následující postup.</span><span class="sxs-lookup"><span data-stu-id="732d9-209">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="732d9-210">`Authentication`součást (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="732d9-210">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="732d9-211">`RemoteAuthenticatorView` Má jeden fragment, který se dá použít pro jednu trasu ověřování, jak je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="732d9-211">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="732d9-212">Trasa</span><span class="sxs-lookup"><span data-stu-id="732d9-212">Route</span></span>                            | <span data-ttu-id="732d9-213">Fragment</span><span class="sxs-lookup"><span data-stu-id="732d9-213">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="732d9-214">Přizpůsobení uživatele</span><span class="sxs-lookup"><span data-stu-id="732d9-214">Customize the user</span></span>

<span data-ttu-id="732d9-215">Uživatele navázané na aplikaci je možné přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="732d9-215">Users bound to the app can be customized.</span></span> <span data-ttu-id="732d9-216">V následujícím příkladu obdrží všichni ověření uživatelé `amr` deklaraci identity pro každou metodu ověřování uživatele.</span><span class="sxs-lookup"><span data-stu-id="732d9-216">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="732d9-217">Vytvořte třídu, která rozšiřuje `RemoteUserAccount` třídu:</span><span class="sxs-lookup"><span data-stu-id="732d9-217">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="732d9-218">Vytvořte objekt pro vytváření, `AccountClaimsPrincipalFactory<TAccount>`který rozšiřuje:</span><span class="sxs-lookup"><span data-stu-id="732d9-218">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="732d9-219">`CustomAccountFactory` Zaregistrujte zprostředkovatele ověřování, který se používá.</span><span class="sxs-lookup"><span data-stu-id="732d9-219">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="732d9-220">Platná jsou následující registrace:</span><span class="sxs-lookup"><span data-stu-id="732d9-220">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="732d9-221">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="732d9-221">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="732d9-222">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="732d9-222">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="732d9-223">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="732d9-223">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="732d9-224">Podpora předběžného vykreslování s ověřováním</span><span class="sxs-lookup"><span data-stu-id="732d9-224">Support prerendering with authentication</span></span>

<span data-ttu-id="732d9-225">Po použití pokynů v některém z hostovaných Blazor témat aplikace WebAssembly použijte následující pokyny k vytvoření aplikace, která:</span><span class="sxs-lookup"><span data-stu-id="732d9-225">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="732d9-226">Předem vykreslí cesty, pro které není nutná autorizace.</span><span class="sxs-lookup"><span data-stu-id="732d9-226">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="732d9-227">Nejedná se o cesty PreRender, pro které se vyžaduje autorizace.</span><span class="sxs-lookup"><span data-stu-id="732d9-227">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="732d9-228">V rámci `Program` třídy klientské aplikace (*program.cs*) se služba Factoring Common registruje do samostatné metody (například `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="732d9-228">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="732d9-229">V serverové aplikaci `Startup.ConfigureServices`Zaregistrujte následující další služby:</span><span class="sxs-lookup"><span data-stu-id="732d9-229">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="732d9-230">V `Startup.Configure` metodě serverové aplikace nahraďte `endpoints.MapFallbackToFile("index.html")` `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="732d9-230">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="732d9-231">V serverové aplikaci vytvořte složku *stránky* , pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="732d9-231">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="732d9-232">Vytvořte stránku *_Host. cshtml* ve složce *stránek* serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="732d9-232">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="732d9-233">Vložte obsah ze souboru *wwwroot/index.html* klientské aplikace do souboru *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="732d9-233">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="732d9-234">Aktualizujte obsah souboru:</span><span class="sxs-lookup"><span data-stu-id="732d9-234">Update the file's contents:</span></span>

* <span data-ttu-id="732d9-235">Přidejte `@page "_Host"` na začátek souboru.</span><span class="sxs-lookup"><span data-stu-id="732d9-235">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="732d9-236">`<app>Loading...</app>` Značku nahraďte následujícím:</span><span class="sxs-lookup"><span data-stu-id="732d9-236">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="732d9-237">Možnosti pro hostované aplikace a poskytovatele přihlašovacích údajů třetích stran</span><span class="sxs-lookup"><span data-stu-id="732d9-237">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="732d9-238">Při ověřování a autorizaci hostované Blazor aplikace WebAssembly s poskytovatelem třetí strany je k dispozici několik možností pro ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="732d9-238">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="732d9-239">Kterou zvolíte, závisí na vašem scénáři.</span><span class="sxs-lookup"><span data-stu-id="732d9-239">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="732d9-240">Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="732d9-240">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="732d9-241">Ověřování uživatelů pro volání rozhraní API chráněných třetích stran</span><span class="sxs-lookup"><span data-stu-id="732d9-241">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="732d9-242">Ověřit uživatele pomocí toku OAuth na straně klienta proti poskytovateli rozhraní API třetí strany:</span><span class="sxs-lookup"><span data-stu-id="732d9-242">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="732d9-243">V tomto scénáři:</span><span class="sxs-lookup"><span data-stu-id="732d9-243">In this scenario:</span></span>

* <span data-ttu-id="732d9-244">Server, který je hostitelem aplikace, nehraje roli.</span><span class="sxs-lookup"><span data-stu-id="732d9-244">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="732d9-245">Rozhraní API na serveru nejde chránit.</span><span class="sxs-lookup"><span data-stu-id="732d9-245">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="732d9-246">Aplikace může volat jenom chráněná rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="732d9-246">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="732d9-247">Ověřování uživatelů pomocí poskytovatele třetí strany a volání chráněných rozhraní API na hostitelském serveru a třetí straně</span><span class="sxs-lookup"><span data-stu-id="732d9-247">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="732d9-248">Nakonfigurujte Identity pomocí poskytovatele přihlášení třetí strany.</span><span class="sxs-lookup"><span data-stu-id="732d9-248">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="732d9-249">Získejte tokeny vyžadované pro přístup k rozhraní API třetích stran a uložte je.</span><span class="sxs-lookup"><span data-stu-id="732d9-249">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="732d9-250">Když se uživatel přihlásí, Identity shromažďuje v rámci procesu ověřování tokeny a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="732d9-250">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="732d9-251">V tomto okamžiku je k dispozici několik přístupů pro volání rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="732d9-251">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="732d9-252">Použití přístupového tokenu serveru k načtení přístupového tokenu třetí strany</span><span class="sxs-lookup"><span data-stu-id="732d9-252">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="732d9-253">K načtení přístupového tokenu třetí strany z koncového bodu rozhraní API serveru použijte přístupový token vygenerovaný na serveru.</span><span class="sxs-lookup"><span data-stu-id="732d9-253">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="732d9-254">Odtud můžete pomocí přístupového tokenu třetí strany volat prostředky rozhraní API třetích stran přímo z Identity klienta.</span><span class="sxs-lookup"><span data-stu-id="732d9-254">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="732d9-255">Tento postup nedoporučujeme.</span><span class="sxs-lookup"><span data-stu-id="732d9-255">We don't recommend this approach.</span></span> <span data-ttu-id="732d9-256">Tento přístup vyžaduje ošetření přístupového tokenu třetí strany, jako kdyby byl vygenerován pro veřejného klienta.</span><span class="sxs-lookup"><span data-stu-id="732d9-256">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="732d9-257">V případech OAuth nemá veřejná aplikace tajný klíč klienta, protože nemůže být důvěryhodná pro bezpečné ukládání tajných kódů a přístupového tokenu se vytvoří pro důvěrného klienta.</span><span class="sxs-lookup"><span data-stu-id="732d9-257">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="732d9-258">Důvěrný klient je klient, který má tajný klíč klienta a předpokládá, že bude moci bezpečně ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="732d9-258">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="732d9-259">Přístupovému tokenu třetí strany se můžou udělit další obory, které budou provádět citlivé operace na základě skutečnosti, že třetí strana vygenerovala token pro více důvěryhodných klientů.</span><span class="sxs-lookup"><span data-stu-id="732d9-259">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="732d9-260">Podobně by se aktualizační tokeny neměly vystavit klientovi, který není důvěryhodný, protože by to tak měl klienta bez omezení přístupu, pokud se na ně neukládají jiná omezení.</span><span class="sxs-lookup"><span data-stu-id="732d9-260">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="732d9-261">Volání rozhraní API z klienta na rozhraní API serveru za účelem volání rozhraní API třetích stran</span><span class="sxs-lookup"><span data-stu-id="732d9-261">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="732d9-262">Naplňte volání rozhraní API z klienta na serverové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="732d9-262">Make an API call from the client to the server API.</span></span> <span data-ttu-id="732d9-263">Ze serveru načtěte přístupový token pro prostředek rozhraní API třetí strany a vydejte jakékoli volání, které je potřeba.</span><span class="sxs-lookup"><span data-stu-id="732d9-263">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="732d9-264">I když tento přístup vyžaduje další síťové směrování prostřednictvím serveru za účelem volání rozhraní API třetí strany, má za následek bezpečnější prostředí:</span><span class="sxs-lookup"><span data-stu-id="732d9-264">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="732d9-265">Server může ukládat aktualizační tokeny a zajistit, aby aplikace neztratila přístup k prostředkům třetích stran.</span><span class="sxs-lookup"><span data-stu-id="732d9-265">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="732d9-266">Aplikace nemůže zajímat přístupové tokeny ze serveru, který může obsahovat citlivá oprávnění.</span><span class="sxs-lookup"><span data-stu-id="732d9-266">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
