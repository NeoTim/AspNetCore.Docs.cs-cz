---
title: ASP.NET Core Blazor ověřování a autorizace
author: guardrex
description: Přečtěte si o Blazor scénářích ověřování a autorizace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/index
ms.openlocfilehash: e905f08f867b73fc37d5fed7138256ac89811312
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402400"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="c4862-103">ASP.NET Core Blazor ověřování a autorizace</span><span class="sxs-lookup"><span data-stu-id="c4862-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="c4862-104">Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c4862-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c4862-105">ASP.NET Core podporuje konfiguraci a správu zabezpečení v Blazor aplikacích.</span><span class="sxs-lookup"><span data-stu-id="c4862-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="c4862-106">Scénáře zabezpečení se mezi Blazor Server aplikacemi a liší Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="c4862-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="c4862-107">Vzhledem Blazor Server k tomu, že aplikace běží na serveru, kontroly autorizace můžou určit:</span><span class="sxs-lookup"><span data-stu-id="c4862-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="c4862-108">Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou k dispozici uživateli).</span><span class="sxs-lookup"><span data-stu-id="c4862-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="c4862-109">Pravidla přístupu pro oblasti aplikace a součástí</span><span class="sxs-lookup"><span data-stu-id="c4862-109">Access rules for areas of the app and components.</span></span>

Blazor WebAssembly<span data-ttu-id="c4862-110">aplikace běží na klientovi.</span><span class="sxs-lookup"><span data-stu-id="c4862-110"> apps run on the client.</span></span> <span data-ttu-id="c4862-111">Autorizace se používá *jenom* k určení možností uživatelského rozhraní, které se mají zobrazit.</span><span class="sxs-lookup"><span data-stu-id="c4862-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="c4862-112">Vzhledem k tomu, že kontroly na straně klienta může uživatel upravit nebo obejít, Blazor WebAssembly nemůže aplikace pravidla přístupu k autorizaci vyhovět.</span><span class="sxs-lookup"><span data-stu-id="c4862-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="c4862-113">[ Razor Konvence autorizace stránek](xref:security/authorization/razor-pages-authorization) se nevztahují na směrovatelné Razor součásti.</span><span class="sxs-lookup"><span data-stu-id="c4862-113">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="c4862-114">Pokud Razor je na [stránce vložena](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view)Nesměrovatelné komponenty, zásady autorizace stránky nepřímo ovlivňují Razor komponentu spolu se zbytkem obsahu stránky.</span><span class="sxs-lookup"><span data-stu-id="c4862-114">If a non-routable Razor component is [embedded in a page](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="c4862-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>a <xref:Microsoft.AspNetCore.Identity.UserManager%601> nejsou podporovány v Razor součástech.</span><span class="sxs-lookup"><span data-stu-id="c4862-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="c4862-116">Ověřování</span><span class="sxs-lookup"><span data-stu-id="c4862-116">Authentication</span></span>

Blazor<span data-ttu-id="c4862-117">používá k vytvoření identity uživatele existující mechanismy ověřování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4862-117"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="c4862-118">Přesný mechanismus závisí na tom, jak Blazor je aplikace hostována, Blazor WebAssembly nebo Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="c4862-118">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor WebAssembly<span data-ttu-id="c4862-119">přihlašovací</span><span class="sxs-lookup"><span data-stu-id="c4862-119"> authentication</span></span>

<span data-ttu-id="c4862-120">V Blazor WebAssembly aplikacích lze kontroly ověřování obejít, protože všechny kódy na straně klienta mohou být změněny uživateli.</span><span class="sxs-lookup"><span data-stu-id="c4862-120">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="c4862-121">Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="c4862-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="c4862-122">Přidejte následující:</span><span class="sxs-lookup"><span data-stu-id="c4862-122">Add the following:</span></span>

* <span data-ttu-id="c4862-123">Odkaz na balíček pro [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) soubor projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4862-123">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="c4862-124">`Microsoft.AspNetCore.Components.Authorization`Obor názvů `_Imports.razor` souboru aplikace</span><span class="sxs-lookup"><span data-stu-id="c4862-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="c4862-125">Pro zpracování ověřování se implementace předdefinované nebo vlastní <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> služby zabývá následujícími oddíly.</span><span class="sxs-lookup"><span data-stu-id="c4862-125">To handle authentication, implementation of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="c4862-126">Další informace o vytváření aplikací a konfigurace najdete v tématu <xref:blazor/security/webassembly/index> .</span><span class="sxs-lookup"><span data-stu-id="c4862-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor Server<span data-ttu-id="c4862-127">přihlašovací</span><span class="sxs-lookup"><span data-stu-id="c4862-127"> authentication</span></span>

Blazor Server<span data-ttu-id="c4862-128">aplikace pracují s připojením v reálném čase vytvořeným pomocí SignalR .</span><span class="sxs-lookup"><span data-stu-id="c4862-128"> apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="c4862-129">[Ověřování v SignalR aplikacích založených na ověřování](xref:signalr/authn-and-authz) se zpracovává při navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="c4862-129">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="c4862-130">Ověřování může být založené na souboru cookie nebo nějakém jiném nosným tokenu.</span><span class="sxs-lookup"><span data-stu-id="c4862-130">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="c4862-131">Další informace o vytváření aplikací a konfigurace najdete v tématu <xref:blazor/security/server/index> .</span><span class="sxs-lookup"><span data-stu-id="c4862-131">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="c4862-132">Služba AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="c4862-132">AuthenticationStateProvider service</span></span>

<span data-ttu-id="c4862-133">Integrovaná <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> služba získá data stavu ověřování z ASP.NET Core `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="c4862-133">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="c4862-134">To je způsob, jakým se stav ověřování integruje se stávajícími mechanismy ověřování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4862-134">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="c4862-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>je základní službou, kterou <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> Komponenta a komponenta používá <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> k získání stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="c4862-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="c4862-136">Obvykle přímo nepoužíváte <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> .</span><span class="sxs-lookup"><span data-stu-id="c4862-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="c4862-137">Použijte [ `AuthorizeView` součást](#authorizeview-component) nebo [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) přístupy popsané dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="c4862-137">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="c4862-138">Hlavní nevýhodou použití <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> přímo je, že součást není automaticky oznámena v případě, že dojde ke změně podkladových dat stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="c4862-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="c4862-139"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>Služba může poskytnout data aktuálního uživatele <xref:System.Security.Claims.ClaimsPrincipal> , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c4862-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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
            <li>@claim.Type: @claim.Value</li>
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

<span data-ttu-id="c4862-140">Pokud `user.Identity.IsAuthenticated` je `true` a vzhledem k tomu, že je uživatel a <xref:System.Security.Claims.ClaimsPrincipal> , mohou být deklarace identity vyhodnoceny a ve vyhodnocování členů.</span><span class="sxs-lookup"><span data-stu-id="c4862-140">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="c4862-141">Další informace o vkládání závislostí (DI) a službách naleznete v tématu <xref:blazor/fundamentals/dependency-injection> a <xref:fundamentals/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="c4862-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="c4862-142">Implementace vlastního AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="c4862-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="c4862-143">Pokud aplikace vyžaduje vlastního poskytovatele, implementujte <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> a přepište `GetAuthenticationStateAsync` :</span><span class="sxs-lookup"><span data-stu-id="c4862-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="c4862-144">V Blazor WebAssembly aplikaci `CustomAuthStateProvider` je služba zaregistrovaná v `Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="c4862-144">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="c4862-145">V Blazor Server aplikaci `CustomAuthStateProvider` je služba zaregistrovaná v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c4862-145">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="c4862-146">Pomocí v `CustomAuthStateProvider` předchozím příkladu se všechny uživatele ověřují pomocí uživatelského jména `mrfibuli` .</span><span class="sxs-lookup"><span data-stu-id="c4862-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="c4862-147">Zveřejnit stav ověřování jako kaskádový parametr</span><span class="sxs-lookup"><span data-stu-id="c4862-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="c4862-148">Pokud jsou v procedurální logice požadovány údaje o stavu ověřování, například při provádění akce aktivované uživatelem, Získejte údaje o stavu ověřování definováním kaskádového parametru typu `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :</span><span class="sxs-lookup"><span data-stu-id="c4862-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

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

<span data-ttu-id="c4862-149">V takovém případě `user.Identity.IsAuthenticated` `true` můžou být deklarace identity výčtové a ve vyhodnocených rolích.</span><span class="sxs-lookup"><span data-stu-id="c4862-149">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="c4862-150">Nastavte `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` kaskádový parametr pomocí <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> komponent a v `App` součásti ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="c4862-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="c4862-151">V Blazor WebAssembly aplikaci přidejte služby pro možnosti a autorizaci do `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="c4862-151">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="c4862-152">V Blazor Server aplikaci už existují služby pro možnosti a autorizaci, takže se nevyžaduje žádná další akce.</span><span class="sxs-lookup"><span data-stu-id="c4862-152">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="c4862-153">Autorizace</span><span class="sxs-lookup"><span data-stu-id="c4862-153">Authorization</span></span>

<span data-ttu-id="c4862-154">Po ověření uživatele se uplatní *autorizační* pravidla, která řídí, co může uživatel dělat.</span><span class="sxs-lookup"><span data-stu-id="c4862-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="c4862-155">Přístup je obvykle udělen nebo odepřen na základě toho, zda:</span><span class="sxs-lookup"><span data-stu-id="c4862-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="c4862-156">Uživatel je ověřený (přihlášený).</span><span class="sxs-lookup"><span data-stu-id="c4862-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="c4862-157">Uživatel je v *roli*.</span><span class="sxs-lookup"><span data-stu-id="c4862-157">A user is in a *role*.</span></span>
* <span data-ttu-id="c4862-158">Uživatel má *deklaraci identity*.</span><span class="sxs-lookup"><span data-stu-id="c4862-158">A user has a *claim*.</span></span>
* <span data-ttu-id="c4862-159">Byla splněna *zásada* .</span><span class="sxs-lookup"><span data-stu-id="c4862-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="c4862-160">Každá z těchto konceptů je stejná jako v aplikaci ASP.NET Core MVC nebo Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c4862-160">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="c4862-161">Další informace o ASP.NET Core zabezpečení najdete v článcích [ASP.NET Core Security a Identity ](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="c4862-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="c4862-162">Komponenta AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="c4862-162">AuthorizeView component</span></span>

<span data-ttu-id="c4862-163"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Komponenta selektivně zobrazuje uživatelské rozhraní v závislosti na tom, zda je uživatel autorizován pro jeho zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c4862-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="c4862-164">Tento přístup je užitečný, když potřebujete jenom *Zobrazit* data pro uživatele a nemusíte používat identitu uživatele v procedurální logice.</span><span class="sxs-lookup"><span data-stu-id="c4862-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="c4862-165">Komponenta zveřejňuje `context` proměnnou typu <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> , kterou můžete použít pro přístup k informacím o přihlášeném uživateli:</span><span class="sxs-lookup"><span data-stu-id="c4862-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="c4862-166">V případě, že se uživatel neověřuje, můžete také Dodejte jiný obsah, který se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="c4862-166">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="c4862-167"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Komponentu lze použít v `NavMenu` komponentě ( `Shared/NavMenu.razor` ) k zobrazení položky seznamu ( `<li>...</li>` ) pro [ `NavLink` komponentu](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), ale Všimněte si, že tento přístup odebere pouze položku seznamu z vykresleného výstupu.</span><span class="sxs-lookup"><span data-stu-id="c4862-167">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="c4862-168">Nebrání uživateli přejít na součást.</span><span class="sxs-lookup"><span data-stu-id="c4862-168">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="c4862-169">Obsah `<Authorized>` `<NotAuthorized>` značek a může obsahovat libovolné položky, jako jsou například jiné interaktivní komponenty.</span><span class="sxs-lookup"><span data-stu-id="c4862-169">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="c4862-170">Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou uvedené v části [autorizace](#authorization) .</span><span class="sxs-lookup"><span data-stu-id="c4862-170">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="c4862-171">Pokud nejsou zadané autorizační podmínky, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> použije se výchozí zásada a bude se považovat za:</span><span class="sxs-lookup"><span data-stu-id="c4862-171">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="c4862-172">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="c4862-172">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="c4862-173">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="c4862-173">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="c4862-174">Ověřování na základě rolí a na základě zásad</span><span class="sxs-lookup"><span data-stu-id="c4862-174">Role-based and policy-based authorization</span></span>

<span data-ttu-id="c4862-175"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Komponenta podporuje autorizaci na základě *rolí* nebo *na základě zásad* .</span><span class="sxs-lookup"><span data-stu-id="c4862-175">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="c4862-176">Pro autorizaci založenou na rolích použijte <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parametr:</span><span class="sxs-lookup"><span data-stu-id="c4862-176">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="c4862-177">Další informace naleznete v tématu <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="c4862-177">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="c4862-178">Pro autorizaci založenou na zásadách použijte <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parametr:</span><span class="sxs-lookup"><span data-stu-id="c4862-178">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="c4862-179">Ověřování na základě deklarací identity je zvláštní případ ověřování na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="c4862-179">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="c4862-180">Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="c4862-180">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="c4862-181">Další informace naleznete v tématu <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="c4862-181">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="c4862-182">Tato rozhraní API je možné použít v Blazor Server Blazor WebAssembly aplikacích nebo.</span><span class="sxs-lookup"><span data-stu-id="c4862-182">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="c4862-183">Pokud <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> není zadán ani ani, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> používá výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="c4862-183">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="c4862-184">Obsah zobrazený během asynchronního ověřování</span><span class="sxs-lookup"><span data-stu-id="c4862-184">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="c4862-185">umožňuje, aby byl stav ověřování určován *asynchronně*.</span><span class="sxs-lookup"><span data-stu-id="c4862-185"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="c4862-186">Primární scénář pro tento přístup je v Blazor WebAssembly aplikacích, které vytvářejí požadavek na externí koncový bod pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="c4862-186">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="c4862-187">V průběhu ověřování ve <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> výchozím nastavení nezobrazí žádný obsah.</span><span class="sxs-lookup"><span data-stu-id="c4862-187">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="c4862-188">Chcete-li zobrazit obsah, když dojde k ověřování, použijte `<Authorizing>` element:</span><span class="sxs-lookup"><span data-stu-id="c4862-188">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="c4862-189">Tento přístup se obvykle nevztahuje na Blazor Server aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4862-189">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor Server<span data-ttu-id="c4862-190">aplikace znají stav ověřování, jakmile se stav naváže.</span><span class="sxs-lookup"><span data-stu-id="c4862-190"> apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="c4862-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing>obsah může být k dispozici v Blazor Server <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> komponentě aplikace, ale obsah se nikdy nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="c4862-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a Blazor Server app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="c4862-192">[Autorizační] – atribut</span><span class="sxs-lookup"><span data-stu-id="c4862-192">[Authorize] attribute</span></span>

<span data-ttu-id="c4862-193">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut lze použít v Razor součástech:</span><span class="sxs-lookup"><span data-stu-id="c4862-193">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="c4862-194">Používat pouze [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) v `@page` součástech, které byly dosaženy prostřednictvím Blazor směrovače.</span><span class="sxs-lookup"><span data-stu-id="c4862-194">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="c4862-195">Autorizace se provádí jenom jako aspekt směrování, a *ne* pro podřízené komponenty vygenerované v rámci stránky.</span><span class="sxs-lookup"><span data-stu-id="c4862-195">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="c4862-196">Chcete-li autorizovat zobrazení určitých částí v rámci stránky, použijte <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> místo toho.</span><span class="sxs-lookup"><span data-stu-id="c4862-196">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="c4862-197">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut také podporuje ověřování na základě rolí nebo na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="c4862-197">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="c4862-198">Pro autorizaci založenou na rolích použijte <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parametr:</span><span class="sxs-lookup"><span data-stu-id="c4862-198">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="c4862-199">Pro autorizaci založenou na zásadách použijte <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parametr:</span><span class="sxs-lookup"><span data-stu-id="c4862-199">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="c4862-200">Pokud není <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> zadán ani ani <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> , [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) používá výchozí zásadu, která ve výchozím nastavení zachází:</span><span class="sxs-lookup"><span data-stu-id="c4862-200">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="c4862-201">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="c4862-201">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="c4862-202">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="c4862-202">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="c4862-203">Přizpůsobení neoprávněného obsahu pomocí součásti směrovače</span><span class="sxs-lookup"><span data-stu-id="c4862-203">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="c4862-204"><xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta společně s <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> komponentou umožňuje aplikaci zadat vlastní obsah, pokud:</span><span class="sxs-lookup"><span data-stu-id="c4862-204">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="c4862-205">Obsah nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="c4862-205">Content isn't found.</span></span>
* <span data-ttu-id="c4862-206">Uživatel nezdařil [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) podmínku použitou pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="c4862-206">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="c4862-207">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut je popsán v části [ `[Authorize]` atributu](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="c4862-207">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="c4862-208">Probíhá asynchronní ověřování.</span><span class="sxs-lookup"><span data-stu-id="c4862-208">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="c4862-209">Ve výchozí Blazor Server šabloně projektu `App` součást ( `App.razor` ) ukazuje, jak nastavit vlastní obsah:</span><span class="sxs-lookup"><span data-stu-id="c4862-209">In the default Blazor Server project template, the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
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
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="c4862-210">Obsah `<NotFound>` `<NotAuthorized>` značek, a `<Authorizing>` může obsahovat libovolné položky, jako jsou například jiné interaktivní komponenty.</span><span class="sxs-lookup"><span data-stu-id="c4862-210">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="c4862-211">Pokud `<NotAuthorized>` není element zadán, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> používá následující záložní zprávu:</span><span class="sxs-lookup"><span data-stu-id="c4862-211">If the `<NotAuthorized>` element isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="c4862-212">Oznámení o změnách stavu ověřování</span><span class="sxs-lookup"><span data-stu-id="c4862-212">Notification about authentication state changes</span></span>

<span data-ttu-id="c4862-213">Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například kvůli tomu, že se uživatel odhlásil nebo jiný uživatel změnil své role), [vlastní `AuthenticationStateProvider` ](#implement-a-custom-authenticationstateprovider) možnost může volitelně vyvolat metodu <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> základní třídy.</span><span class="sxs-lookup"><span data-stu-id="c4862-213">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="c4862-214">Tím se uživatele upozorní na data stavu ověřování (například <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> ) k opakovanému vykreslení pomocí nových dat.</span><span class="sxs-lookup"><span data-stu-id="c4862-214">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="c4862-215">Procesní logika</span><span class="sxs-lookup"><span data-stu-id="c4862-215">Procedural logic</span></span>

<span data-ttu-id="c4862-216">Pokud je aplikace nutná k kontrole autorizačních pravidel v rámci procedurální logiky, použijte `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` k získání uživatele kaskádový parametr typu <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="c4862-216">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="c4862-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`lze kombinovat s jinými službami, například `IAuthorizationService` , k vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="c4862-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
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
> <span data-ttu-id="c4862-218">Do Blazor WebAssembly komponenty aplikace přidejte <xref:Microsoft.AspNetCore.Authorization> <xref:Microsoft.AspNetCore.Components.Authorization> obory názvů a:</span><span class="sxs-lookup"><span data-stu-id="c4862-218">In a Blazor WebAssembly app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="c4862-219">Tyto obory názvů je možné poskytnout globálně přidáním do `_Imports.razor` souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4862-219">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="c4862-220">Řešení chyb</span><span class="sxs-lookup"><span data-stu-id="c4862-220">Troubleshoot errors</span></span>

<span data-ttu-id="c4862-221">Běžné chyby:</span><span class="sxs-lookup"><span data-stu-id="c4862-221">Common errors:</span></span>

* <span data-ttu-id="c4862-222">**Autorizace vyžaduje kaskádový parametr typu `Task\<AuthenticationState>` . Zvažte použití `CascadingAuthenticationState` tohoto zadání.**</span><span class="sxs-lookup"><span data-stu-id="c4862-222">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="c4862-223">**`null`hodnota je přijata pro`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="c4862-223">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="c4862-224">Je možné, že projekt nebyl vytvořen pomocí Blazor Server šablony s povoleným ověřováním.</span><span class="sxs-lookup"><span data-stu-id="c4862-224">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="c4862-225">Zabalte `<CascadingAuthenticationState>` kolem některé části stromu uživatelského rozhraní, například v `App` součásti ( `App.razor` ) následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c4862-225">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="c4862-226"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` Doplní kaskádový parametr, který zase získá z podkladové <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> služby di.</span><span class="sxs-lookup"><span data-stu-id="c4862-226">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4862-227">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c4862-227">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="c4862-228">[Super Blazor : ověřování –](https://github.com/AdrienTorris/awesome-blazor#authentication) ukázkové odkazy komunity</span><span class="sxs-lookup"><span data-stu-id="c4862-228">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
