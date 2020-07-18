---
title: ASP.NET Core Blazor WebAssembly Další scénáře zabezpečení
author: guardrex
description: Naučte se konfigurovat Blazor WebAssembly pro další scénáře zabezpečení.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 79f7b2177d6d07101c73cde841c062b0e1468593
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445148"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="8d50e-103">ASP.NET Core Blazor WebAssembly Další scénáře zabezpečení</span><span class="sxs-lookup"><span data-stu-id="8d50e-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="8d50e-104">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8d50e-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="8d50e-105">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="8d50e-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="8d50e-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Službu lze použít s nástrojem <xref:System.Net.Http.HttpClient> k připojení přístupových tokenů k odchozím žádostem.</span><span class="sxs-lookup"><span data-stu-id="8d50e-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="8d50e-107">Tokeny se získávají pomocí existující <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> služby.</span><span class="sxs-lookup"><span data-stu-id="8d50e-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="8d50e-108">Pokud token nelze získat, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="8d50e-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="8d50e-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>má <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> metodu, která se dá použít k navigaci uživatele na poskytovatele identity za účelem získání nového tokenu.</span><span class="sxs-lookup"><span data-stu-id="8d50e-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="8d50e-110"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Pomocí metody lze konfigurovat pomocí autorizovaných adres URL, oborů a návratové adresy URL <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="8d50e-111">Ke konfiguraci obslužné rutiny zpráv pro odchozí požadavky použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="8d50e-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="8d50e-112">[Vlastní `AuthorizationMessageHandler` Třída](#custom-authorizationmessagehandler-class) (*doporučeno*)</span><span class="sxs-lookup"><span data-stu-id="8d50e-112">[Custom `AuthorizationMessageHandler` class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="8d50e-113">Konfigurace`AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="8d50e-113">Configure `AuthorizationMessageHandler`</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="8d50e-114">Vlastní třída AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="8d50e-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="8d50e-115">V následujícím příkladu vlastní třída rozšiřuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , která může být použita ke konfiguraci <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="8d50e-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

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

<span data-ttu-id="8d50e-116">V `Program.Main` ( `Program.cs` ) <xref:System.Net.Http.HttpClient> je nakonfigurován s vlastní obslužnou rutinou ověřovací zprávy:</span><span class="sxs-lookup"><span data-stu-id="8d50e-116">In `Program.Main` (`Program.cs`), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="8d50e-117">Pro Blazor aplikaci založenou na Blazor WebAssembly hostované šabloně <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> () je `new Uri(builder.HostEnvironment.BaseAddress)` možné přiřadit k <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-117">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="8d50e-118">Nakonfigurovaná <xref:System.Net.Http.HttpClient> se používá k provádění autorizovaných požadavků pomocí [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) vzoru.</span><span class="sxs-lookup"><span data-stu-id="8d50e-118">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="8d50e-119">V případě, že je klient vytvořen pomocí nástroje <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) balíček), <xref:System.Net.Http.HttpClient> jsou dodány instance, které zahrnují přístupové tokeny při vytváření požadavků na rozhraní API serveru:</span><span class="sxs-lookup"><span data-stu-id="8d50e-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

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

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="8d50e-120">Konfigurace AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="8d50e-120">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="8d50e-121">V následujícím příkladu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> nakonfiguruje <xref:System.Net.Http.HttpClient> v `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="8d50e-121">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="8d50e-122">Pro Blazor aplikaci založenou na Blazor WebAssembly hostované šabloně se <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> dá přiřadit:</span><span class="sxs-lookup"><span data-stu-id="8d50e-122">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="8d50e-123">Rozhraní <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="8d50e-123">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="8d50e-124">Adresa URL `authorizedUrls` pole</span><span class="sxs-lookup"><span data-stu-id="8d50e-124">A URL of the `authorizedUrls` array.</span></span>

<span data-ttu-id="8d50e-125">Pro usnadnění práce <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> je součástí předem nakonfigurovaná základní adresa aplikace jako autorizovaná adresa URL.</span><span class="sxs-lookup"><span data-stu-id="8d50e-125">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="8d50e-126">Šablony s povoleným ověřováním Blazor WebAssembly používají <xref:System.Net.Http.IHttpClientFactory> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) balíček) v projektu rozhraní API serveru k nastavení <xref:System.Net.Http.HttpClient> pomocí <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="8d50e-126">The authentication-enabled Blazor WebAssembly templates use <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="8d50e-127">Pro Blazor aplikaci založenou na Blazor WebAssembly hostované šabloně <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> () je `new Uri(builder.HostEnvironment.BaseAddress)` možné přiřadit k <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-127">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="8d50e-128">Tam, kde je klient vytvořen pomocí <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> v předchozím příkladu, se <xref:System.Net.Http.HttpClient> dodávají instance, které zahrnují přístupové tokeny při vytváření požadavků na serverový projekt.</span><span class="sxs-lookup"><span data-stu-id="8d50e-128">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="8d50e-129">Nakonfigurovaná <xref:System.Net.Http.HttpClient> se používá k provádění autorizovaných požadavků pomocí [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) vzoru:</span><span class="sxs-lookup"><span data-stu-id="8d50e-129">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="8d50e-130">Typové HttpClient</span><span class="sxs-lookup"><span data-stu-id="8d50e-130">Typed HttpClient</span></span>

<span data-ttu-id="8d50e-131">Je možné definovat zadaného klienta, který zpracovává všechny aspekty získání HTTP a tokenu v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="8d50e-131">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="8d50e-132">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="8d50e-132">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="8d50e-133">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `using static BlazorSample.Data;` ).</span><span class="sxs-lookup"><span data-stu-id="8d50e-133">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="8d50e-134">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="8d50e-134">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="8d50e-135">Pro Blazor aplikaci založenou na Blazor WebAssembly hostované šabloně <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> () je `new Uri(builder.HostEnvironment.BaseAddress)` možné přiřadit k <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-135">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="8d50e-136">`FetchData`součást ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="8d50e-136">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="8d50e-137">Konfigurace obslužné rutiny HttpClient</span><span class="sxs-lookup"><span data-stu-id="8d50e-137">Configure the HttpClient handler</span></span>

<span data-ttu-id="8d50e-138">Obslužná rutina může být dále nakonfigurována <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="8d50e-138">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="8d50e-139">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="8d50e-139">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="8d50e-140">Pro Blazor aplikaci založenou na Blazor WebAssembly hostované šabloně se <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> dá přiřadit:</span><span class="sxs-lookup"><span data-stu-id="8d50e-140">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="8d50e-141">Rozhraní <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="8d50e-141">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="8d50e-142">Adresa URL `authorizedUrls` pole</span><span class="sxs-lookup"><span data-stu-id="8d50e-142">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="8d50e-143">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="8d50e-143">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="8d50e-144">Pokud Blazor WebAssembly aplikace běžně používá zabezpečené výchozí nastavení <xref:System.Net.Http.HttpClient> , může také aplikace neověřené nebo neautorizované požadavky webového rozhraní API provést konfigurací pojmenovaného <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="8d50e-144">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="8d50e-145">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="8d50e-145">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="8d50e-146">Pro Blazor aplikaci založenou na Blazor WebAssembly hostované šabloně <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> () je `new Uri(builder.HostEnvironment.BaseAddress)` možné přiřadit k <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-146">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="8d50e-147">Předchozí registrace je kromě existující zabezpečené výchozí <xref:System.Net.Http.HttpClient> registrace.</span><span class="sxs-lookup"><span data-stu-id="8d50e-147">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="8d50e-148">Komponenta vytvoří <xref:System.Net.Http.HttpClient> z <xref:System.Net.Http.IHttpClientFactory> [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) balíčku (balíček) k provedení neověřených nebo neautorizovaných požadavků:</span><span class="sxs-lookup"><span data-stu-id="8d50e-148">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="8d50e-149">Řadič v rozhraní API serveru `WeatherForecastNoAuthenticationController` pro předchozí příklad není označený [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="8d50e-149">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="8d50e-150">Rozhodnutí, jestli se má použít zabezpečený klient nebo nezabezpečený klient, protože výchozí <xref:System.Net.Http.HttpClient> instance je až pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="8d50e-150">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="8d50e-151">Jedním ze způsobů, jak toto rozhodnutí udělat, je zvážit počet ověřených i neověřených koncových bodů, které aplikace kontaktuje.</span><span class="sxs-lookup"><span data-stu-id="8d50e-151">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="8d50e-152">Pokud většina požadavků aplikace má zabezpečit koncové body rozhraní API, použijte <xref:System.Net.Http.HttpClient> jako výchozí instanci ověřenou.</span><span class="sxs-lookup"><span data-stu-id="8d50e-152">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="8d50e-153">V opačném případě Zaregistrujte neověřenou <xref:System.Net.Http.HttpClient> instanci jako výchozí.</span><span class="sxs-lookup"><span data-stu-id="8d50e-153">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="8d50e-154">Alternativním přístupem k použití <xref:System.Net.Http.IHttpClientFactory> je vytvoření [typu klient](#typed-httpclient) pro neověřený přístup k anonymním koncovým bodům.</span><span class="sxs-lookup"><span data-stu-id="8d50e-154">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="8d50e-155">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="8d50e-155">Request additional access tokens</span></span>

<span data-ttu-id="8d50e-156">Přístupové tokeny lze získat ručně voláním `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="8d50e-156">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="8d50e-157">V následujícím příkladu jsou vyžadovány další Azure Active Directory (AAD) Microsoft Graph obory rozhraní API pro čtení uživatelských dat a odesílání e-mailů.</span><span class="sxs-lookup"><span data-stu-id="8d50e-157">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="8d50e-158">Po přidání oprávnění rozhraní API Microsoft Graph na portálu Azure AAD jsou další obory nakonfigurované v klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8d50e-158">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="8d50e-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="8d50e-159">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="8d50e-160">`IAccessTokenProvider.RequestToken`Metoda poskytuje přetížení, které umožňuje aplikaci zřídit přístupový token s danou sadou oborů.</span><span class="sxs-lookup"><span data-stu-id="8d50e-160">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="8d50e-161">V Razor součásti:</span><span class="sxs-lookup"><span data-stu-id="8d50e-161">In a Razor component:</span></span>

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

<span data-ttu-id="8d50e-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Vrátí</span><span class="sxs-lookup"><span data-stu-id="8d50e-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="8d50e-163">`true`s nástrojem `token` pro použití.</span><span class="sxs-lookup"><span data-stu-id="8d50e-163">`true` with the `token` for use.</span></span>
* <span data-ttu-id="8d50e-164">`false`Pokud se token nenačte.</span><span class="sxs-lookup"><span data-stu-id="8d50e-164">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="8d50e-165">HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení</span><span class="sxs-lookup"><span data-stu-id="8d50e-165">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="8d50e-166">Při spuštění na WebAssembly v Blazor WebAssembly aplikaci [`HttpClient`](xref:fundamentals/http-requests) ([dokumentace k rozhraní API](xref:System.Net.Http.HttpClient)) a <xref:System.Net.Http.HttpRequestMessage> dá se použít k přizpůsobení požadavků.</span><span class="sxs-lookup"><span data-stu-id="8d50e-166">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="8d50e-167">Můžete například zadat metodu HTTP a hlavičku požadavku.</span><span class="sxs-lookup"><span data-stu-id="8d50e-167">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="8d50e-168">Následující komponenta vytvoří požadavek na `POST` koncový bod rozhraní API seznamu na serveru a zobrazí tělo odpovědi:</span><span class="sxs-lookup"><span data-stu-id="8d50e-168">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="8d50e-169">Implementace rozhraní .NET WebAssembly pro <xref:System.Net.Http.HttpClient> používá [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="8d50e-169">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="8d50e-170">Načtení umožňuje nakonfigurovat několik [možností specifických pro požadavky](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="8d50e-170">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="8d50e-171">Možnosti požadavku HTTP Fetch lze konfigurovat pomocí <xref:System.Net.Http.HttpRequestMessage> rozšiřujících metod, které jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="8d50e-171">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="8d50e-172">Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="8d50e-172">Extension method</span></span> | <span data-ttu-id="8d50e-173">Načíst vlastnost žádosti</span><span class="sxs-lookup"><span data-stu-id="8d50e-173">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="8d50e-174">Další možnosti můžete nastavit pomocí obecnější <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="8d50e-174">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="8d50e-175">Odpověď HTTP je obvykle ukládána do vyrovnávací paměti v Blazor WebAssembly aplikaci, aby umožňovala podporu pro čtení v obsahu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="8d50e-175">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="8d50e-176">Pokud chcete povolit podporu pro streamování odpovědí, použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metodu rozšíření v žádosti.</span><span class="sxs-lookup"><span data-stu-id="8d50e-176">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="8d50e-177">Pokud chcete do žádosti o více zdrojů zahrnout přihlašovací údaje, použijte <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="8d50e-177">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="8d50e-178">Další informace o možnostech načtení rozhraní API naleznete v tématu [MDN web Docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="8d50e-178">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="8d50e-179">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="8d50e-179">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="8d50e-180">Při odesílání přihlašovacích údajů (souborů cookie autorizace/hlaviček) na žádostech CORS `Authorization` musí být záhlaví povoleno zásadami CORS.</span><span class="sxs-lookup"><span data-stu-id="8d50e-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="8d50e-181">Následující zásady zahrnují konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="8d50e-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="8d50e-182">Původ žádosti ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="8d50e-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="8d50e-183">Libovolná metoda (příkaz).</span><span class="sxs-lookup"><span data-stu-id="8d50e-183">Any method (verb).</span></span>
* <span data-ttu-id="8d50e-184">`Content-Type`a `Authorization` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="8d50e-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="8d50e-185">Chcete-li pro vlastní hlavičku (například `x-custom-header` ), uveďte záhlaví při volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="8d50e-186">Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta ( `credentials` vlastnost je nastavena na hodnotu `include` ).</span><span class="sxs-lookup"><span data-stu-id="8d50e-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="8d50e-187">Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace ( `Components/HTTPRequestTester.razor` ).</span><span class="sxs-lookup"><span data-stu-id="8d50e-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="8d50e-188">Zpracování chyb požadavků na tokeny</span><span class="sxs-lookup"><span data-stu-id="8d50e-188">Handle token request errors</span></span>

<span data-ttu-id="8d50e-189">Když jedna stránková aplikace (SPA) ověřuje uživatele pomocí funkce Open ID Connect (OIDC), je stav ověřování udržován místně v zabezpečeném uživatelském rozhraní (SPA) a ve Identity zprostředkovateli (IP) ve formě souboru cookie relace, který je nastaven jako výsledek uživatele, který poskytuje své přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="8d50e-189">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="8d50e-190">Tokeny, které jsou pro uživatele vysílané, jsou obvykle platné po krátkou dobu přibližně jedna hodina, takže klientská aplikace musí pravidelně načítat nové tokeny.</span><span class="sxs-lookup"><span data-stu-id="8d50e-190">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="8d50e-191">V opačném případě se uživatel odhlásí po vypršení platnosti udělených tokenů.</span><span class="sxs-lookup"><span data-stu-id="8d50e-191">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="8d50e-192">Ve většině případů můžou klienti OIDC zřizovat nové tokeny, aniž by museli znovu ověřovat uživatele díky stavu ověřování nebo "relaci", která se udržuje v rámci IP adresy.</span><span class="sxs-lookup"><span data-stu-id="8d50e-192">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="8d50e-193">V některých případech může klient získat token bez zásahu uživatele, například pokud z nějakého důvodu se uživatel výslovně odhlásí z IP adresy.</span><span class="sxs-lookup"><span data-stu-id="8d50e-193">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="8d50e-194">K tomuto scénáři dochází, pokud uživatel navštíví `https://login.microsoftonline.com` a odhlásí. V těchto scénářích aplikace neví hned, že se uživatel odhlásil. Libovolný token, který může klient obsahovat, již nemusí být platný.</span><span class="sxs-lookup"><span data-stu-id="8d50e-194">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="8d50e-195">Klient navíc nemůže zřídit nový token bez zásahu uživatele po vypršení platnosti tohoto tokenu.</span><span class="sxs-lookup"><span data-stu-id="8d50e-195">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="8d50e-196">Tyto scénáře nejsou specifické pro ověřování založené na tokenech.</span><span class="sxs-lookup"><span data-stu-id="8d50e-196">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="8d50e-197">Jsou součástí charakteru jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="8d50e-197">They are part of the nature of SPAs.</span></span> <span data-ttu-id="8d50e-198">ZABEZPEČENÉ ověřování pomocí souborů cookie také nedokáže volat rozhraní API serveru, pokud je soubor cookie ověření odebrán.</span><span class="sxs-lookup"><span data-stu-id="8d50e-198">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="8d50e-199">Když aplikace provede volání rozhraní API k chráněným prostředkům, musíte mít na paměti následující informace:</span><span class="sxs-lookup"><span data-stu-id="8d50e-199">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="8d50e-200">Pro zřízení nového přístupového tokenu pro volání rozhraní API může být uživatel požádán o ověření znovu.</span><span class="sxs-lookup"><span data-stu-id="8d50e-200">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="8d50e-201">I v případě, že má klient token, který je pravděpodobně platný, volání serveru může selhat, protože byl token odvolán uživatelem.</span><span class="sxs-lookup"><span data-stu-id="8d50e-201">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="8d50e-202">Když aplikace požaduje token, existují dva možné výsledky:</span><span class="sxs-lookup"><span data-stu-id="8d50e-202">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="8d50e-203">Požadavek je úspěšný a aplikace má platný token.</span><span class="sxs-lookup"><span data-stu-id="8d50e-203">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="8d50e-204">Požadavek se nezdařil a aplikace musí znovu ověřit uživatele, aby získal nový token.</span><span class="sxs-lookup"><span data-stu-id="8d50e-204">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="8d50e-205">Pokud se žádost o token nezdařila, musíte se rozhodnout, zda chcete před provedením přesměrování Uložit aktuální stav.</span><span class="sxs-lookup"><span data-stu-id="8d50e-205">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="8d50e-206">Existuje několik přístupů se zvýšenými úrovněmi složitosti:</span><span class="sxs-lookup"><span data-stu-id="8d50e-206">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="8d50e-207">Uloží aktuální stav stránky do úložiště relace.</span><span class="sxs-lookup"><span data-stu-id="8d50e-207">Store the current page state in session storage.</span></span> <span data-ttu-id="8d50e-208">Během [ `OnInitializedAsync` události životního cyklu](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) ověřte, zda je možné obnovit stav, než budete pokračovat.</span><span class="sxs-lookup"><span data-stu-id="8d50e-208">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="8d50e-209">Přidejte parametr řetězce dotazu a použijte ho jako způsob, jak aplikaci signalizovat, že potřebuje znovu Hydrate dříve uložený stav.</span><span class="sxs-lookup"><span data-stu-id="8d50e-209">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="8d50e-210">Přidejte parametr řetězce dotazu s jedinečným identifikátorem pro ukládání dat v úložišti relace bez rizikových kolizí s ostatními položkami.</span><span class="sxs-lookup"><span data-stu-id="8d50e-210">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="8d50e-211">Následující příklad ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="8d50e-211">The following example shows how to:</span></span>

* <span data-ttu-id="8d50e-212">Zachovat stav před přesměrováním na přihlašovací stránku</span><span class="sxs-lookup"><span data-stu-id="8d50e-212">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="8d50e-213">Obnovte předchozí stav po ověření pomocí parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="8d50e-213">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="8d50e-214">Uložení stavu aplikace před operací ověřování</span><span class="sxs-lookup"><span data-stu-id="8d50e-214">Save app state before an authentication operation</span></span>

<span data-ttu-id="8d50e-215">Během operace ověřování existují případy, kdy chcete uložit stav aplikace, než se prohlížeč přesměruje na IP adresu.</span><span class="sxs-lookup"><span data-stu-id="8d50e-215">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="8d50e-216">To může být případ, kdy používáte kontejner stavu a chcete obnovit stav po úspěšném ověření.</span><span class="sxs-lookup"><span data-stu-id="8d50e-216">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="8d50e-217">Vlastní objekt stavu ověřování můžete použít k zachování stavu specifického pro aplikaci nebo odkaz na něj a obnovení tohoto stavu po úspěšném dokončení operace ověřování.</span><span class="sxs-lookup"><span data-stu-id="8d50e-217">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="8d50e-218">Následující příklad demonstruje přístup.</span><span class="sxs-lookup"><span data-stu-id="8d50e-218">The following example demonstrates the approach.</span></span>

<span data-ttu-id="8d50e-219">V aplikaci se vytvoří třída kontejneru stavů s vlastnostmi, které uchovávají hodnoty stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="8d50e-219">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="8d50e-220">V následujícím příkladu se kontejner používá k údržbě hodnoty čítače pro komponentu výchozí šablony `Counter` ( `Pages/Counter.razor` ).</span><span class="sxs-lookup"><span data-stu-id="8d50e-220">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="8d50e-221">Metody pro serializaci a deserializaci kontejneru jsou založeny na <xref:System.Text.Json> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-221">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="8d50e-222">`Counter`Komponenta pomocí kontejneru stavů udržuje `currentCount` hodnotu mimo součást:</span><span class="sxs-lookup"><span data-stu-id="8d50e-222">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="8d50e-223">Vytvořte `ApplicationAuthenticationState` z <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-223">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="8d50e-224">Zadejte `Id` vlastnost, která slouží jako identifikátor místně uloženého stavu.</span><span class="sxs-lookup"><span data-stu-id="8d50e-224">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="8d50e-225">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="8d50e-225">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="8d50e-226">`Authentication`Součást ( `Pages/Authentication.razor` ) ukládá a obnovuje stav aplikace pomocí úložiště místních relací s `StateContainer` metodami serializace a deserializace a `GetStateForLocalStorage` `SetStateFromLocalStorage` :</span><span class="sxs-lookup"><span data-stu-id="8d50e-226">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="8d50e-227">V tomto příkladu se pro ověřování používá Azure Active Directory (AAD).</span><span class="sxs-lookup"><span data-stu-id="8d50e-227">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="8d50e-228">V `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="8d50e-228">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="8d50e-229">`ApplicationAuthenticationState`Je nakonfigurovaný jako typ knihovny Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` .</span><span class="sxs-lookup"><span data-stu-id="8d50e-229">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="8d50e-230">Kontejner stavů je zaregistrován v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="8d50e-230">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="8d50e-231">Přizpůsobení směrování aplikací</span><span class="sxs-lookup"><span data-stu-id="8d50e-231">Customize app routes</span></span>

<span data-ttu-id="8d50e-232">Ve výchozím nastavení [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) Knihovna používá trasy, které jsou uvedeny v následující tabulce, pro reprezentace různých stavů ověřování.</span><span class="sxs-lookup"><span data-stu-id="8d50e-232">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="8d50e-233">Trasa</span><span class="sxs-lookup"><span data-stu-id="8d50e-233">Route</span></span>                            | <span data-ttu-id="8d50e-234">Účel</span><span class="sxs-lookup"><span data-stu-id="8d50e-234">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="8d50e-235">Spustí operaci přihlášení.</span><span class="sxs-lookup"><span data-stu-id="8d50e-235">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="8d50e-236">Zpracovává výsledek jakékoli operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="8d50e-236">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="8d50e-237">Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="8d50e-237">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="8d50e-238">Spustí operaci odhlášení.</span><span class="sxs-lookup"><span data-stu-id="8d50e-238">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="8d50e-239">Zpracovává výsledek operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="8d50e-239">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="8d50e-240">Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="8d50e-240">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="8d50e-241">Indikuje, že se uživatel úspěšně odhlásit.</span><span class="sxs-lookup"><span data-stu-id="8d50e-241">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="8d50e-242">Aktivuje operaci pro úpravu profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="8d50e-242">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="8d50e-243">Aktivuje operaci pro registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="8d50e-243">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="8d50e-244">Trasy zobrazené v předchozí tabulce lze konfigurovat prostřednictvím <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-244">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="8d50e-245">Při nastavování možností pro poskytování vlastních tras potvrďte, že aplikace má trasu, která zpracovává jednotlivé cesty.</span><span class="sxs-lookup"><span data-stu-id="8d50e-245">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="8d50e-246">V následujícím příkladu jsou všechny cesty s předponou `/security` .</span><span class="sxs-lookup"><span data-stu-id="8d50e-246">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="8d50e-247">`Authentication`součást ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="8d50e-247">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="8d50e-248">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="8d50e-248">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="8d50e-249">Pokud požadavek volá zcela různé cesty, nastavte trasy popsané dříve a vykreslete <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> s parametrem explicitní akce:</span><span class="sxs-lookup"><span data-stu-id="8d50e-249">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="8d50e-250">Pokud se rozhodnete tak učinit, můžete uživatelské rozhraní přerušit na jiné stránky.</span><span class="sxs-lookup"><span data-stu-id="8d50e-250">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="8d50e-251">Přizpůsobení uživatelského rozhraní ověřování</span><span class="sxs-lookup"><span data-stu-id="8d50e-251">Customize the authentication user interface</span></span>

<span data-ttu-id="8d50e-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>obsahuje výchozí sadu částí uživatelského rozhraní pro každý stav ověřování.</span><span class="sxs-lookup"><span data-stu-id="8d50e-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="8d50e-253">Každý stav lze přizpůsobit předáním vlastního <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-253">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="8d50e-254">K přizpůsobení zobrazeného textu během procesu prvotního přihlášení může změnit následující postup <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-254">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="8d50e-255">`Authentication`součást ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="8d50e-255">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="8d50e-256"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Má jeden fragment, který se dá použít pro jednu trasu ověřování, jak je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="8d50e-256">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="8d50e-257">Trasa</span><span class="sxs-lookup"><span data-stu-id="8d50e-257">Route</span></span>                            | <span data-ttu-id="8d50e-258">Fragment</span><span class="sxs-lookup"><span data-stu-id="8d50e-258">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="8d50e-259">Přizpůsobení uživatele</span><span class="sxs-lookup"><span data-stu-id="8d50e-259">Customize the user</span></span>

<span data-ttu-id="8d50e-260">Uživatele navázané na aplikaci je možné přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="8d50e-260">Users bound to the app can be customized.</span></span> <span data-ttu-id="8d50e-261">V následujícím příkladu obdrží všichni ověření uživatelé `amr` deklaraci identity pro každou metodu ověřování uživatele.</span><span class="sxs-lookup"><span data-stu-id="8d50e-261">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="8d50e-262">Vytvořte třídu, která rozšiřuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> třídu:</span><span class="sxs-lookup"><span data-stu-id="8d50e-262">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="8d50e-263">Vytvořte objekt pro vytváření, který rozšiřuje <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="8d50e-263">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="8d50e-264">Zaregistrujte zprostředkovatele ověřování, který se `CustomAccountFactory` používá.</span><span class="sxs-lookup"><span data-stu-id="8d50e-264">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="8d50e-265">Platná jsou následující registrace:</span><span class="sxs-lookup"><span data-stu-id="8d50e-265">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="8d50e-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="8d50e-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="8d50e-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="8d50e-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="8d50e-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="8d50e-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="8d50e-269">Podpora předběžného vykreslování s ověřováním</span><span class="sxs-lookup"><span data-stu-id="8d50e-269">Support prerendering with authentication</span></span>

<span data-ttu-id="8d50e-270">Po provedení pokynů v některém z témat hostovaných Blazor WebAssembly aplikací použijte následující pokyny k vytvoření aplikace, která:</span><span class="sxs-lookup"><span data-stu-id="8d50e-270">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="8d50e-271">Předem vykreslí cesty, pro které není nutná autorizace.</span><span class="sxs-lookup"><span data-stu-id="8d50e-271">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="8d50e-272">Nejedná se o cesty PreRender, pro které se vyžaduje autorizace.</span><span class="sxs-lookup"><span data-stu-id="8d50e-272">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="8d50e-273">V části Třída klientské aplikace `Program` ( `Program.cs` ) se služba Factor Common registruje do samostatné metody (například `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="8d50e-273">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="8d50e-274">V serverové aplikaci `Startup.ConfigureServices` Zaregistrujte následující další služby:</span><span class="sxs-lookup"><span data-stu-id="8d50e-274">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="8d50e-275">V metodě serverové aplikace `Startup.Configure` nahraďte [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :</span><span class="sxs-lookup"><span data-stu-id="8d50e-275">In the Server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="8d50e-276">V serverové aplikaci vytvořte `Pages` složku, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="8d50e-276">In the Server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="8d50e-277">Vytvořte `_Host.cshtml` stránku ve složce serverové aplikace `Pages` .</span><span class="sxs-lookup"><span data-stu-id="8d50e-277">Create a `_Host.cshtml` page inside the Server app's `Pages` folder.</span></span> <span data-ttu-id="8d50e-278">Vložte obsah ze souboru klientské aplikace `wwwroot/index.html` do `Pages/_Host.cshtml` souboru.</span><span class="sxs-lookup"><span data-stu-id="8d50e-278">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="8d50e-279">Aktualizujte obsah souboru:</span><span class="sxs-lookup"><span data-stu-id="8d50e-279">Update the file's contents:</span></span>

* <span data-ttu-id="8d50e-280">Přidejte `@page "_Host"` na začátek souboru.</span><span class="sxs-lookup"><span data-stu-id="8d50e-280">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="8d50e-281">Značku nahraďte `<app>Loading...</app>` následujícím:</span><span class="sxs-lookup"><span data-stu-id="8d50e-281">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="8d50e-282">Možnosti pro hostované aplikace a poskytovatele přihlašovacích údajů třetích stran</span><span class="sxs-lookup"><span data-stu-id="8d50e-282">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="8d50e-283">Při ověřování a autorizaci hostované Blazor WebAssembly aplikace s poskytovatelem třetí strany je k dispozici několik možností pro ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="8d50e-283">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="8d50e-284">Kterou zvolíte, závisí na vašem scénáři.</span><span class="sxs-lookup"><span data-stu-id="8d50e-284">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="8d50e-285">Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="8d50e-285">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="8d50e-286">Ověřování uživatelů pro volání rozhraní API chráněných třetích stran</span><span class="sxs-lookup"><span data-stu-id="8d50e-286">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="8d50e-287">Ověřit uživatele pomocí toku OAuth na straně klienta proti poskytovateli rozhraní API třetí strany:</span><span class="sxs-lookup"><span data-stu-id="8d50e-287">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="8d50e-288">V tomto scénáři:</span><span class="sxs-lookup"><span data-stu-id="8d50e-288">In this scenario:</span></span>

* <span data-ttu-id="8d50e-289">Server, který je hostitelem aplikace, nehraje roli.</span><span class="sxs-lookup"><span data-stu-id="8d50e-289">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="8d50e-290">Rozhraní API na serveru nejde chránit.</span><span class="sxs-lookup"><span data-stu-id="8d50e-290">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="8d50e-291">Aplikace může volat jenom chráněná rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="8d50e-291">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="8d50e-292">Ověřování uživatelů pomocí poskytovatele třetí strany a volání chráněných rozhraní API na hostitelském serveru a třetí straně</span><span class="sxs-lookup"><span data-stu-id="8d50e-292">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="8d50e-293">Nakonfigurujte Identity pomocí poskytovatele přihlášení třetí strany.</span><span class="sxs-lookup"><span data-stu-id="8d50e-293">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="8d50e-294">Získejte tokeny vyžadované pro přístup k rozhraní API třetích stran a uložte je.</span><span class="sxs-lookup"><span data-stu-id="8d50e-294">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="8d50e-295">Když se uživatel přihlásí, Identity shromažďuje v rámci procesu ověřování tokeny a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="8d50e-295">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="8d50e-296">V tomto okamžiku je k dispozici několik přístupů pro volání rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="8d50e-296">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="8d50e-297">Použití přístupového tokenu serveru k načtení přístupového tokenu třetí strany</span><span class="sxs-lookup"><span data-stu-id="8d50e-297">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="8d50e-298">K načtení přístupového tokenu třetí strany z koncového bodu rozhraní API serveru použijte přístupový token vygenerovaný na serveru.</span><span class="sxs-lookup"><span data-stu-id="8d50e-298">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="8d50e-299">Odtud můžete pomocí přístupového tokenu třetí strany volat prostředky rozhraní API třetích stran přímo z Identity klienta.</span><span class="sxs-lookup"><span data-stu-id="8d50e-299">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="8d50e-300">Tento postup nedoporučujeme.</span><span class="sxs-lookup"><span data-stu-id="8d50e-300">We don't recommend this approach.</span></span> <span data-ttu-id="8d50e-301">Tento přístup vyžaduje ošetření přístupového tokenu třetí strany, jako kdyby byl vygenerován pro veřejného klienta.</span><span class="sxs-lookup"><span data-stu-id="8d50e-301">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="8d50e-302">V případech OAuth nemá veřejná aplikace tajný klíč klienta, protože nemůže být důvěryhodná pro bezpečné ukládání tajných kódů a přístupového tokenu se vytvoří pro důvěrného klienta.</span><span class="sxs-lookup"><span data-stu-id="8d50e-302">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="8d50e-303">Důvěrný klient je klient, který má tajný klíč klienta a předpokládá, že bude moci bezpečně ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="8d50e-303">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="8d50e-304">Přístupovému tokenu třetí strany se můžou udělit další obory, které budou provádět citlivé operace na základě skutečnosti, že třetí strana vygenerovala token pro více důvěryhodných klientů.</span><span class="sxs-lookup"><span data-stu-id="8d50e-304">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="8d50e-305">Podobně by se aktualizační tokeny neměly vystavit klientovi, který není důvěryhodný, protože by to tak měl klienta bez omezení přístupu, pokud se na ně neukládají jiná omezení.</span><span class="sxs-lookup"><span data-stu-id="8d50e-305">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="8d50e-306">Volání rozhraní API z klienta na rozhraní API serveru za účelem volání rozhraní API třetích stran</span><span class="sxs-lookup"><span data-stu-id="8d50e-306">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="8d50e-307">Naplňte volání rozhraní API z klienta na serverové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="8d50e-307">Make an API call from the client to the server API.</span></span> <span data-ttu-id="8d50e-308">Ze serveru načtěte přístupový token pro prostředek rozhraní API třetí strany a vydejte jakékoli volání, které je potřeba.</span><span class="sxs-lookup"><span data-stu-id="8d50e-308">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="8d50e-309">I když tento přístup vyžaduje další síťové směrování prostřednictvím serveru za účelem volání rozhraní API třetí strany, má za následek bezpečnější prostředí:</span><span class="sxs-lookup"><span data-stu-id="8d50e-309">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="8d50e-310">Server může ukládat aktualizační tokeny a zajistit, aby aplikace neztratila přístup k prostředkům třetích stran.</span><span class="sxs-lookup"><span data-stu-id="8d50e-310">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="8d50e-311">Aplikace nemůže zajímat přístupové tokeny ze serveru, který může obsahovat citlivá oprávnění.</span><span class="sxs-lookup"><span data-stu-id="8d50e-311">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="8d50e-312">Použití koncových bodů Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="8d50e-312">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="8d50e-313">Knihovna a šablony ověřování Blazor používají koncové body Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="8d50e-313">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="8d50e-314">Pokud chcete použít koncový bod v 2.0, nakonfigurujte možnost nosiče JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8d50e-314">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="8d50e-315">V následujícím příkladu je AAD nakonfigurováno v 2.0 připojením `v2.0` segmentu k <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="8d50e-315">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="8d50e-316">Případně může být nastavení provedeno v souboru nastavení aplikace ( `appsettings.json` ):</span><span class="sxs-lookup"><span data-stu-id="8d50e-316">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="8d50e-317">Pokud se označení segmentu pro autoritu nehodí pro poskytovatele OIDC aplikace, jako je třeba u jiných poskytovatelů než AAD, nastavte <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> vlastnost přímo.</span><span class="sxs-lookup"><span data-stu-id="8d50e-317">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="8d50e-318">Buď nastavte vlastnost v <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> nebo v souboru nastavení aplikace ( `appsettings.json` ) s `Authority` klíčem.</span><span class="sxs-lookup"><span data-stu-id="8d50e-318">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="8d50e-319">Seznam deklarací identity v tokenu ID se mění pro koncové body verze 2.0.</span><span class="sxs-lookup"><span data-stu-id="8d50e-319">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="8d50e-320">Další informace najdete v tématu [Proč aktualizace pro Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="8d50e-320">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="8d50e-321">Konfigurace a použití gRPC v součástech</span><span class="sxs-lookup"><span data-stu-id="8d50e-321">Configure and use gRPC in components</span></span>

<span data-ttu-id="8d50e-322">Konfigurace Blazor WebAssembly aplikace pro použití [ASP.NET Core gRPC Framework](xref:grpc/index):</span><span class="sxs-lookup"><span data-stu-id="8d50e-322">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="8d50e-323">Povolte na serveru gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="8d50e-323">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="8d50e-324">Další informace naleznete v tématu <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="8d50e-324">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="8d50e-325">Zaregistrujte služby gRPC pro obslužnou rutinu zpráv aplikace.</span><span class="sxs-lookup"><span data-stu-id="8d50e-325">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="8d50e-326">V následujícím příkladu se nakonfiguruje obslužná rutina autorizační zprávy aplikace, aby používala [ `GreeterClient` službu z kurzu gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) ( `Program.Main` ):</span><span class="sxs-lookup"><span data-stu-id="8d50e-326">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

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

<span data-ttu-id="8d50e-327">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="8d50e-327">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="8d50e-328">Umístěte `.proto` soubor do `Shared` projektu hostovaného Blazor řešení.</span><span class="sxs-lookup"><span data-stu-id="8d50e-328">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="8d50e-329">Komponenta v klientské aplikaci může volat gRPC pomocí klienta gRPC ( `Pages/Grpc.razor` ):</span><span class="sxs-lookup"><span data-stu-id="8d50e-329">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

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

<span data-ttu-id="8d50e-330">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="8d50e-330">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="8d50e-331">Chcete-li použít `Status.DebugException` vlastnost, použijte [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) verze 2.30.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="8d50e-331">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="8d50e-332">Další informace naleznete v tématu <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="8d50e-332">For more information, see <xref:grpc/browser>.</span></span>
