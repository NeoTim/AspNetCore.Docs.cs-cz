---
title: Zabezpečené aplikace Blazor ASP.NET Core Server
author: guardrex
description: Přečtěte si, jak Blazor zmírnit bezpečnostní hrozby pro serverové aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626025"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="a8846-103">Zabezpečené aplikace ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="a8846-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="a8846-104">Podle [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="a8846-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="a8846-105">Aplikace Blazor Server přijímají *stavový* model zpracování dat, kde server a klient udržují dlouhodobý vztah.</span><span class="sxs-lookup"><span data-stu-id="a8846-105">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="a8846-106">Trvalý stav je udržován [obvodem](xref:blazor/state-management), který může span připojení, které jsou také potenciálně životnost.</span><span class="sxs-lookup"><span data-stu-id="a8846-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="a8846-107">Když uživatel navštíví server Blazor server, server vytvoří okruh v paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="a8846-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="a8846-108">Obvod indikuje prohlížeči, jaký obsah se má vykreslit, a reaguje na události, například když uživatel vybere tlačítko v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a8846-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="a8846-109">K provedení těchto akcí obvod vyvolá funkce jazyka JavaScript v prohlížeči uživatele a metody .NET na serveru.</span><span class="sxs-lookup"><span data-stu-id="a8846-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="a8846-110">Tato obousměrná interakce založená na JavaScriptu se označuje jako [JavaScript interop (JS interop).](xref:blazor/call-javascript-from-dotnet)</span><span class="sxs-lookup"><span data-stu-id="a8846-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="a8846-111">Vzhledem k tomu, že js interop dochází přes Internet a klient používá vzdálený prohlížeč, Aplikace Blazor Server sdílejí většinu problémů se zabezpečením webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="a8846-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="a8846-112">Toto téma popisuje běžné hrozby pro aplikace Blazor Server a poskytuje pokyny ke zmírnění hrozeb zaměřené na aplikace orientované na Internet.</span><span class="sxs-lookup"><span data-stu-id="a8846-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="a8846-113">V omezených prostředích, například v podnikových sítích nebo intranetu, některé pokyny ke zmírnění rizika:</span><span class="sxs-lookup"><span data-stu-id="a8846-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="a8846-114">Neplatí v omezeném prostředí.</span><span class="sxs-lookup"><span data-stu-id="a8846-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="a8846-115">Nestojí za náklady na implementaci, protože bezpečnostní riziko je nízké v omezeném prostředí.</span><span class="sxs-lookup"><span data-stu-id="a8846-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="a8846-116">Šablona projektu Blazor Server</span><span class="sxs-lookup"><span data-stu-id="a8846-116">Blazor Server project template</span></span>

<span data-ttu-id="a8846-117">Šablonu projektu Blazor Server lze nakonfigurovat pro ověřování při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="a8846-117">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a8846-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8846-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a8846-119">Postupujte podle pokynů <xref:blazor/get-started> visual studio v článku k vytvoření nového projektu Blazor Server s mechanismem ověřování.</span><span class="sxs-lookup"><span data-stu-id="a8846-119">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="a8846-120">Po výběru šablony **aplikace Blazor Server** v **dialogovém** okně Vytvořit nový ASP.NET základní webové aplikace vyberte v části **Ověřování** **možnost Změnit** .</span><span class="sxs-lookup"><span data-stu-id="a8846-120">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="a8846-121">Otevře se dialogové okno, které nabízí stejnou sadu mechanismů ověřování, které jsou k dispozici pro jiné ASP.NET základní projekty:</span><span class="sxs-lookup"><span data-stu-id="a8846-121">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="a8846-122">**Bez ověřování**</span><span class="sxs-lookup"><span data-stu-id="a8846-122">**No Authentication**</span></span>
* <span data-ttu-id="a8846-123">**Jednotlivé uživatelské účty** &ndash; uživatelské účty uživatelské účty mohou být uloženy:</span><span class="sxs-lookup"><span data-stu-id="a8846-123">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="a8846-124">V rámci aplikace pomocí ASP.NET [systému identit core.](xref:security/authentication/identity)</span><span class="sxs-lookup"><span data-stu-id="a8846-124">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="a8846-125">S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="a8846-125">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="a8846-126">**Pracovní nebo školní účty**</span><span class="sxs-lookup"><span data-stu-id="a8846-126">**Work or School Accounts**</span></span>
* <span data-ttu-id="a8846-127">**Ověřování systému Windows**</span><span class="sxs-lookup"><span data-stu-id="a8846-127">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a8846-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a8846-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a8846-129">Postupujte podle pokynů kódu <xref:blazor/get-started> sady Visual Studio v článku a vytvořte nový projekt Blazor Server s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="a8846-129">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="a8846-130">Přípustné hodnoty ověřování`{AUTHENTICATION}`( ) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a8846-130">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="a8846-131">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="a8846-131">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="a8846-132">`{AUTHENTICATION}`Hodnotu</span><span class="sxs-lookup"><span data-stu-id="a8846-132">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="a8846-133">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="a8846-133">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="a8846-134">Jednotlivé</span><span class="sxs-lookup"><span data-stu-id="a8846-134">Individual</span></span><br><span data-ttu-id="a8846-135">Uživatelé uložení v aplikaci s ASP.NET Základní identita.</span><span class="sxs-lookup"><span data-stu-id="a8846-135">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="a8846-136">Jednotlivé</span><span class="sxs-lookup"><span data-stu-id="a8846-136">Individual</span></span><br><span data-ttu-id="a8846-137">Uživatelé uložená v [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="a8846-137">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="a8846-138">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="a8846-138">Work or School Accounts</span></span><br><span data-ttu-id="a8846-139">Organizační ověřování pro jednoho klienta.</span><span class="sxs-lookup"><span data-stu-id="a8846-139">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="a8846-140">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="a8846-140">Work or School Accounts</span></span><br><span data-ttu-id="a8846-141">Organizační ověřování pro více klientů.</span><span class="sxs-lookup"><span data-stu-id="a8846-141">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="a8846-142">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="a8846-142">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="a8846-143">Příkaz vytvoří složku s názvem s hodnotou poskytnutou `{APP NAME}` pro zástupný symbol a použije název složky jako název aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8846-143">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="a8846-144">Další informace naleznete v novém příkazu [dotnet](/dotnet/core/tools/dotnet-new) v průvodci jádrem rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="a8846-144">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a8846-145">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a8846-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a8846-146">Postupujte podle pokynů Visual <xref:blazor/get-started> Studio pro Mac v článku.</span><span class="sxs-lookup"><span data-stu-id="a8846-146">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="a8846-147">V části **Konfigurace nové aplikace Blazor Server** vyberte v rozevíracím přehledu **Ověřování** možnost Individuální **ověřování (v aplikaci).**</span><span class="sxs-lookup"><span data-stu-id="a8846-147">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="a8846-148">Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci s ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="a8846-148">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a8846-149">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="a8846-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="a8846-150">Postupujte podle pokynů rozhraní .NET Core CLI v <xref:blazor/get-started> článku a vytvořte nový projekt Serveru Blazor s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="a8846-150">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="a8846-151">Přípustné hodnoty ověřování`{AUTHENTICATION}`( ) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a8846-151">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="a8846-152">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="a8846-152">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="a8846-153">`{AUTHENTICATION}`Hodnotu</span><span class="sxs-lookup"><span data-stu-id="a8846-153">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="a8846-154">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="a8846-154">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="a8846-155">Jednotlivé</span><span class="sxs-lookup"><span data-stu-id="a8846-155">Individual</span></span><br><span data-ttu-id="a8846-156">Uživatelé uložení v aplikaci s ASP.NET Základní identita.</span><span class="sxs-lookup"><span data-stu-id="a8846-156">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="a8846-157">Jednotlivé</span><span class="sxs-lookup"><span data-stu-id="a8846-157">Individual</span></span><br><span data-ttu-id="a8846-158">Uživatelé uložená v [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="a8846-158">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="a8846-159">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="a8846-159">Work or School Accounts</span></span><br><span data-ttu-id="a8846-160">Organizační ověřování pro jednoho klienta.</span><span class="sxs-lookup"><span data-stu-id="a8846-160">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="a8846-161">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="a8846-161">Work or School Accounts</span></span><br><span data-ttu-id="a8846-162">Organizační ověřování pro více klientů.</span><span class="sxs-lookup"><span data-stu-id="a8846-162">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="a8846-163">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="a8846-163">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="a8846-164">Příkaz vytvoří složku s názvem s hodnotou poskytnutou `{APP NAME}` pro zástupný symbol a použije název složky jako název aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8846-164">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="a8846-165">Další informace naleznete v novém příkazu [dotnet](/dotnet/core/tools/dotnet-new) v průvodci jádrem rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="a8846-165">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="a8846-166">Předání tokenů do aplikace Blazor Server</span><span class="sxs-lookup"><span data-stu-id="a8846-166">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="a8846-167">Ověřte aplikaci Blazor Server stejně jako u běžné aplikace Razor Pages nebo MVC.</span><span class="sxs-lookup"><span data-stu-id="a8846-167">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="a8846-168">Zřízení a uložení tokenů do ověřovacího souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="a8846-168">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="a8846-169">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a8846-169">For example:</span></span>

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

<span data-ttu-id="a8846-170">Ukázkový kód, včetně `Startup.ConfigureServices` úplného příkladu, naleznete [v tématu Předávání tokenů do aplikace Blazor na straně serveru](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="a8846-170">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="a8846-171">Definujte třídu, která má být předávána v počátečním stavu aplikace pomocí tokenů pro přístup a aktualizaci:</span><span class="sxs-lookup"><span data-stu-id="a8846-171">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="a8846-172">Definujte službu poskytovatele tokenů **s oborem,** kterou lze použít v rámci aplikace Blazor k vyřešení tokenů z DI:</span><span class="sxs-lookup"><span data-stu-id="a8846-172">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="a8846-173">V `Startup.ConfigureServices`oblasti přidejte služby pro:</span><span class="sxs-lookup"><span data-stu-id="a8846-173">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="a8846-174">V souboru *_Host.cshtml* vytvořte `InitialApplicationState` a předajte jej jako parametr aplikaci:</span><span class="sxs-lookup"><span data-stu-id="a8846-174">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="a8846-175">V `App` komponentě (*App.razor*) vyřešte službu a inicializujte ji daty z parametru:</span><span class="sxs-lookup"><span data-stu-id="a8846-175">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="a8846-176">Ve službě, která vytváří požadavek na zabezpečené rozhraní API, vložte zprostředkovatele tokenu a načtěte token pro volání rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="a8846-176">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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

## <a name="resource-exhaustion"></a><span data-ttu-id="a8846-177">Vyčerpání zdrojů</span><span class="sxs-lookup"><span data-stu-id="a8846-177">Resource exhaustion</span></span>

<span data-ttu-id="a8846-178">Vyčerpání prostředků může dojít při interakci klienta se serverem a způsobí, že server spotřebovávají nadměrné prostředky.</span><span class="sxs-lookup"><span data-stu-id="a8846-178">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="a8846-179">Nadměrná spotřeba zdrojů ovlivňuje především:</span><span class="sxs-lookup"><span data-stu-id="a8846-179">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="a8846-180">Cpu</span><span class="sxs-lookup"><span data-stu-id="a8846-180">CPU</span></span>](#cpu)
* [<span data-ttu-id="a8846-181">Paměti</span><span class="sxs-lookup"><span data-stu-id="a8846-181">Memory</span></span>](#memory)
* [<span data-ttu-id="a8846-182">Připojení klientů</span><span class="sxs-lookup"><span data-stu-id="a8846-182">Client connections</span></span>](#client-connections)

<span data-ttu-id="a8846-183">Útoky typu DoS (Denial of Service) se obvykle snaží vyčerpat prostředky aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a8846-183">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="a8846-184">Vyčerpání prostředků však nemusí být nutně výsledkem útoku na systém.</span><span class="sxs-lookup"><span data-stu-id="a8846-184">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="a8846-185">Například omezené prostředky mohou být vyčerpány z důvodu vysoké poptávky uživatelů.</span><span class="sxs-lookup"><span data-stu-id="a8846-185">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="a8846-186">DoS je dále zahrnuta v části [útoky odmítnutí služby (DoS).](#denial-of-service-dos-attacks)</span><span class="sxs-lookup"><span data-stu-id="a8846-186">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="a8846-187">Prostředky mimo architekturu Blazor, jako jsou databáze a popisovače souborů (používané ke čtení a zápisu souborů), může také dojít k vyčerpání prostředků.</span><span class="sxs-lookup"><span data-stu-id="a8846-187">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="a8846-188">Další informace naleznete v tématu <xref:performance/performance-best-practices>.</span><span class="sxs-lookup"><span data-stu-id="a8846-188">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="a8846-189">Procesor</span><span class="sxs-lookup"><span data-stu-id="a8846-189">CPU</span></span>

<span data-ttu-id="a8846-190">K vyčerpání procesoru může dojít, když jeden nebo více klientů vynutí server k provádění intenzivní práce procesoru.</span><span class="sxs-lookup"><span data-stu-id="a8846-190">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="a8846-191">Zvažte například aplikaci Blazor Server, která vypočítá *číslo Fibonnacci*.</span><span class="sxs-lookup"><span data-stu-id="a8846-191">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="a8846-192">Fibonnacciho číslo je vyrobeno z Fibonnacciho sekvence, kde každé číslo v pořadí je součtem dvou předchozích čísel.</span><span class="sxs-lookup"><span data-stu-id="a8846-192">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="a8846-193">Množství práce potřebné k dosažení odpovědi závisí na délce sekvence a velikosti počáteční hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a8846-193">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="a8846-194">Pokud aplikace neomezuje požadavek klienta, výpočty náročné na procesor mohou dominovat času procesoru a snížit výkon jiných úkolů.</span><span class="sxs-lookup"><span data-stu-id="a8846-194">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="a8846-195">Nadměrná spotřeba prostředků je problém se zabezpečením, který má vliv na dostupnost.</span><span class="sxs-lookup"><span data-stu-id="a8846-195">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="a8846-196">Vyčerpání procesoru je problémem pro všechny veřejné aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8846-196">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="a8846-197">V běžných webových aplikacích se požadavky a připojení neposouvají jako pojistka, ale aplikace Blazor Server neposkytují stejná ochranná opatření.</span><span class="sxs-lookup"><span data-stu-id="a8846-197">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="a8846-198">Aplikace Blazor Server musí před provedením potenciálně náročné práce na procesoru obsahovat příslušné kontroly a limity.</span><span class="sxs-lookup"><span data-stu-id="a8846-198">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="a8846-199">Memory (Paměť)</span><span class="sxs-lookup"><span data-stu-id="a8846-199">Memory</span></span>

<span data-ttu-id="a8846-200">Vyčerpání paměti může dojít, když jeden nebo více klientů vynutit server spotřebovat velké množství paměti.</span><span class="sxs-lookup"><span data-stu-id="a8846-200">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="a8846-201">Zvažte například aplikaci na straně Blazor-server s komponentou, která přijímá a zobrazuje seznam položek.</span><span class="sxs-lookup"><span data-stu-id="a8846-201">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="a8846-202">Pokud aplikace Blazor neomezuje počet povolených položek nebo počet položek vykreslených zpět klientovi, může zpracování a vykreslování náročné na paměť v paměti serveru převládat do bodu, kdy dochází k výkonu serveru.</span><span class="sxs-lookup"><span data-stu-id="a8846-202">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="a8846-203">Server může dojít k chybě nebo zpomalit do bodu, který se zdá, že došlo k chybě.</span><span class="sxs-lookup"><span data-stu-id="a8846-203">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="a8846-204">Zvažte následující scénář pro udržování a zobrazení seznamu položek, které se připojují k možnému scénáři vyčerpání paměti na serveru:</span><span class="sxs-lookup"><span data-stu-id="a8846-204">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="a8846-205">Položky ve `List<MyItem>` vlastnosti nebo poli používají paměť serveru.</span><span class="sxs-lookup"><span data-stu-id="a8846-205">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="a8846-206">Pokud aplikace umožňuje, aby seznam položek rostl bez omezení, existuje riziko, že serveru dojde paměť.</span><span class="sxs-lookup"><span data-stu-id="a8846-206">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="a8846-207">Nedostatek paměti způsobí ukončení aktuální relace (selhání) a všechny souběžné relace v této instanci serveru obdrží výjimku z nedostatku paměti.</span><span class="sxs-lookup"><span data-stu-id="a8846-207">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="a8846-208">Chcete-li zabránit tomuto scénáři, aplikace musí použít datovou strukturu, která ukládá omezení položky pro souběžné uživatele.</span><span class="sxs-lookup"><span data-stu-id="a8846-208">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="a8846-209">Pokud schéma stránkování se nepoužívá pro vykreslování, server používá další paměť pro objekty, které nejsou viditelné v uživatelském uživatelském terminálu.</span><span class="sxs-lookup"><span data-stu-id="a8846-209">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="a8846-210">Bez omezení počtu položek může požadavky na paměť vyčerpat dostupnou paměť serveru.</span><span class="sxs-lookup"><span data-stu-id="a8846-210">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="a8846-211">Chcete-li tomuto scénáři zabránit, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="a8846-211">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="a8846-212">Při vykreslování používejte stránkované seznamy.</span><span class="sxs-lookup"><span data-stu-id="a8846-212">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="a8846-213">Zobrazí pouze prvních 100 až 1 000 položek a bude vyžadovat, aby uživatel zadá kritéria vyhledávání, aby vyhledal položky za zobrazené položky.</span><span class="sxs-lookup"><span data-stu-id="a8846-213">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="a8846-214">Pro pokročilejší scénář vykreslování implementujte seznamy nebo mřížky, které podporují *virtualizaci*.</span><span class="sxs-lookup"><span data-stu-id="a8846-214">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="a8846-215">Pomocí virtualizace seznamy vykreslují pouze podmnožinu položek, které jsou aktuálně viditelné pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="a8846-215">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="a8846-216">Když uživatel interaguje s posuvníkem v uživatelském rozhraní, komponenta vykreslí pouze ty položky potřebné pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a8846-216">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="a8846-217">Položky, které nejsou aktuálně požadovány pro zobrazení, mohou být uloženy ve sekundárním úložišti, což je ideální přístup.</span><span class="sxs-lookup"><span data-stu-id="a8846-217">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="a8846-218">Nezobrazené položky mohou být také uloženy v paměti, což je méně ideální.</span><span class="sxs-lookup"><span data-stu-id="a8846-218">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="a8846-219">Aplikace Blazor Server nabízejí podobný programovací model jako jiné architektury uživatelského rozhraní pro stavové aplikace, jako jsou WPF, Windows Forms nebo Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="a8846-219">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="a8846-220">Hlavní rozdíl je, že v několika rozhraní rozhraní paměti spotřebované aplikací patří klientovi a ovlivňuje pouze jednotlivé klienta.</span><span class="sxs-lookup"><span data-stu-id="a8846-220">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="a8846-221">Například aplikace Blazor WebAssembly běží výhradně na straně klienta a používá pouze prostředky klientské paměti.</span><span class="sxs-lookup"><span data-stu-id="a8846-221">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="a8846-222">Ve scénáři Blazor Server paměť spotřebovaná aplikací patří k serveru a je sdílena mezi klienty na instanci serveru.</span><span class="sxs-lookup"><span data-stu-id="a8846-222">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="a8846-223">Požadavky na paměť na straně serveru jsou zvažou pro všechny aplikace Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a8846-223">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="a8846-224">Většina webových aplikací je však bezstavová a paměť použitá při zpracování požadavku je uvolněna při vrácení odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a8846-224">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="a8846-225">Jako obecné doporučení nepovolujte klientům přidělit neomezené množství paměti jako v jakékoli jiné aplikaci na straně serveru, která zachová připojení klientů.</span><span class="sxs-lookup"><span data-stu-id="a8846-225">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="a8846-226">Paměť spotřebovaná aplikací Blazor Server přetrvává déle než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="a8846-226">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="a8846-227">Během vývoje profiler lze použít nebo trasování zachycené k posouzení požadavků paměti klientů.</span><span class="sxs-lookup"><span data-stu-id="a8846-227">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="a8846-228">Profiler nebo trasování nezachytí paměť přidělenou konkrétnímu klientovi.</span><span class="sxs-lookup"><span data-stu-id="a8846-228">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="a8846-229">Chcete-li zachytit využití paměti konkrétního klienta během vývoje, zachyťte výpis a zkontrolujte požadavek paměti všech objektů zakořeněných v okruhu uživatele.</span><span class="sxs-lookup"><span data-stu-id="a8846-229">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="a8846-230">Připojení klientů</span><span class="sxs-lookup"><span data-stu-id="a8846-230">Client connections</span></span>

<span data-ttu-id="a8846-231">K vyčerpání připojení může dojít, když jeden nebo více klientů otevře příliš mnoho souběžných připojení k serveru a zabrání ostatním klientům v navazování nových připojení.</span><span class="sxs-lookup"><span data-stu-id="a8846-231">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="a8846-232">Klienti Blazor uváže jedno připojení na relaci a ponechají připojení otevřené tak dlouho, dokud je otevřené okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a8846-232">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="a8846-233">Požadavky na server údržby všech připojení nejsou specifické pro aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="a8846-233">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="a8846-234">Vzhledem k trvalé povaze připojení a stavové povaze aplikací Blazor Server je vyčerpání připojení větším rizikem pro dostupnost aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8846-234">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="a8846-235">Ve výchozím nastavení není omezen počet připojení na uživatele aplikace Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a8846-235">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="a8846-236">Pokud aplikace vyžaduje omezení připojení, vezměte jeden nebo více z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="a8846-236">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="a8846-237">Vyžadovat ověření, což přirozeně omezuje možnost neoprávněných uživatelů připojit se k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a8846-237">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="a8846-238">Aby byl tento scénář efektivní, musí být uživatelům zabráněno v zřizování nových uživatelů podle vůle.</span><span class="sxs-lookup"><span data-stu-id="a8846-238">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="a8846-239">Omezte počet připojení na uživatele.</span><span class="sxs-lookup"><span data-stu-id="a8846-239">Limit the number of connections per user.</span></span> <span data-ttu-id="a8846-240">Omezení připojení lze provést pomocí následujících přístupů.</span><span class="sxs-lookup"><span data-stu-id="a8846-240">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="a8846-241">Péče o zabezpečení, aby oprávněným uživatelům přístup k aplikaci (například při stanovení limitu připojení na základě IP adresy klienta).</span><span class="sxs-lookup"><span data-stu-id="a8846-241">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="a8846-242">Na úrovni aplikace:</span><span class="sxs-lookup"><span data-stu-id="a8846-242">At the app level:</span></span>
    * <span data-ttu-id="a8846-243">Rozšiřitelnost směrování koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="a8846-243">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="a8846-244">Vyžadovat ověření pro připojení k aplikaci a sledování aktivních relací na uživatele.</span><span class="sxs-lookup"><span data-stu-id="a8846-244">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="a8846-245">Odmítnout nové relace po dosažení limitu.</span><span class="sxs-lookup"><span data-stu-id="a8846-245">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="a8846-246">Proxy WebSocket připojení k aplikaci pomocí proxy serveru, jako je například [služba Azure SignalR,](/azure/azure-signalr/signalr-overview) který multiplexu připojení z klientů do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8846-246">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="a8846-247">To poskytuje aplikaci s větší kapacitou připojení, než může vytvořit jeden klient, což brání klientovi v vyčerpání připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="a8846-247">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="a8846-248">Na úrovni serveru: Použijte proxy/bránu před aplikací.</span><span class="sxs-lookup"><span data-stu-id="a8846-248">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="a8846-249">[Azure Front Door](/azure/frontdoor/front-door-overview) například umožňuje definovat, spravovat a monitorovat globální směrování webového provozu do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8846-249">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="a8846-250">Útoky odmítnutí služby (DoS)</span><span class="sxs-lookup"><span data-stu-id="a8846-250">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="a8846-251">Útoky odmítnutí služby (DoS) zahrnují klienta, který způsobuje, že server vyčerpá jeden nebo více prostředků, které znepřístupňují aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a8846-251">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="a8846-252">Aplikace Blazor Server obsahují některé výchozí limity a spoléhají se na další limity ASP.NET core a signalr, aby se ochránily před útoky DoS:</span><span class="sxs-lookup"><span data-stu-id="a8846-252">Blazor Server apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| <span data-ttu-id="a8846-253">Limit aplikace Blazor Server</span><span class="sxs-lookup"><span data-stu-id="a8846-253">Blazor Server app limit</span></span>                            | <span data-ttu-id="a8846-254">Popis</span><span class="sxs-lookup"><span data-stu-id="a8846-254">Description</span></span> | <span data-ttu-id="a8846-255">Výchozí</span><span class="sxs-lookup"><span data-stu-id="a8846-255">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="a8846-256">Maximální počet odpojených obvodů, které daný server uchovává v paměti současně.</span><span class="sxs-lookup"><span data-stu-id="a8846-256">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="a8846-257">100</span><span class="sxs-lookup"><span data-stu-id="a8846-257">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="a8846-258">Maximální doba, po kterou je odpojený obvod před stržením v paměti.</span><span class="sxs-lookup"><span data-stu-id="a8846-258">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="a8846-259">3 minuty</span><span class="sxs-lookup"><span data-stu-id="a8846-259">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="a8846-260">Maximální doba, po kterou server čeká před vypršením asynchronního vyvolání funkce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a8846-260">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="a8846-261">1 minuta</span><span class="sxs-lookup"><span data-stu-id="a8846-261">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="a8846-262">Maximální počet nepotvrzených vykreslovacích dávek, které server uchovává v paměti na okruh v daném okamžiku pro podporu robustního opětovného připojení.</span><span class="sxs-lookup"><span data-stu-id="a8846-262">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="a8846-263">Po dosažení limitu server přestane vyrábět nové dávky vykreslení, dokud klient nepotvrdí jednu nebo více dávek.</span><span class="sxs-lookup"><span data-stu-id="a8846-263">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="a8846-264">10</span><span class="sxs-lookup"><span data-stu-id="a8846-264">10</span></span> |


| <span data-ttu-id="a8846-265">SignalR a ASP.NET core limit</span><span class="sxs-lookup"><span data-stu-id="a8846-265">SignalR and ASP.NET Core limit</span></span>             | <span data-ttu-id="a8846-266">Popis</span><span class="sxs-lookup"><span data-stu-id="a8846-266">Description</span></span> | <span data-ttu-id="a8846-267">Výchozí</span><span class="sxs-lookup"><span data-stu-id="a8846-267">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="a8846-268">Velikost zprávy pro jednotlivé zprávy.</span><span class="sxs-lookup"><span data-stu-id="a8846-268">Message size for an individual message.</span></span> | <span data-ttu-id="a8846-269">32 KB</span><span class="sxs-lookup"><span data-stu-id="a8846-269">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="a8846-270">Interakce s prohlížečem (klientem)</span><span class="sxs-lookup"><span data-stu-id="a8846-270">Interactions with the browser (client)</span></span>

<span data-ttu-id="a8846-271">Klient spolupracuje se serverem prostřednictvím js interop události odeslání a vykreslování dokončení.</span><span class="sxs-lookup"><span data-stu-id="a8846-271">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="a8846-272">JS interop komunikace jde oběma směry mezi JavaScript a .NET:</span><span class="sxs-lookup"><span data-stu-id="a8846-272">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="a8846-273">Události prohlížeče jsou odesílány z klienta na server asynchronním způsobem.</span><span class="sxs-lookup"><span data-stu-id="a8846-273">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="a8846-274">Server reaguje asynchronně rerendering uI podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="a8846-274">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="a8846-275">Funkce JavaScriptu vyvolané z rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="a8846-275">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="a8846-276">Pro volání z metod .NET do Jazyka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="a8846-276">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="a8846-277">Všechny vyvolání mají konfigurovatelný časový rámec, po kterém <xref:System.OperationCanceledException> se nezdaří, vrácení a volajícímu.</span><span class="sxs-lookup"><span data-stu-id="a8846-277">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="a8846-278">Pro volání (`CircuitOptions.JSInteropDefaultCallTimeout`) je výchozí časový limit jedné minuty.</span><span class="sxs-lookup"><span data-stu-id="a8846-278">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="a8846-279">Chcete-li tento <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>limit nakonfigurovat, přečtěte si téma .</span><span class="sxs-lookup"><span data-stu-id="a8846-279">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="a8846-280">Token zrušení lze poskytnout pro řízení zrušení na základě volání.</span><span class="sxs-lookup"><span data-stu-id="a8846-280">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="a8846-281">Spoléhat se na výchozí časový limit volání, pokud je to možné a časově vázané jakékoli volání klientovi, pokud je k dispozici token zrušení.</span><span class="sxs-lookup"><span data-stu-id="a8846-281">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="a8846-282">Výsledek volání javascriptu nelze důvěřovat.</span><span class="sxs-lookup"><span data-stu-id="a8846-282">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="a8846-283">Klient Blazor aplikace spuštěný v prohlížeči hledá funkci JavaScriptu, kterou chcete vyvolat.</span><span class="sxs-lookup"><span data-stu-id="a8846-283">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="a8846-284">Funkce je vyvolána a je vytvořen výsledek nebo chyba.</span><span class="sxs-lookup"><span data-stu-id="a8846-284">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="a8846-285">Klient se zlými úmysly se může pokusit o:</span><span class="sxs-lookup"><span data-stu-id="a8846-285">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="a8846-286">Způsobit problém v aplikaci vrácením chyby z funkce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a8846-286">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="a8846-287">Vyvolat nežádoucí chování na serveru vrácením neočekávaný výsledek z funkce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a8846-287">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="a8846-288">Postupujte podle následujících opatření, abyste se ochránili před předchozími scénáři:</span><span class="sxs-lookup"><span data-stu-id="a8846-288">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="a8846-289">Zalomit js interop volání v rámci [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) příkazy k účtu chyby, které mohou nastat během vyvolání.</span><span class="sxs-lookup"><span data-stu-id="a8846-289">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="a8846-290">Další informace naleznete v tématu <xref:blazor/handle-errors#javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="a8846-290">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="a8846-291">Před zahájením jakékoli akce ověřte data vrácená z vyvolání interop js, včetně chybových zpráv.</span><span class="sxs-lookup"><span data-stu-id="a8846-291">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="a8846-292">Metody .NET vyvolané z prohlížeče</span><span class="sxs-lookup"><span data-stu-id="a8846-292">.NET methods invoked from the browser</span></span>

<span data-ttu-id="a8846-293">Nedůvěřujte volání z JavaScriptu do metod .NET.</span><span class="sxs-lookup"><span data-stu-id="a8846-293">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="a8846-294">Pokud je metoda .NET vystavena javascriptu, zvažte, jak je metoda .NET vyvolána:</span><span class="sxs-lookup"><span data-stu-id="a8846-294">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="a8846-295">Zacházejte s libovolnou metodou .NET vystavenou JavaScriptu stejně jako s veřejným koncovým bodem aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8846-295">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="a8846-296">Ověřte vstup.</span><span class="sxs-lookup"><span data-stu-id="a8846-296">Validate input.</span></span>
    * <span data-ttu-id="a8846-297">Ujistěte se, že hodnoty jsou v rámci očekávaných rozsahů.</span><span class="sxs-lookup"><span data-stu-id="a8846-297">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="a8846-298">Ujistěte se, že uživatel má oprávnění k provedení požadované akce.</span><span class="sxs-lookup"><span data-stu-id="a8846-298">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="a8846-299">Nepřidělujte nadměrné množství zdrojů jako součást vyvolání metody .NET.</span><span class="sxs-lookup"><span data-stu-id="a8846-299">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="a8846-300">Můžete například provádět kontroly a uvádět omezení pro využití procesoru a paměti.</span><span class="sxs-lookup"><span data-stu-id="a8846-300">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="a8846-301">Vezměte v úvahu, že statické metody a metody instance mohou být vystaveny klientům JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="a8846-301">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="a8846-302">Vyhněte se sdílení stavu mezi relacemi, pokud návrh volá pro sdílení stavu s příslušnými omezeními.</span><span class="sxs-lookup"><span data-stu-id="a8846-302">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="a8846-303">Pro metody vystavené prostřednictvím `DotNetReference` objektů, které jsou původně vytvořeny prostřednictvím vkládání závislostí (DI), objekty by měly být registrovány jako objekty s vymezeným oborem.</span><span class="sxs-lookup"><span data-stu-id="a8846-303">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="a8846-304">To platí pro všechny služby Blazor DI, které aplikace Server používá.</span><span class="sxs-lookup"><span data-stu-id="a8846-304">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="a8846-305">U statických metod se vyhněte vytváření stavu, který nelze určit na klienta, pokud aplikace explicitně nesdílí návrh stavu mezi všemi uživateli na instanci serveru.</span><span class="sxs-lookup"><span data-stu-id="a8846-305">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="a8846-306">Vyhněte se předávání dat dodaných uživatelem v parametrech voláním JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="a8846-306">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="a8846-307">Pokud je přenos dat v parametrech naprosto vyžadován, ujistěte se, že kód JavaScript zpracovává předávání dat bez zavedení chyb zabezpečení [skriptování napříč weby (XSS).](#cross-site-scripting-xss)</span><span class="sxs-lookup"><span data-stu-id="a8846-307">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="a8846-308">Například nezapisujte uživatelem zadaná data do modelu DOCUMENT `innerHTML` Object Model (DOM) nastavením vlastnosti prvku.</span><span class="sxs-lookup"><span data-stu-id="a8846-308">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="a8846-309">Zvažte použití zásad zabezpečení obsahu `eval` [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) zakázat a další nebezpečné javascriptové primitivy.</span><span class="sxs-lookup"><span data-stu-id="a8846-309">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="a8846-310">Vyhněte se implementaci vlastní odesílání vyvolání .NET nad implementací odesílání rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a8846-310">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="a8846-311">Vystavení metod .NET prohlížeči je pokročilý scénář, který Blazor se nedoporučuje pro obecný vývoj.</span><span class="sxs-lookup"><span data-stu-id="a8846-311">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="a8846-312">Události</span><span class="sxs-lookup"><span data-stu-id="a8846-312">Events</span></span>

<span data-ttu-id="a8846-313">Události poskytují vstupní bod Blazor do aplikace Server.</span><span class="sxs-lookup"><span data-stu-id="a8846-313">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="a8846-314">Stejná pravidla pro zabezpečení koncových bodů ve Blazor webových aplikacích platí pro zpracování událostí v serverových aplikacích.</span><span class="sxs-lookup"><span data-stu-id="a8846-314">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="a8846-315">Klient se zlými úmysly může odesílat všechna data, která chce odeslat jako datovou část události.</span><span class="sxs-lookup"><span data-stu-id="a8846-315">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="a8846-316">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a8846-316">For example:</span></span>

* <span data-ttu-id="a8846-317">Událost změny pro `<select>` může odeslat hodnotu, která není v rámci možností, které aplikace prezentovány klientovi.</span><span class="sxs-lookup"><span data-stu-id="a8846-317">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="a8846-318">Může `<input>` odeslat všechna textová data na server, obcházet ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a8846-318">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="a8846-319">Aplikace musí ověřit data pro každou událost, kterou aplikace zpracovává.</span><span class="sxs-lookup"><span data-stu-id="a8846-319">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="a8846-320">Blazor [Součásti rámcových formulářů](xref:blazor/forms-validation) provádějí základní ověření.</span><span class="sxs-lookup"><span data-stu-id="a8846-320">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="a8846-321">Pokud aplikace používá vlastní komponenty formulářů, musí být vlastní kód zapsán k ověření dat událostí podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="a8846-321">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

Blazor<span data-ttu-id="a8846-322">Události serveru jsou asynchronní, takže více událostí může být odesláno na server dříve, než aplikace bude mít čas reagovat vytvořením nového vykreslení.</span><span class="sxs-lookup"><span data-stu-id="a8846-322"> Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="a8846-323">To má některé bezpečnostní důsledky, aby zvážila.</span><span class="sxs-lookup"><span data-stu-id="a8846-323">This has some security implications to consider.</span></span> <span data-ttu-id="a8846-324">Omezení akce klienta v aplikaci musí být provedeno uvnitř obslužné rutiny událostí a není závislé na aktuální matné zobrazení stavu.</span><span class="sxs-lookup"><span data-stu-id="a8846-324">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="a8846-325">Zvažte součást čítače, která by měla umožnit uživateli inkresovat čítač maximálně třikrát.</span><span class="sxs-lookup"><span data-stu-id="a8846-325">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="a8846-326">Tlačítko pro zvýšení čítače je podmíněně `count`založeno na hodnotě :</span><span class="sxs-lookup"><span data-stu-id="a8846-326">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="a8846-327">Klient může odeslat jednu nebo více událostí přírůstku před rozhraní ms vytvoří nové vykreslení této součásti.</span><span class="sxs-lookup"><span data-stu-id="a8846-327">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="a8846-328">Výsledkem je, `count` že může být zvýšil *více než třikrát* uživatelem, protože tlačítko není odebrána uživatelského rozhraní dostatečně rychle.</span><span class="sxs-lookup"><span data-stu-id="a8846-328">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="a8846-329">Správný způsob, jak dosáhnout `count` limitu tří přírůstků, je uveden v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a8846-329">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="a8846-330">Přidáním `if (count < 3) { ... }` kontroly uvnitř obslužné `count` rutiny je rozhodnutí o zvýšení založeno na aktuálním stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8846-330">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="a8846-331">Rozhodnutí není založeno na stavu ui, jak tomu bylo v předchozím příkladu, které mohou být dočasně zastaralé.</span><span class="sxs-lookup"><span data-stu-id="a8846-331">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="a8846-332">Ochrana před více násobnými odesláními</span><span class="sxs-lookup"><span data-stu-id="a8846-332">Guard against multiple dispatches</span></span>

<span data-ttu-id="a8846-333">Pokud zpětné volání události vyvolá dlouho běžící operaci asynchronně, jako je například načítání dat z externí služby nebo databáze, zvažte použití guard.</span><span class="sxs-lookup"><span data-stu-id="a8846-333">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="a8846-334">Ochranka může zabránit uživateli ve frontě do více operací, zatímco operace probíhá s vizuální zpětnou vazbu.</span><span class="sxs-lookup"><span data-stu-id="a8846-334">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="a8846-335">Následující kód komponenty `true` `GetForecastAsync` se nastaví `isLoading` na zatímco získá data ze serveru.</span><span class="sxs-lookup"><span data-stu-id="a8846-335">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="a8846-336">Zatímco `isLoading` `true`je , tlačítko je zakázáno v ui:</span><span class="sxs-lookup"><span data-stu-id="a8846-336">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="a8846-337">Vzor guard demonstrovaný v předchozím příkladu funguje, pokud je operace na `async` - `await` pozadí prováděna asynchronně se vzorem.</span><span class="sxs-lookup"><span data-stu-id="a8846-337">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="a8846-338">Zrušit předčasně a vyhnout se použití po likvidaci</span><span class="sxs-lookup"><span data-stu-id="a8846-338">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="a8846-339">Kromě použití guard, jak je popsáno v guard proti více <xref:System.Threading.CancellationToken> [odeslání](#guard-against-multiple-dispatches) části, zvažte použití zrušit dlouhotrvající operace při vyřazení komponenty.</span><span class="sxs-lookup"><span data-stu-id="a8846-339">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="a8846-340">Tento přístup má další výhodu v tom, že se v součástech vyhýbá použití *po odstranění:*</span><span class="sxs-lookup"><span data-stu-id="a8846-340">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="a8846-341">Vyhněte se událostem, které vytvářejí velké množství dat</span><span class="sxs-lookup"><span data-stu-id="a8846-341">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="a8846-342">Některé události dom, `oninput` `onscroll`například nebo , může produkovat velké množství dat.</span><span class="sxs-lookup"><span data-stu-id="a8846-342">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="a8846-343">Nepoužívejte tyto Blazor události v serverových aplikacích.</span><span class="sxs-lookup"><span data-stu-id="a8846-343">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="a8846-344">Další bezpečnostní pokyny</span><span class="sxs-lookup"><span data-stu-id="a8846-344">Additional security guidance</span></span>

<span data-ttu-id="a8846-345">Pokyny pro zabezpečení aplikací ASP.NET Blazor Core se vztahují na serverové aplikace a jsou popsány v následujících částech:</span><span class="sxs-lookup"><span data-stu-id="a8846-345">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="a8846-346">Protokolování a citlivá data</span><span class="sxs-lookup"><span data-stu-id="a8846-346">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="a8846-347">Ochrana informací při přenosu pomocí protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="a8846-347">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="a8846-348">[Skriptování napříč weby (XSS)](#cross-site-scripting-xss)</span><span class="sxs-lookup"><span data-stu-id="a8846-348">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="a8846-349">Ochrana mezi původem</span><span class="sxs-lookup"><span data-stu-id="a8846-349">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="a8846-350">Kliknutí -jacking</span><span class="sxs-lookup"><span data-stu-id="a8846-350">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="a8846-351">Otevřená přesměrování</span><span class="sxs-lookup"><span data-stu-id="a8846-351">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="a8846-352">Protokolování a citlivá data</span><span class="sxs-lookup"><span data-stu-id="a8846-352">Logging and sensitive data</span></span>

<span data-ttu-id="a8846-353">Interakce mezi klientem a serverem jsou zaznamenány v protokolech <xref:Microsoft.Extensions.Logging.ILogger> serveru s instancemi.</span><span class="sxs-lookup"><span data-stu-id="a8846-353">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> Blazor<span data-ttu-id="a8846-354">zabraňuje protokolování citlivých informací, jako jsou skutečné události nebo vstupy a výstupy JS interop.</span><span class="sxs-lookup"><span data-stu-id="a8846-354"> avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="a8846-355">Dojde-li k chybě na serveru, rozhraní upozorní klienta a strhává relace.</span><span class="sxs-lookup"><span data-stu-id="a8846-355">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="a8846-356">Ve výchozím nastavení se klientovi zobrazí obecná chybová zpráva, která se zobrazí v vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a8846-356">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="a8846-357">Chyba na straně klienta neobsahuje zásobník volání a neposkytuje podrobnosti o příčině chyby, ale protokoly serveru tyto informace obsahují.</span><span class="sxs-lookup"><span data-stu-id="a8846-357">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="a8846-358">Pro účely vývoje citlivé informace o chybách mohou být zpřístupněny klientovi povolením podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="a8846-358">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="a8846-359">Povolit podrobné chyby s:</span><span class="sxs-lookup"><span data-stu-id="a8846-359">Enable detailed errors with:</span></span>

* <span data-ttu-id="a8846-360">`CircuitOptions.DetailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="a8846-360">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="a8846-361">`DetailedErrors`konfiguračního klíče.</span><span class="sxs-lookup"><span data-stu-id="a8846-361">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="a8846-362">Například nastavte `ASPNETCORE_DETAILEDERRORS` proměnnou prostředí na `true`hodnotu .</span><span class="sxs-lookup"><span data-stu-id="a8846-362">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="a8846-363">Vystavení informací o chybách klientům v Internetu představuje bezpečnostní riziko, kterému je třeba se vždy vyhnout.</span><span class="sxs-lookup"><span data-stu-id="a8846-363">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="a8846-364">Ochrana informací při přenosu pomocí protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="a8846-364">Protect information in transit with HTTPS</span></span>

Blazor<span data-ttu-id="a8846-365">Server SignalR používá pro komunikaci mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="a8846-365"> Server uses SignalR for communication between the client and the server.</span></span> Blazor<span data-ttu-id="a8846-366">Server obvykle používá SignalR přenos, který vyjednává, což je obvykle WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a8846-366"> Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

Blazor<span data-ttu-id="a8846-367">Server nezajišťuje integritu a důvěrnost dat odeslaných mezi serverem a klientem.</span><span class="sxs-lookup"><span data-stu-id="a8846-367"> Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="a8846-368">Vždy používejte protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a8846-368">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="a8846-369">Skriptování mezi weby (XSS)</span><span class="sxs-lookup"><span data-stu-id="a8846-369">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="a8846-370">Skriptování mezi servery (XSS) umožňuje neautorizované straně spustit libovolnou logiku v kontextu prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a8846-370">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="a8846-371">Ohrožená aplikace může potenciálně spustit libovolný kód na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a8846-371">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="a8846-372">Tato chyba zabezpečení by mohla být použita k provedení řady škodlivých akcí proti serveru:</span><span class="sxs-lookup"><span data-stu-id="a8846-372">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="a8846-373">Odešlete falešné/neplatné události na server.</span><span class="sxs-lookup"><span data-stu-id="a8846-373">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="a8846-374">Odeslání selhání nebo neplatné dokončení vykreslení.</span><span class="sxs-lookup"><span data-stu-id="a8846-374">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="a8846-375">Vyhněte se odesílání dokončení vykreslení.</span><span class="sxs-lookup"><span data-stu-id="a8846-375">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="a8846-376">Odeslání interop volání z JavaScriptu do .NET.</span><span class="sxs-lookup"><span data-stu-id="a8846-376">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="a8846-377">Upravte odpověď interop volání z .NET do JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="a8846-377">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="a8846-378">Vyhněte se odesílání .NET do výsledků js interop.</span><span class="sxs-lookup"><span data-stu-id="a8846-378">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="a8846-379">Server Blazor framework podniká kroky k ochraně před některými z předchozích hrozeb:</span><span class="sxs-lookup"><span data-stu-id="a8846-379">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="a8846-380">Zastaví vytváření nových aktualizací ui, pokud klient není potvrzení vykreslovací dávky.</span><span class="sxs-lookup"><span data-stu-id="a8846-380">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="a8846-381">Nakonfigurován `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`o .</span><span class="sxs-lookup"><span data-stu-id="a8846-381">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="a8846-382">Zaosí všechny volání .NET na JavaScript po jedné minutě bez přijetí odpovědi od klienta.</span><span class="sxs-lookup"><span data-stu-id="a8846-382">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="a8846-383">Nakonfigurován `CircuitOptions.JSInteropDefaultCallTimeout`o .</span><span class="sxs-lookup"><span data-stu-id="a8846-383">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="a8846-384">Provádí základní ověření na všech vstupech pocházejících z prohlížeče během JS interop:</span><span class="sxs-lookup"><span data-stu-id="a8846-384">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="a8846-385">Odkazy .NET jsou platné a typu očekávaného metodou .NET.</span><span class="sxs-lookup"><span data-stu-id="a8846-385">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="a8846-386">Data nejsou poškozená.</span><span class="sxs-lookup"><span data-stu-id="a8846-386">The data isn't malformed.</span></span>
  * <span data-ttu-id="a8846-387">Správný počet argumentů pro metodu jsou přítomny v datové části.</span><span class="sxs-lookup"><span data-stu-id="a8846-387">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="a8846-388">Argumenty nebo výsledek lze rekonstruovat správně před vyvoláním metody.</span><span class="sxs-lookup"><span data-stu-id="a8846-388">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="a8846-389">Provádí základní ověření ve všech vstupech pocházejících z prohlížeče z odeslaných událostí:</span><span class="sxs-lookup"><span data-stu-id="a8846-389">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="a8846-390">Událost má platný typ.</span><span class="sxs-lookup"><span data-stu-id="a8846-390">The event has a valid type.</span></span>
  * <span data-ttu-id="a8846-391">Data pro událost lze rekonstruovat.</span><span class="sxs-lookup"><span data-stu-id="a8846-391">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="a8846-392">K události je přidružena obslužná rutina události.</span><span class="sxs-lookup"><span data-stu-id="a8846-392">There's an event handler associated with the event.</span></span>

<span data-ttu-id="a8846-393">Kromě záruk, které rámec implementuje, musí být aplikace kódována vývojářem, aby byla chráněna před hrozbami a přijala vhodná opatření:</span><span class="sxs-lookup"><span data-stu-id="a8846-393">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="a8846-394">Při zpracování událostí vždy ověřte data.</span><span class="sxs-lookup"><span data-stu-id="a8846-394">Always validate data when handling events.</span></span>
* <span data-ttu-id="a8846-395">Přijmout vhodná opatření po obdržení neplatných údajů:</span><span class="sxs-lookup"><span data-stu-id="a8846-395">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="a8846-396">Ignorujte data a vraťte se.</span><span class="sxs-lookup"><span data-stu-id="a8846-396">Ignore the data and return.</span></span> <span data-ttu-id="a8846-397">To umožňuje aplikaci pokračovat ve zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="a8846-397">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="a8846-398">Pokud aplikace zjistí, že vstup je nelegitimní a nelze je vyrobit legitimním klientem, vyvoláte výjimku.</span><span class="sxs-lookup"><span data-stu-id="a8846-398">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="a8846-399">Vyvolání výjimky strhává okruh a ukončí relaci.</span><span class="sxs-lookup"><span data-stu-id="a8846-399">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="a8846-400">Nedůvěřujte chybové zprávě poskytované dokončením dávky vykreslení zahrnuté v protokolech.</span><span class="sxs-lookup"><span data-stu-id="a8846-400">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="a8846-401">Chyba je poskytována klientem a nelze obecně důvěřovat, protože klient může být ohrožena.</span><span class="sxs-lookup"><span data-stu-id="a8846-401">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="a8846-402">Nedůvěřujte vstupu na js interop volání v obou směrech mezi JavaScript a .NET metody.</span><span class="sxs-lookup"><span data-stu-id="a8846-402">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="a8846-403">Aplikace je zodpovědná za ověření, že obsah argumentů a výsledků jsou platné, i v případě, že argumenty nebo výsledky jsou správně rekonstruovat.</span><span class="sxs-lookup"><span data-stu-id="a8846-403">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="a8846-404">Aby existovala chyba zabezpečení XSS, musí aplikace na vykreslené stránce začlenit vstup uživatele.</span><span class="sxs-lookup"><span data-stu-id="a8846-404">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> Blazor<span data-ttu-id="a8846-405">Součásti serveru provést krok kompilace, kde je značka v souboru *.razor* transformována do procedurální logiky Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="a8846-405"> Server components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="a8846-406">Za běhu logika Jazyka C# vytvoří *strom vykreslení* popisující prvky, text a podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="a8846-406">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="a8846-407">To se aplikuje na dom prohlížeče prostřednictvím sekvence javascriptových instrukcí (nebo je serializován do HTML v případě prerendering):</span><span class="sxs-lookup"><span data-stu-id="a8846-407">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="a8846-408">Uživatelský vstup vykreslený pomocí normální syntaxe Razor (například) neodhaluje chybu zabezpečení XSS, `@someStringValue`protože syntaxe Razor je přidána do DOM pomocí příkazů, které mohou psát pouze text.</span><span class="sxs-lookup"><span data-stu-id="a8846-408">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="a8846-409">I v případě, že hodnota obsahuje značky HTML, hodnota se zobrazí jako statický text.</span><span class="sxs-lookup"><span data-stu-id="a8846-409">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="a8846-410">Při předběžném vykreslování je výstup kódován html, který také zobrazuje obsah jako statický text.</span><span class="sxs-lookup"><span data-stu-id="a8846-410">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="a8846-411">Značky skriptů nejsou povoleny a neměly by být zahrnuty do stromu vykreslení komponent aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8846-411">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="a8846-412">Pokud je značka skriptu zahrnuta do značky komponenty, je generována chyba v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="a8846-412">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="a8846-413">Autoři komponent mohou vytvářet komponenty v C# bez použití Razor.</span><span class="sxs-lookup"><span data-stu-id="a8846-413">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="a8846-414">Autor komponenty je zodpovědný za použití správných api při vyzařování výstupu.</span><span class="sxs-lookup"><span data-stu-id="a8846-414">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="a8846-415">Například použití `builder.AddContent(0, someUserSuppliedString)` a *ne* `builder.AddMarkupContent(0, someUserSuppliedString)`, protože posledně uvedené by mohlo vytvořit chybu zabezpečení XSS.</span><span class="sxs-lookup"><span data-stu-id="a8846-415">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="a8846-416">Jako součást ochrany proti útokům XSS zvažte implementaci zmírnění xss, jako je [například zásady zabezpečení obsahu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span><span class="sxs-lookup"><span data-stu-id="a8846-416">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="a8846-417">Další informace naleznete v tématu <xref:security/cross-site-scripting>.</span><span class="sxs-lookup"><span data-stu-id="a8846-417">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="a8846-418">Ochrana mezi původem</span><span class="sxs-lookup"><span data-stu-id="a8846-418">Cross-origin protection</span></span>

<span data-ttu-id="a8846-419">Útoky napříč původem zahrnují klienta z jiného původu, který provádí akci proti serveru.</span><span class="sxs-lookup"><span data-stu-id="a8846-419">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="a8846-420">Škodlivá akce je obvykle požadavek GET nebo formulář POST (Cross-Site Request Forgery, CSRF), ale otevření škodlivého WebSocket je také možné.</span><span class="sxs-lookup"><span data-stu-id="a8846-420">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> Blazor<span data-ttu-id="a8846-421">Serverové aplikace nabízejí [stejné záruky SignalR jako všechny ostatní aplikace používající nabídku centrálního protokolu](xref:signalr/security):</span><span class="sxs-lookup"><span data-stu-id="a8846-421"> Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* Blazor<span data-ttu-id="a8846-422">Serverové aplikace lze přistupovat cross-origin, pokud další opatření, aby se zabránilo.</span><span class="sxs-lookup"><span data-stu-id="a8846-422"> Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="a8846-423">Chcete-li zakázat přístup mezi zdroji, zakažte cors v koncovém bodě `DisableCorsAttribute` přidáním middlewaru CORS do kanálu a přidáním metadat Blazor koncového bodu nebo omezte sadu povolených počátků [konfigurací SignalR pro sdílení prostředků mezi zdroji](xref:signalr/security#cross-origin-resource-sharing).</span><span class="sxs-lookup"><span data-stu-id="a8846-423">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="a8846-424">Pokud je povolena CORS, další kroky mohou být vyžadovány k ochraně aplikace v závislosti na konfiguraci CORS.</span><span class="sxs-lookup"><span data-stu-id="a8846-424">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="a8846-425">Pokud je cors globálně povolena, může Blazor být cors `DisableCorsAttribute` zakázán pro server rozbočovač `hub.MapBlazorHub()`přidáním metadat do metadat koncového bodu po volání .</span><span class="sxs-lookup"><span data-stu-id="a8846-425">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="a8846-426">Další informace naleznete v tématu <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="a8846-426">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="a8846-427">Kliknutí -jacking</span><span class="sxs-lookup"><span data-stu-id="a8846-427">Click-jacking</span></span>

<span data-ttu-id="a8846-428">Click-jacking zahrnuje vykreslování webu jako `<iframe>` vnitřního webu z jiného původu, aby se uživatel i obelstil, aby provedl akce na webu, který je pod útokem.</span><span class="sxs-lookup"><span data-stu-id="a8846-428">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="a8846-429">Chcete-li aplikaci chránit `<iframe>`před vykreslováním uvnitř `X-Frame-Options` aplikace , použijte [zásady zabezpečení obsahu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) a záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a8846-429">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="a8846-430">Další informace naleznete v [tématu MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span><span class="sxs-lookup"><span data-stu-id="a8846-430">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="a8846-431">Otevřená přesměrování</span><span class="sxs-lookup"><span data-stu-id="a8846-431">Open redirects</span></span>

<span data-ttu-id="a8846-432">Při Blazor spuštění relace aplikace Server server server provede základní ověření adres URL odeslaných jako součást spuštění relace.</span><span class="sxs-lookup"><span data-stu-id="a8846-432">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="a8846-433">Rámec zkontroluje, zda je základní adresa URL nadřazenou aktuální adresou URL před vytvořením okruhu.</span><span class="sxs-lookup"><span data-stu-id="a8846-433">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="a8846-434">Rámec neprovádí žádné další kontroly.</span><span class="sxs-lookup"><span data-stu-id="a8846-434">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="a8846-435">Když uživatel vybere odkaz na straně klienta, adresa URL pro odkaz je odeslána na server, který určuje, jaké akce má být.</span><span class="sxs-lookup"><span data-stu-id="a8846-435">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="a8846-436">Aplikace může například provést navigaci na straně klienta nebo označit prohlížeči, aby přešel do nového umístění.</span><span class="sxs-lookup"><span data-stu-id="a8846-436">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="a8846-437">Součásti mohou také programově aktivovat navigační `NavigationManager`požadavky pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="a8846-437">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="a8846-438">V takových případech může aplikace provést navigaci na straně klienta nebo označit prohlížeči, aby přešel do nového umístění.</span><span class="sxs-lookup"><span data-stu-id="a8846-438">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="a8846-439">Součásti musí:</span><span class="sxs-lookup"><span data-stu-id="a8846-439">Components must:</span></span>

* <span data-ttu-id="a8846-440">Nepoužívejte vstup uživatele jako součást argumentů navigačního volání.</span><span class="sxs-lookup"><span data-stu-id="a8846-440">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="a8846-441">Ověřte argumenty a ujistěte se, že cíl je povolen aplikací.</span><span class="sxs-lookup"><span data-stu-id="a8846-441">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="a8846-442">V opačném případě může uživatel se zlými úmysly vynutit, aby prohlížeč přešel na web řízený útočníkem.</span><span class="sxs-lookup"><span data-stu-id="a8846-442">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="a8846-443">V tomto scénáři útočník triky aplikace do použití některé hod `NavigationManager.Navigate` uživatele jako součást vyvolání metody.</span><span class="sxs-lookup"><span data-stu-id="a8846-443">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="a8846-444">Tato rada platí také při vykreslování odkazů jako součást aplikace:</span><span class="sxs-lookup"><span data-stu-id="a8846-444">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="a8846-445">Pokud je to možné, použijte relativní odkazy.</span><span class="sxs-lookup"><span data-stu-id="a8846-445">If possible, use relative links.</span></span>
* <span data-ttu-id="a8846-446">Před zahrnutím na stránku ověřte, zda jsou absolutní cíle odkazů platné.</span><span class="sxs-lookup"><span data-stu-id="a8846-446">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="a8846-447">Další informace naleznete v tématu <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="a8846-447">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="a8846-448">Ověřování a autorizace</span><span class="sxs-lookup"><span data-stu-id="a8846-448">Authentication and authorization</span></span>

<span data-ttu-id="a8846-449">Pokyny k ověřování a <xref:security/blazor/index>autorizaci naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="a8846-449">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="a8846-450">Kontrolní seznam zabezpečení</span><span class="sxs-lookup"><span data-stu-id="a8846-450">Security checklist</span></span>

<span data-ttu-id="a8846-451">Následující seznam aspektů zabezpečení není vyčerpávající:</span><span class="sxs-lookup"><span data-stu-id="a8846-451">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="a8846-452">Ověřte argumenty z událostí.</span><span class="sxs-lookup"><span data-stu-id="a8846-452">Validate arguments from events.</span></span>
* <span data-ttu-id="a8846-453">Ověřte vstupy a výsledky z js interop volání.</span><span class="sxs-lookup"><span data-stu-id="a8846-453">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="a8846-454">Vyhněte se použití (nebo ověření předem) vstup uživatele pro volání .NET na JS interop.</span><span class="sxs-lookup"><span data-stu-id="a8846-454">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="a8846-455">Zabrání klientovi v přidělení neomezené množství paměti.</span><span class="sxs-lookup"><span data-stu-id="a8846-455">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="a8846-456">Data v rámci komponenty.</span><span class="sxs-lookup"><span data-stu-id="a8846-456">Data within the component.</span></span>
  * <span data-ttu-id="a8846-457">`DotNetObject`odkazy vrácené klientovi.</span><span class="sxs-lookup"><span data-stu-id="a8846-457">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="a8846-458">Chraňte se před několika zásilkami.</span><span class="sxs-lookup"><span data-stu-id="a8846-458">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="a8846-459">Zrušte dlouhotrvající operace při vyřazení součásti.</span><span class="sxs-lookup"><span data-stu-id="a8846-459">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="a8846-460">Vyhněte se událostem, které vytvářejí velké množství dat.</span><span class="sxs-lookup"><span data-stu-id="a8846-460">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="a8846-461">Pokud je to nevyhnutelné, `NavigationManager.Navigate` vyhněte se použití uživatelského vstupu jako součásti volání a ověřte vstup uživatele pro adresy URL proti sadě povolených původů.</span><span class="sxs-lookup"><span data-stu-id="a8846-461">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="a8846-462">Neprováděte rozhodnutí o autorizaci na základě stavu ui, ale pouze ze stavu komponenty.</span><span class="sxs-lookup"><span data-stu-id="a8846-462">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="a8846-463">Zvažte použití [zásad zabezpečení obsahu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) k ochraně před útoky XSS.</span><span class="sxs-lookup"><span data-stu-id="a8846-463">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="a8846-464">Zvažte použití csp a [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) k ochraně proti přepojit.</span><span class="sxs-lookup"><span data-stu-id="a8846-464">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="a8846-465">Ujistěte se, že nastavení CORS jsou vhodné Blazor při povolení CORS nebo explicitně zakázat CORS pro aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8846-465">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="a8846-466">Vyzkoušejte, zda limity na straně Blazor serveru pro aplikaci poskytují přijatelné uživatelské prostředí bez nepřijatelné úrovně rizika.</span><span class="sxs-lookup"><span data-stu-id="a8846-466">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
