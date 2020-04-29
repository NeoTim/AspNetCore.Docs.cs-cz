---
title: ASP.NET Core Blazor ověřování a autorizace
author: guardrex
description: Přečtěte Blazor si o scénářích ověřování a autorizace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: ced8e90147b08bc75aec4534fdd8d8552506f88c
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206096"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="3f5fc-103">ASP.NET Core Blazor ověřování a autorizace</span><span class="sxs-lookup"><span data-stu-id="3f5fc-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="3f5fc-104">Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3f5fc-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="3f5fc-105">Pro pokyny v tomto článku, které platí pro Blazor WebAssembly, je vyžadována Blazor šablona ASP.NET Core WebAssembly verze 3,2 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="3f5fc-106">Pokud nepoužíváte Visual Studio verze 16,6 Preview 2 nebo novější, Získejte nejnovější Blazor šablonu WebAssembly podle pokynů v <xref:blazor/get-started>části.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="3f5fc-107">ASP.NET Core podporuje konfiguraci a správu zabezpečení v Blazor aplikacích.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="3f5fc-108">Scénáře zabezpečení se liší Blazor mezi serverem Blazor a aplikacemi WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="3f5fc-109">Vzhledem Blazor k tomu, že serverové aplikace běží na serveru, kontroly autorizace můžou určit:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="3f5fc-110">Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou k dispozici uživateli).</span><span class="sxs-lookup"><span data-stu-id="3f5fc-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="3f5fc-111">Pravidla přístupu pro oblasti aplikace a součástí</span><span class="sxs-lookup"><span data-stu-id="3f5fc-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="3f5fc-112">Aplikace WebAssembly běží na klientovi.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="3f5fc-113">Autorizace se používá *jenom* k určení možností uživatelského rozhraní, které se mají zobrazit.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="3f5fc-114">Vzhledem k tomu, že kontroly na straně klienta může uživatel upravit nebo obejít, Blazor nemůže aplikace typu WebAssembly vyhovět autorizačním pravidlům.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="3f5fc-115">[Razor Pages konvence autorizace](xref:security/authorization/razor-pages-authorization) se nevztahují na směrovatelné komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="3f5fc-116">Pokud je na [stránce vložena](xref:blazor/integrate-components#render-components-from-a-page-or-view)Nesměrovatelné komponenty Razor, zásady autorizace této stránky nepřímo ovlivňují komponentu Razor společně se zbytkem obsahu stránky.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="3f5fc-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>a <xref:Microsoft.AspNetCore.Identity.UserManager%601> nejsou podporovány v součástech Razor.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="3f5fc-118">Authentication</span><span class="sxs-lookup"><span data-stu-id="3f5fc-118">Authentication</span></span>

Blazor<span data-ttu-id="3f5fc-119">používá k vytvoření identity uživatele existující mechanismy ověřování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="3f5fc-120">Přesný mechanismus závisí na tom, jak Blazor je aplikace hostována Blazor , WebAssembly Blazor nebo server.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor<span data-ttu-id="3f5fc-121">Ověřování pro WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3f5fc-121"> WebAssembly authentication</span></span>

<span data-ttu-id="3f5fc-122">V Blazor aplikacích pro WebAssembly lze kontroly ověřování obejít, protože všechny kódy na straně klienta mohou být změněny uživateli.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="3f5fc-123">Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="3f5fc-124">Přidejte následující:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-124">Add the following:</span></span>

* <span data-ttu-id="3f5fc-125">Odkaz na balíček pro [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) do souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="3f5fc-126">`Microsoft.AspNetCore.Components.Authorization` Obor názvů pro soubor *_Imports. Razor* aplikace</span><span class="sxs-lookup"><span data-stu-id="3f5fc-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="3f5fc-127">Pro zpracování ověřování se implementace předdefinované nebo vlastní `AuthenticationStateProvider` služby zabývá následujícími oddíly.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="3f5fc-128">Další informace o vytváření aplikací a konfigurace najdete v tématu <xref:security/blazor/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor<span data-ttu-id="3f5fc-129">Ověřování serveru</span><span class="sxs-lookup"><span data-stu-id="3f5fc-129"> Server authentication</span></span>

Blazor<span data-ttu-id="3f5fc-130">Serverové aplikace fungují prostřednictvím připojení v reálném čase vytvořeného pomocí SignalR.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="3f5fc-131">[Ověřování v SignalRaplikacích založených na ověřování](xref:signalr/authn-and-authz) se zpracovává při navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="3f5fc-132">Ověřování může být založené na souboru cookie nebo nějakém jiném nosným tokenu.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="3f5fc-133">Další informace o vytváření aplikací a konfigurace najdete v tématu <xref:security/blazor/server/index>.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-133">For more information on creating apps and configuration, see <xref:security/blazor/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="3f5fc-134">Služba AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="3f5fc-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="3f5fc-135">Integrovaná `AuthenticationStateProvider` služba získá data stavu ověřování z ASP.NET Core `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="3f5fc-136">To je způsob, jakým se stav ověřování integruje se stávajícími mechanismy ověřování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="3f5fc-137">`AuthenticationStateProvider`je základní službou, kterou `AuthorizeView` komponenta a `CascadingAuthenticationState` komponenta používá k získání stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="3f5fc-138">Obvykle přímo nepoužíváte `AuthenticationStateProvider` .</span><span class="sxs-lookup"><span data-stu-id="3f5fc-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="3f5fc-139">Použijte [komponentu AuthorizeView](#authorizeview-component) nebo [\<úlohu AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) přístupy popsané dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="3f5fc-140">Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že součást není automaticky oznámena v případě, že dojde ke změně podkladových dat stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="3f5fc-141">`AuthenticationStateProvider` Služba může poskytnout <xref:System.Security.Claims.ClaimsPrincipal> data aktuálního uživatele, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="3f5fc-142">Pokud `user.Identity.IsAuthenticated` je `true` a vzhledem k tomu <xref:System.Security.Claims.ClaimsPrincipal>, že je uživatel a, mohou být deklarace identity vyhodnoceny a ve vyhodnocování členů.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="3f5fc-143">Další informace o vkládání závislostí (DI) a službách naleznete v <xref:blazor/dependency-injection> tématu <xref:fundamentals/dependency-injection>a.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="3f5fc-144">Implementace vlastního AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="3f5fc-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="3f5fc-145">Pokud aplikace vyžaduje vlastního poskytovatele, implementujte `AuthenticationStateProvider` a přepište `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="3f5fc-146">Blazor V aplikaci WebAssembly je `CustomAuthStateProvider` služba zaregistrovaná v `Main` *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="3f5fc-147">V Blazor serverové aplikaci je `CustomAuthStateProvider` služba zaregistrovaná v: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="3f5fc-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="3f5fc-148">`CustomAuthStateProvider` Pomocí v předchozím příkladu se všechny uživatele ověřují pomocí uživatelského jména `mrfibuli`.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="3f5fc-149">Zveřejnit stav ověřování jako kaskádový parametr</span><span class="sxs-lookup"><span data-stu-id="3f5fc-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="3f5fc-150">Pokud jsou v procedurální logice požadovány údaje o stavu ověřování, například při provádění akce aktivované uživatelem, Získejte údaje o stavu ověřování definováním kaskádového parametru typu `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="3f5fc-151">`true`V takovém případě `user.Identity.IsAuthenticated` můžou být deklarace identity výčtové a ve vyhodnocených rolích.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="3f5fc-152">`Task<AuthenticationState>` Nastavte kaskádový parametr pomocí komponent `AuthorizeRouteView` a `CascadingAuthenticationState` v `App` součásti (*App. Razor*):</span><span class="sxs-lookup"><span data-stu-id="3f5fc-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="3f5fc-153">Blazor V aplikaci WebAssembly přidejte služby pro možnosti a autorizaci do `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="3f5fc-154">V Blazor serverové aplikaci jsou již nainstalovány služby pro možnosti a autorizaci, takže není nutné provádět žádnou další akci.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="3f5fc-155">Autorizace</span><span class="sxs-lookup"><span data-stu-id="3f5fc-155">Authorization</span></span>

<span data-ttu-id="3f5fc-156">Po ověření uživatele se uplatní *autorizační* pravidla, která řídí, co může uživatel dělat.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="3f5fc-157">Přístup je obvykle udělen nebo odepřen na základě toho, zda:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="3f5fc-158">Uživatel je ověřený (přihlášený).</span><span class="sxs-lookup"><span data-stu-id="3f5fc-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="3f5fc-159">Uživatel je v *roli*.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-159">A user is in a *role*.</span></span>
* <span data-ttu-id="3f5fc-160">Uživatel má *deklaraci identity*.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-160">A user has a *claim*.</span></span>
* <span data-ttu-id="3f5fc-161">Byla splněna *zásada* .</span><span class="sxs-lookup"><span data-stu-id="3f5fc-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="3f5fc-162">Každá z těchto konceptů je stejná jako u ASP.NET Core MVC nebo Razor Pages aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="3f5fc-163">Další informace o ASP.NET Core zabezpečení najdete v článcích [ASP.NET Core Security and identity](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="3f5fc-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="3f5fc-164">Komponenta AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="3f5fc-164">AuthorizeView component</span></span>

<span data-ttu-id="3f5fc-165">`AuthorizeView` Komponenta selektivně zobrazuje uživatelské rozhraní v závislosti na tom, zda je uživatel autorizován pro jeho zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="3f5fc-166">Tento přístup je užitečný, když potřebujete jenom *Zobrazit* data pro uživatele a nemusíte používat identitu uživatele v procedurální logice.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="3f5fc-167">Komponenta zveřejňuje `context` proměnnou typu `AuthenticationState`, kterou můžete použít pro přístup k informacím o přihlášeném uživateli:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="3f5fc-168">V případě, že se uživatel neověřuje, můžete také Dodejte jiný obsah, který se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-168">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="3f5fc-169">`AuthorizeView` Komponentu `NavMenu` lze použít v komponentě (*Shared/NavMenu. Razor*) k zobrazení položky seznamu (`<li>...</li>`) pro `NavLink`, ale tento přístup odstraní pouze položku seznamu z vykresleného výstupu.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="3f5fc-170">Nebrání uživateli přejít na součást.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="3f5fc-171">Obsah značek `<Authorized>` a `<NotAuthorized>` může obsahovat libovolné položky, jako jsou například jiné interaktivní komponenty.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="3f5fc-172">Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou uvedené v části [autorizace](#authorization) .</span><span class="sxs-lookup"><span data-stu-id="3f5fc-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="3f5fc-173">Pokud nejsou zadané autorizační podmínky, `AuthorizeView` použije se výchozí zásada a bude se považovat za:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="3f5fc-174">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="3f5fc-175">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="3f5fc-176">Ověřování na základě rolí a na základě zásad</span><span class="sxs-lookup"><span data-stu-id="3f5fc-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="3f5fc-177">`AuthorizeView` Komponenta podporuje autorizaci na základě *rolí* nebo *na základě zásad* .</span><span class="sxs-lookup"><span data-stu-id="3f5fc-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="3f5fc-178">Pro autorizaci založenou na rolích `Roles` použijte parametr:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="3f5fc-179">Další informace naleznete v tématu <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="3f5fc-180">Pro autorizaci založenou na zásadách použijte `Policy` parametr:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="3f5fc-181">Ověřování na základě deklarací identity je zvláštní případ ověřování na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="3f5fc-182">Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="3f5fc-183">Další informace naleznete v tématu <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="3f5fc-184">Tato rozhraní API se dají použít v Blazor aplikacích serveru Blazor nebo WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="3f5fc-185">Pokud není `Roles` zadán `Policy` ani ani, `AuthorizeView` používá výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="3f5fc-186">Obsah zobrazený během asynchronního ověřování</span><span class="sxs-lookup"><span data-stu-id="3f5fc-186">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="3f5fc-187">umožňuje, aby byl stav ověřování určován *asynchronně*.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-187"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="3f5fc-188">Primární scénář pro tento přístup je v Blazor aplikacích pro WebAssembly, které vytvářejí požadavek na externí koncový bod pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="3f5fc-189">V průběhu ověřování ve výchozím nastavení `AuthorizeView` nezobrazí žádný obsah.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="3f5fc-190">Chcete-li zobrazit obsah, když dojde k `<Authorizing>` ověřování, použijte element:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="3f5fc-191">Tento přístup se obvykle nevztahuje na Blazor serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="3f5fc-192">Serverové aplikace znají stav ověřování, jakmile se stav naváže.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="3f5fc-193">`Authorizing`obsah může být k dispozici Blazor v `AuthorizeView` součásti serverové aplikace, ale obsah se nikdy nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="3f5fc-194">[Autorizační] – atribut</span><span class="sxs-lookup"><span data-stu-id="3f5fc-194">[Authorize] attribute</span></span>

<span data-ttu-id="3f5fc-195">`[Authorize]` Atribut lze použít v součástech Razor:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="3f5fc-196">Používat `[Authorize]` pouze v `@page` součástech, které Blazor byly dosaženy prostřednictvím směrovače.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="3f5fc-197">Autorizace se provádí jenom jako aspekt směrování, a *ne* pro podřízené komponenty vygenerované v rámci stránky.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="3f5fc-198">Chcete-li autorizovat zobrazení určitých částí v rámci stránky, použijte `AuthorizeView` místo toho.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="3f5fc-199">`[Authorize]` Atribut také podporuje ověřování na základě rolí nebo na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="3f5fc-200">Pro autorizaci založenou na rolích `Roles` použijte parametr:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="3f5fc-201">Pro autorizaci založenou na zásadách použijte `Policy` parametr:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="3f5fc-202">Pokud není `Roles` zadán `Policy` ani ani, `[Authorize]` používá výchozí zásadu, která ve výchozím nastavení zachází:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="3f5fc-203">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="3f5fc-204">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="3f5fc-205">Přizpůsobení neoprávněného obsahu pomocí součásti směrovače</span><span class="sxs-lookup"><span data-stu-id="3f5fc-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="3f5fc-206">`Router` Komponenta společně s `AuthorizeRouteView` komponentou umožňuje aplikaci zadat vlastní obsah, pokud:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="3f5fc-207">Obsah nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-207">Content isn't found.</span></span>
* <span data-ttu-id="3f5fc-208">Uživatel nezdařil `[Authorize]` podmínku použitou pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="3f5fc-209">`[Authorize]` Atribut je popsán v části [ `[Authorize]` atributu](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="3f5fc-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="3f5fc-210">Probíhá asynchronní ověřování.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="3f5fc-211">V šabloně výchozího Blazor serverového projektu `App` komponenta (*App. Razor*) ukazuje, jak nastavit vlastní obsah:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="3f5fc-212">Obsah značek `<NotFound>`, `<NotAuthorized>`a `<Authorizing>` může obsahovat libovolné položky, jako jsou například jiné interaktivní komponenty.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="3f5fc-213">Pokud není `<NotAuthorized>` element zadán, `AuthorizeRouteView` používá následující záložní zprávu:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="3f5fc-214">Oznámení o změnách stavu ověřování</span><span class="sxs-lookup"><span data-stu-id="3f5fc-214">Notification about authentication state changes</span></span>

<span data-ttu-id="3f5fc-215">Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například kvůli tomu, že se uživatel odhlásil nebo jiný uživatel změnil své role), [vlastní AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) může volitelně vyvolat metodu `NotifyAuthenticationStateChanged` pro `AuthenticationStateProvider` základní třídu.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="3f5fc-216">Tím se uživatele upozorní na data stavu ověřování (například `AuthorizeView`) k opakovanému vykreslení pomocí nových dat.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="3f5fc-217">Procesní logika</span><span class="sxs-lookup"><span data-stu-id="3f5fc-217">Procedural logic</span></span>

<span data-ttu-id="3f5fc-218">Pokud je aplikace nutná k kontrole autorizačních pravidel v rámci procedurální logiky, použijte k získání uživatele kaskádový parametr `Task<AuthenticationState>` typu <xref:System.Security.Claims.ClaimsPrincipal>.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="3f5fc-219">`Task<AuthenticationState>`lze kombinovat s jinými službami, například `IAuthorizationService`, k vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="3f5fc-220">Blazor Do komponenty aplikace WebAssembly přidejte obory názvů `Microsoft.AspNetCore.Authorization` a `Microsoft.AspNetCore.Components.Authorization` :</span><span class="sxs-lookup"><span data-stu-id="3f5fc-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="3f5fc-221">Tyto obory názvů je možné poskytnout globálně tak, že je přidáte do souboru *_Imports. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="3f5fc-222">Autorizace v Blazor aplikacích pro WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3f5fc-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="3f5fc-223">V Blazor aplikacích pro WebAssembly lze kontroly autorizace obejít, protože všechny kódy na straně klienta mohou být změněny uživateli.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="3f5fc-224">Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="3f5fc-225">**Na serveru vždy provádějte kontroly autorizace v libovolném koncovém bodu rozhraní API, ke kterému přistupovala aplikace na straně klienta.**</span><span class="sxs-lookup"><span data-stu-id="3f5fc-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="3f5fc-226">Další informace najdete v článcích v části <xref:security/blazor/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="3f5fc-227">Řešení chyb</span><span class="sxs-lookup"><span data-stu-id="3f5fc-227">Troubleshoot errors</span></span>

<span data-ttu-id="3f5fc-228">Běžné chyby:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-228">Common errors:</span></span>

* <span data-ttu-id="3f5fc-229">**Autorizace vyžaduje kaskádový parametr typu Task\<AuthenticationState>. Zvažte použití CascadingAuthenticationState k zadání.**</span><span class="sxs-lookup"><span data-stu-id="3f5fc-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="3f5fc-230">**`null`hodnota je přijata pro`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="3f5fc-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="3f5fc-231">Je možné, že projekt nebyl vytvořen pomocí šablony Blazor serveru s povoleným ověřováním.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="3f5fc-232">Zabalte `<CascadingAuthenticationState>` kolem některé části stromu uživatelského rozhraní, například do `App` komponenty (*App. Razor*), následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="3f5fc-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="3f5fc-233">`CascadingAuthenticationState` Doplní `Task<AuthenticationState>` kaskádový parametr, který zase získá z podkladové `AuthenticationStateProvider` služby di.</span><span class="sxs-lookup"><span data-stu-id="3f5fc-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f5fc-234">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3f5fc-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="3f5fc-235">[Super Blazor: ověřování –](https://github.com/AdrienTorris/awesome-blazor#authentication) ukázkové odkazy komunity</span><span class="sxs-lookup"><span data-stu-id="3f5fc-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
