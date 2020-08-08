---
title: ASP.NET Core Blazor WebAssembly Další scénáře zabezpečení
author: guardrex
description: Naučte se konfigurovat Blazor WebAssembly pro další scénáře zabezpečení.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 15531c39a66a9f6dfd0f5c20cf960e4db5a78074
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013798"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly Další scénáře zabezpečení

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

## <a name="attach-tokens-to-outgoing-requests"></a>Připojit tokeny k odchozím žádostem

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>slouží <xref:System.Net.Http.DelegatingHandler> k připojení přístupových tokenů k odchozím <xref:System.Net.Http.HttpResponseMessage> instancím. Tokeny jsou získány pomocí <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> služby, která je registrována rozhraním. Pokud token nelze získat, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> je vyvolána výjimka. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>má <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> metodu, která se dá použít k navigaci uživatele na poskytovatele identity za účelem získání nového tokenu.

Pro usnadnění pohodlí poskytuje rozhraní <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> předem nakonfigurovanou základní adresu aplikace jako autorizovanou adresu URL. **Přístupové tokeny se přidávají jenom v případě, že identifikátor URI žádosti spadá do základního identifikátoru URI aplikace.** Pokud neexistují odchozí identifikátory URI žádosti v rámci základního identifikátoru URI aplikace, použijte [vlastní `AuthorizationMessageHandler` třídu (*doporučeno*)](#custom-authorizationmessagehandler-class) nebo [Nakonfigurujte `AuthorizationMessageHandler` ](#configure-authorizationmessagehandler).

> [!NOTE]
> Kromě konfigurace klientské aplikace pro přístup k rozhraní API serveru musí serverové rozhraní API také umožňovat žádosti mezi zdroji (CORS), pokud se klient a server nenachází na stejné základní adrese. Další informace o konfiguraci CORS na straně serveru najdete v části [sdílení prostředků mezi zdroji (CORS)](#cross-origin-resource-sharing-cors) dále v tomto článku.

V následujícím příkladu:

* <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A>přidá <xref:System.Net.Http.IHttpClientFactory> a související služby do kolekce služeb a nakonfiguruje s názvem <xref:System.Net.Http.HttpClient> ( `ServerAPI` ). <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>je základní adresou identifikátoru URI prostředku při odesílání požadavků. <xref:System.Net.Http.IHttpClientFactory>je poskytováno [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) balíčkem NuGet.
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler><xref:System.Net.Http.DelegatingHandler>slouží k připojení přístupových tokenů k odchozím <xref:System.Net.Http.HttpResponseMessage> instancím. Přístupové tokeny se přidávají jenom v případě, že identifikátor URI žádosti spadá do základního identifikátoru URI aplikace.
* <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType>Vytvoří a nakonfiguruje <xref:System.Net.Http.HttpClient> instanci pro odchozí požadavky pomocí konfigurace, která odpovídá pojmenovanému <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

V případě Blazor aplikace založené na Blazor WebAssembly šabloně hostovaného projektu jsou ve výchozím nastavení identifikátory URI požadavků v rámci základního identifikátoru URI aplikace. Proto <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) je přiřazeno <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> v aplikaci vygenerované ze šablony projektu.

Nakonfigurovaná <xref:System.Net.Http.HttpClient> se používá k provádění autorizovaných požadavků pomocí [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) vzoru:

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
            await Client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a>Vlastní `AuthorizationMessageHandler` Třída

*Tento návod v této části se doporučuje pro klientské aplikace, které vytvářejí odchozí požadavky na identifikátory URI, které nejsou v rámci základního identifikátoru URI aplikace.*

V následujícím příkladu vlastní třída rozšiřuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> pro použití jako <xref:System.Net.Http.DelegatingHandler> pro <xref:System.Net.Http.HttpClient> . <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>nakonfiguruje tuto obslužnou rutinu tak, aby schvalovala odchozí požadavky HTTP pomocí přístupového tokenu. Přístupový token je připojen pouze v případě, že alespoň jedna z autorizovaných adres URL je základem identifikátoru URI žádosti ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).

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

V `Program.Main` ( `Program.cs` ) `CustomAuthorizationMessageHandler` je zaregistrován jako Oborová služba a je nakonfigurován jako <xref:System.Net.Http.DelegatingHandler> pro odchozí <xref:System.Net.Http.HttpResponseMessage> instance vytvořené pomocí pojmenovaného <xref:System.Net.Http.HttpClient> :

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

Pro Blazor aplikaci založenou na Blazor WebAssembly šabloně hostovaného projektu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) je <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ve výchozím nastavení přiřazeno.

Nakonfigurovaná <xref:System.Net.Http.HttpClient> se používá k provádění autorizovaných požadavků pomocí [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) vzoru. V případě, že je klient vytvořen pomocí nástroje <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) balíček), <xref:System.Net.Http.HttpClient> jsou dodány instance, které zahrnují přístupové tokeny při vytváření požadavků na rozhraní API serveru. Pokud je identifikátor URI požadavku relativním identifikátorem URI, protože je v následujícím příkladu ( `ExampleAPIMethod` ), je v kombinaci s rozhraním, <xref:System.Net.Http.HttpClient.BaseAddress> když klientská aplikace provede požadavek:

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
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a>Konfigurace`AuthorizationMessageHandler`

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>dá se nakonfigurovat pomocí autorizovaných adres URL, oborů a návratové adresy URL pomocí <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> metody. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>nakonfiguruje obslužnou rutinu pro autorizaci odchozích požadavků HTTP pomocí přístupového tokenu. Přístupový token je připojen pouze v případě, že alespoň jedna z autorizovaných adres URL je základem identifikátoru URI žádosti ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ). Pokud je identifikátor URI požadavku relativním identifikátorem URI, je v kombinaci s <xref:System.Net.Http.HttpClient.BaseAddress> .

V následujícím příkladu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> nakonfiguruje <xref:System.Net.Http.HttpClient> v `Program.Main` ( `Program.cs` ):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

Pro Blazor aplikaci založenou na Blazor WebAssembly šabloně hostovaného projektu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> je ve výchozím nastavení přiřazen následující:

* Rozhraní <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).
* Adresa URL `authorizedUrls` pole

## <a name="typed-httpclient"></a>Zadal`HttpClient`

Je možné definovat zadaného klienta, který zpracovává všechny aspekty získání HTTP a tokenu v rámci jedné třídy.

`WeatherForecastClient.cs`:

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

Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `using static BlazorSample.Data;` ).

`Program.Main` (`Program.cs`):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

Pro Blazor aplikaci založenou na Blazor WebAssembly šabloně hostovaného projektu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) je <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ve výchozím nastavení přiřazeno.

`FetchData`součást ( `Pages/FetchData.razor` ):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Konfigurace `HttpClient` obslužné rutiny

Obslužná rutina může být dále nakonfigurována <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pro odchozí požadavky HTTP.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

Pro Blazor aplikaci založenou na Blazor WebAssembly šabloně hostovaného projektu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> je ve výchozím nastavení přiřazen následující:

* Rozhraní <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).
* Adresa URL `authorizedUrls` pole

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem

Pokud Blazor WebAssembly aplikace běžně používá zabezpečené výchozí nastavení <xref:System.Net.Http.HttpClient> , může také aplikace neověřené nebo neautorizované požadavky webového rozhraní API provést konfigurací pojmenovaného <xref:System.Net.Http.HttpClient> :

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

Pro Blazor aplikaci založenou na Blazor WebAssembly šabloně hostovaného projektu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) je <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ve výchozím nastavení přiřazeno.

Předchozí registrace je kromě existující zabezpečené výchozí <xref:System.Net.Http.HttpClient> registrace.

Komponenta vytvoří <xref:System.Net.Http.HttpClient> z <xref:System.Net.Http.IHttpClientFactory> [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) balíčku (balíček) k provedení neověřených nebo neautorizovaných požadavků:

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
> Řadič v rozhraní API serveru `WeatherForecastNoAuthenticationController` pro předchozí příklad není označený [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributem.

Rozhodnutí, jestli se má použít zabezpečený klient nebo nezabezpečený klient, protože výchozí <xref:System.Net.Http.HttpClient> instance je až pro vývojáře. Jedním ze způsobů, jak toto rozhodnutí udělat, je zvážit počet ověřených i neověřených koncových bodů, které aplikace kontaktuje. Pokud většina požadavků aplikace má zabezpečit koncové body rozhraní API, použijte <xref:System.Net.Http.HttpClient> jako výchozí instanci ověřenou. V opačném případě Zaregistrujte neověřenou <xref:System.Net.Http.HttpClient> instanci jako výchozí.

Alternativním přístupem k použití <xref:System.Net.Http.IHttpClientFactory> je vytvoření [typu klient](#typed-httpclient) pro neověřený přístup k anonymním koncovým bodům.

## <a name="request-additional-access-tokens"></a>Vyžádání dalších přístupových tokenů

Přístupové tokeny lze získat ručně voláním `IAccessTokenProvider.RequestAccessToken` .

V následujícím příkladu jsou vyžadovány další Azure Active Directory (AAD) Microsoft Graph obory rozhraní API pro čtení uživatelských dat a odesílání e-mailů. Po přidání oprávnění rozhraní API Microsoft Graph na portálu Azure AAD jsou další obory nakonfigurované v klientské aplikaci.

`Program.Main` (`Program.cs`):

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

`IAccessTokenProvider.RequestToken`Metoda poskytuje přetížení, které umožňuje aplikaci zřídit přístupový token s danou sadou oborů.

V Razor součásti:

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Vrátí

* `true`s nástrojem `token` pro použití.
* `false`Pokud se token nenačte.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>`HttpClient`a `HttpRequestMessage` s možnostmi požadavku rozhraní API pro načtení

Při spuštění na WebAssembly v Blazor WebAssembly aplikaci [`HttpClient`](xref:fundamentals/http-requests) ([dokumentace k rozhraní API](xref:System.Net.Http.HttpClient)) a <xref:System.Net.Http.HttpRequestMessage> dá se použít k přizpůsobení požadavků. Můžete například zadat metodu HTTP a hlavičku požadavku. Následující komponenta vytvoří požadavek na `POST` koncový bod rozhraní API seznamu na serveru a zobrazí tělo odpovědi:

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

<p>@responseBody</p>

@code {
    private string responseBody;

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

            responseBody = await response.Content.ReadAsStringAsync();
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

Implementace rozhraní .NET WebAssembly pro <xref:System.Net.Http.HttpClient> používá [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Načtení umožňuje nakonfigurovat několik [možností specifických pro požadavky](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Možnosti požadavku HTTP Fetch lze konfigurovat pomocí <xref:System.Net.Http.HttpRequestMessage> rozšiřujících metod, které jsou uvedeny v následující tabulce.

| Metoda rozšíření | Načíst vlastnost žádosti |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Další možnosti můžete nastavit pomocí obecnější <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> metody rozšíření.
 
Odpověď HTTP je obvykle ukládána do vyrovnávací paměti v Blazor WebAssembly aplikaci, aby umožňovala podporu pro čtení v obsahu odpovědi. Pokud chcete povolit podporu pro streamování odpovědí, použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metodu rozšíření v žádosti.

Pokud chcete do žádosti o více zdrojů zahrnout přihlašovací údaje, použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metodu rozšíření:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Další informace o možnostech načtení rozhraní API naleznete v tématu [MDN web Docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)

Při odesílání přihlašovacích údajů (autorizace cookie s/záhlavími) na žádostech CORS `Authorization` musí být záhlaví povoleno zásadami CORS.

Následující zásady zahrnují konfiguraci pro:

* Původ žádosti ( `http://localhost:5000` , `https://localhost:5001` ).
* Libovolná metoda (příkaz).
* `Content-Type`a `Authorization` hlavičky. Chcete-li pro vlastní hlavičku (například `x-custom-header` ), uveďte záhlaví při volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .
* Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta ( `credentials` vlastnost je nastavena na hodnotu `include` ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Hostované Blazor řešení založené na Blazor šabloně hostovaného projektu používá stejnou základní adresu pro klientské a serverové aplikace. Klientská aplikace <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> je ve výchozím nastavení nastavená na identifikátor URI `builder.HostEnvironment.BaseAddress` . Konfigurace **CORS se nevyžaduje** ve výchozí konfiguraci hostované aplikace vytvořené ze Blazor šablony hostovaného projektu. Další klientské aplikace, které nejsou hostované serverovým projektem a nesdílejí **základní adresu aplikace serveru, vyžadují v** projektu serveru konfiguraci CORS.

Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace ( `Components/HTTPRequestTester.razor` ).

## <a name="handle-token-request-errors"></a>Zpracování chyb požadavků na tokeny

Když se jedna stránková aplikace (SPA) ověřuje uživatele pomocí OpenID Connect (OIDC), stav ověřování se udržuje místně v zabezpečeném uživatelském rozhraní (SPA) a v Identity poskytovateli (IP) ve formě relace cookie , která je nastavená jako výsledek uživatele, který poskytuje svoje přihlašovací údaje.

Tokeny, které jsou pro uživatele vysílané, jsou obvykle platné po krátkou dobu přibližně jedna hodina, takže klientská aplikace musí pravidelně načítat nové tokeny. V opačném případě se uživatel odhlásí po vypršení platnosti udělených tokenů. Ve většině případů můžou klienti OIDC zřizovat nové tokeny, aniž by museli znovu ověřovat uživatele díky stavu ověřování nebo "relaci", která se udržuje v rámci IP adresy.

V některých případech může klient získat token bez zásahu uživatele, například pokud z nějakého důvodu se uživatel výslovně odhlásí z IP adresy. K tomuto scénáři dochází, pokud uživatel navštíví `https://login.microsoftonline.com` a odhlásí. V těchto scénářích aplikace neví hned, že se uživatel odhlásil. Libovolný token, který může klient obsahovat, již nemusí být platný. Klient navíc nemůže zřídit nový token bez zásahu uživatele po vypršení platnosti tohoto tokenu.

Tyto scénáře nejsou specifické pro ověřování založené na tokenech. Jsou součástí charakteru jednostránkové. ZABEZPEČENÉ ověřování pomocí protokolu cookie s použitím s také neumožňuje volat rozhraní API serveru, pokud cookie je ověření odebráno.

Když aplikace provede volání rozhraní API k chráněným prostředkům, musíte mít na paměti následující informace:

* Pro zřízení nového přístupového tokenu pro volání rozhraní API může být uživatel požádán o ověření znovu.
* I v případě, že má klient token, který je pravděpodobně platný, volání serveru může selhat, protože byl token odvolán uživatelem.

Když aplikace požaduje token, existují dva možné výsledky:

* Požadavek je úspěšný a aplikace má platný token.
* Požadavek se nezdařil a aplikace musí znovu ověřit uživatele, aby získal nový token.

Pokud se žádost o token nezdařila, musíte se rozhodnout, zda chcete před provedením přesměrování Uložit aktuální stav. Existuje několik přístupů se zvýšenými úrovněmi složitosti:

* Uloží aktuální stav stránky do úložiště relace. Během [ `OnInitializedAsync` události životního cyklu](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) ověřte, zda je možné obnovit stav, než budete pokračovat.
* Přidejte parametr řetězce dotazu a použijte ho jako způsob, jak aplikaci signalizovat, že potřebuje znovu Hydrate dříve uložený stav.
* Přidejte parametr řetězce dotazu s jedinečným identifikátorem pro ukládání dat v úložišti relace bez rizikových kolizí s ostatními položkami.

Následující příklad ukazuje, jak:

* Zachovat stav před přesměrováním na přihlašovací stránku
* Obnovte předchozí stav po ověření pomocí parametru řetězce dotazu.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Uložení stavu aplikace před operací ověřování

Během operace ověřování existují případy, kdy chcete uložit stav aplikace, než se prohlížeč přesměruje na IP adresu. To může být případ, kdy používáte kontejner stavu a chcete obnovit stav po úspěšném ověření. Vlastní objekt stavu ověřování můžete použít k zachování stavu specifického pro aplikaci nebo odkaz na něj a obnovení tohoto stavu po úspěšném dokončení operace ověřování. Následující příklad demonstruje přístup.

V aplikaci se vytvoří třída kontejneru stavů s vlastnostmi, které uchovávají hodnoty stavu aplikace. V následujícím příkladu je kontejner použit k údržbě hodnoty čítače výchozí komponenty šablony projektu `Counter` ( `Pages/Counter.razor` ). Metody pro serializaci a deserializaci kontejneru jsou založeny na <xref:System.Text.Json> .

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

`Counter`Komponenta pomocí kontejneru stavů udržuje `currentCount` hodnotu mimo součást:

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

Vytvořte `ApplicationAuthenticationState` z <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> . Zadejte `Id` vlastnost, která slouží jako identifikátor místně uloženého stavu.

`ApplicationAuthenticationState.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

`Authentication`Součást ( `Pages/Authentication.razor` ) ukládá a obnovuje stav aplikace pomocí úložiště místních relací s `StateContainer` metodami serializace a deserializace a `GetStateForLocalStorage` `SetStateFromLocalStorage` :

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

V tomto příkladu se pro ověřování používá Azure Active Directory (AAD). V `Program.Main` ( `Program.cs` ):

* `ApplicationAuthenticationState`Je nakonfigurovaný jako typ knihovny Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` .
* Kontejner stavů je zaregistrován v kontejneru služby.

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>Přizpůsobení směrování aplikací

Ve výchozím nastavení [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) Knihovna používá trasy, které jsou uvedeny v následující tabulce, pro reprezentace různých stavů ověřování.

| Trasa                            | Účel |
| -------------------------------- | ------- |
| `authentication/login`           | Spustí operaci přihlášení. |
| `authentication/login-callback`  | Zpracovává výsledek jakékoli operace přihlášení. |
| `authentication/login-failed`    | Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace přihlášení. |
| `authentication/logout`          | Spustí operaci odhlášení. |
| `authentication/logout-callback` | Zpracovává výsledek operace odhlášení. |
| `authentication/logout-failed`   | Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace odhlášení. |
| `authentication/logged-out`      | Indikuje, že se uživatel úspěšně odhlásit. |
| `authentication/profile`         | Aktivuje operaci pro úpravu profilu uživatele. |
| `authentication/register`        | Aktivuje operaci pro registraci nového uživatele. |

Trasy zobrazené v předchozí tabulce lze konfigurovat prostřednictvím <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> . Při nastavování možností pro poskytování vlastních tras potvrďte, že aplikace má trasu, která zpracovává jednotlivé cesty.

V následujícím příkladu jsou všechny cesty s předponou `/security` .

`Authentication`součást ( `Pages/Authentication.razor` ):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (`Program.cs`):

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

Pokud požadavek volá zcela různé cesty, nastavte trasy popsané dříve a vykreslete <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> s parametrem explicitní akce:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Pokud se rozhodnete tak učinit, můžete uživatelské rozhraní přerušit na jiné stránky.

## <a name="customize-the-authentication-user-interface"></a>Přizpůsobení uživatelského rozhraní ověřování

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>obsahuje výchozí sadu částí uživatelského rozhraní pro každý stav ověřování. Každý stav lze přizpůsobit předáním vlastního <xref:Microsoft.AspNetCore.Components.RenderFragment> . K přizpůsobení zobrazeného textu během procesu prvotního přihlášení může změnit následující postup <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> .

`Authentication`součást ( `Pages/Authentication.razor` ):

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Má jeden fragment, který se dá použít pro jednu trasu ověřování, jak je znázorněno v následující tabulce.

| Trasa                            | Fragment                |
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

## <a name="customize-the-user"></a>Přizpůsobení uživatele

Uživatele navázané na aplikaci je možné přizpůsobit. V následujícím příkladu obdrží všichni ověření uživatelé `amr` deklaraci identity pro každou metodu ověřování uživatele.

Vytvořte třídu, která rozšiřuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> třídu:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Vytvořte objekt pro vytváření, který rozšiřuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :

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

Zaregistrujte zprostředkovatele ověřování, který se `CustomAccountFactory` používá. Platná jsou následující registrace: 

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

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

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

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
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

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

## <a name="support-prerendering-with-authentication"></a>Podpora předběžného vykreslování s ověřováním

Po provedení pokynů v některém z témat hostovaných Blazor WebAssembly aplikací použijte následující pokyny k vytvoření aplikace, která:

* Předem vykreslí cesty, pro které není nutná autorizace.
* Nejedná se o cesty PreRender, pro které se vyžaduje autorizace.

V části Třída klientské aplikace `Program` ( `Program.cs` ) se služba Factor Common registruje do samostatné metody (například `ConfigureCommonServices` ):

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
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

V serverové aplikaci `Startup.ConfigureServices` Zaregistrujte následující další služby:

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

V metodě serverové aplikace `Startup.Configure` nahraďte [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

V serverové aplikaci vytvořte `Pages` složku, pokud neexistuje. Vytvořte `_Host.cshtml` stránku ve složce serverové aplikace `Pages` . Vložte obsah ze souboru klientské aplikace `wwwroot/index.html` do `Pages/_Host.cshtml` souboru. Aktualizujte obsah souboru:

* Přidejte `@page "_Host"` na začátek souboru.
* Značku nahraďte `<app>Loading...</app>` následujícím:

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Možnosti pro hostované aplikace a poskytovatele přihlašovacích údajů třetích stran

Při ověřování a autorizaci hostované Blazor WebAssembly aplikace s poskytovatelem třetí strany je k dispozici několik možností pro ověření uživatele. Kterou zvolíte, závisí na vašem scénáři.

Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Ověřování uživatelů pro volání rozhraní API chráněných třetích stran

Ověřit uživatele pomocí toku OAuth na straně klienta proti poskytovateli rozhraní API třetí strany:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 V tomto scénáři:

* Server, který je hostitelem aplikace, nehraje roli.
* Rozhraní API na serveru nejde chránit.
* Aplikace může volat jenom chráněná rozhraní API třetích stran.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Ověřování uživatelů pomocí poskytovatele třetí strany a volání chráněných rozhraní API na hostitelském serveru a třetí straně

Nakonfigurujte Identity pomocí poskytovatele přihlášení třetí strany. Získejte tokeny vyžadované pro přístup k rozhraní API třetích stran a uložte je.

Když se uživatel přihlásí, Identity shromažďuje v rámci procesu ověřování tokeny a aktualizace. V tomto okamžiku je k dispozici několik přístupů pro volání rozhraní API třetích stran.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Použití přístupového tokenu serveru k načtení přístupového tokenu třetí strany

K načtení přístupového tokenu třetí strany z koncového bodu rozhraní API serveru použijte přístupový token vygenerovaný na serveru. Odtud můžete pomocí přístupového tokenu třetí strany volat prostředky rozhraní API třetích stran přímo z Identity klienta.

Tento postup nedoporučujeme. Tento přístup vyžaduje ošetření přístupového tokenu třetí strany, jako kdyby byl vygenerován pro veřejného klienta. V případech OAuth nemá veřejná aplikace tajný klíč klienta, protože nemůže být důvěryhodná pro bezpečné ukládání tajných kódů a přístupového tokenu se vytvoří pro důvěrného klienta. Důvěrný klient je klient, který má tajný klíč klienta a předpokládá, že bude moci bezpečně ukládat tajné klíče.

* Přístupovému tokenu třetí strany se můžou udělit další obory, které budou provádět citlivé operace na základě skutečnosti, že třetí strana vygenerovala token pro více důvěryhodných klientů.
* Podobně by se aktualizační tokeny neměly vystavit klientovi, který není důvěryhodný, protože by to tak měl klienta bez omezení přístupu, pokud se na ně neukládají jiná omezení.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Volání rozhraní API z klienta na rozhraní API serveru za účelem volání rozhraní API třetích stran

Naplňte volání rozhraní API z klienta na serverové rozhraní API. Ze serveru načtěte přístupový token pro prostředek rozhraní API třetí strany a vydejte jakékoli volání, které je potřeba.

I když tento přístup vyžaduje další síťové směrování prostřednictvím serveru za účelem volání rozhraní API třetí strany, má za následek bezpečnější prostředí:

* Server může ukládat aktualizační tokeny a zajistit, aby aplikace neztratila přístup k prostředkům třetích stran.
* Aplikace nemůže zajímat přístupové tokeny ze serveru, který může obsahovat citlivá oprávnění.

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Použití koncových bodů OpenID Connect (OIDC) v 2.0

Knihovna ověřování a Blazor šablony projektu používají koncové body OpenID Connect (OIDC) v 1.0. Pokud chcete použít koncový bod v 2.0, nakonfigurujte možnost nosiče JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> . V následujícím příkladu je AAD nakonfigurováno v 2.0 připojením `v2.0` segmentu k <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> vlastnosti:

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

Případně může být nastavení provedeno v souboru nastavení aplikace ( `appsettings.json` ):

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Pokud se označení segmentu pro autoritu nehodí pro poskytovatele OIDC aplikace, jako je třeba u jiných poskytovatelů než AAD, nastavte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> vlastnost přímo. Buď nastavte vlastnost v <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> nebo v souboru nastavení aplikace ( `appsettings.json` ) s `Authority` klíčem.

Seznam deklarací identity v tokenu ID se mění pro koncové body verze 2.0. Další informace najdete v tématu [Proč aktualizace pro Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).

## <a name="configure-and-use-grpc-in-components"></a>Konfigurace a použití gRPC v součástech

Konfigurace Blazor WebAssembly aplikace pro použití [ASP.NET Core gRPC Framework](xref:grpc/index):

* Povolte na serveru gRPC-Web. Další informace naleznete v tématu <xref:grpc/browser>.
* Zaregistrujte služby gRPC pro obslužnou rutinu zpráv aplikace. V následujícím příkladu se nakonfiguruje obslužná rutina autorizační zprávy aplikace, aby používala [ `GreeterClient` službu z kurzu gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) ( `Program.Main` ):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample` ). Umístěte `.proto` soubor do `Shared` projektu hostovaného Blazor řešení.

Komponenta v klientské aplikaci může volat gRPC pomocí klienta gRPC ( `Pages/Grpc.razor` ):

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample` ). Chcete-li použít `Status.DebugException` vlastnost, použijte [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) verze 2.30.0 nebo novější.

Další informace naleznete v tématu <xref:grpc/browser>.
