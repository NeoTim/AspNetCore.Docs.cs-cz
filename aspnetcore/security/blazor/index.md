---
title: ASP.NET Blazor základní ověřování a autorizace
author: guardrex
description: Informace Blazor o scénářích ověřování a autorizace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501312"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a><span data-ttu-id="12212-103">ASP.NET Blazor základní ověřování a autorizace</span><span class="sxs-lookup"><span data-stu-id="12212-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="12212-104">[Steve Sanderson](https://github.com/SteveSandersonMS) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="12212-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="12212-105">Pokyny v tomto článku, které Blazor se vztahují k Blazor webové sestavě, je vyžadována šablona ASP.NET Core WebAssembly verze 3.2 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="12212-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="12212-106">Pokud nepoužíváte Visual Studio verze 16.6 Preview 2 nebo Blazor novější, získejte nejnovější <xref:blazor/get-started>šablonu WebAssembly podle pokynů v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="12212-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="12212-107">ASP.NET Core podporuje konfiguraci a Blazor správu zabezpečení aplikací.</span><span class="sxs-lookup"><span data-stu-id="12212-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="12212-108">Scénáře zabezpečení Blazor se Blazor liší mezi aplikacemi Server a WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="12212-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="12212-109">Vzhledem k tomu, Blazor že serverové aplikace běží na serveru, mohou kontroly autorizací určit:</span><span class="sxs-lookup"><span data-stu-id="12212-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="12212-110">Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou uživateli k dispozici).</span><span class="sxs-lookup"><span data-stu-id="12212-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="12212-111">Přístup k pravidlům pro oblasti aplikace a komponent.</span><span class="sxs-lookup"><span data-stu-id="12212-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="12212-112">Aplikace WebAssembly běží na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="12212-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="12212-113">Autorizace se používá *pouze* k určení, které možnosti ui chcete zobrazit.</span><span class="sxs-lookup"><span data-stu-id="12212-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="12212-114">Vzhledem k tomu, že kontroly na straně Blazor klienta může uživatel upravit nebo obejít, aplikace WebAssembly nemůže vynutit pravidla přístupu k autorizaci.</span><span class="sxs-lookup"><span data-stu-id="12212-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="12212-115">[Konvence autorizace Razor Pages](xref:security/authorization/razor-pages-authorization) se nevztahují na směrovatelné komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="12212-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="12212-116">Pokud je na [stránce vložena](xref:blazor/integrate-components#render-components-from-a-page-or-view)nesměrovatelná komponenta Razor , konvence autorizace stránky nepřímo ovlivňují komponentu Razor spolu se zbytkem obsahu stránky.</span><span class="sxs-lookup"><span data-stu-id="12212-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="12212-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>a <xref:Microsoft.AspNetCore.Identity.UserManager%601> nejsou podporovány v komponentách Razor.</span><span class="sxs-lookup"><span data-stu-id="12212-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="12212-118">Authentication</span><span class="sxs-lookup"><span data-stu-id="12212-118">Authentication</span></span>

Blazor<span data-ttu-id="12212-119">používá existující mechanismy ověřování ASP.NET Core k vytvoření identity uživatele.</span><span class="sxs-lookup"><span data-stu-id="12212-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="12212-120">Přesný mechanismus závisí Blazor na tom, Blazor jak je Blazor aplikace hostována, webassembly nebo server.</span><span class="sxs-lookup"><span data-stu-id="12212-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor<span data-ttu-id="12212-121">Ověřování webové sestavy</span><span class="sxs-lookup"><span data-stu-id="12212-121"> WebAssembly authentication</span></span>

<span data-ttu-id="12212-122">V Blazor aplikacích WebAssembly lze kontroly ověřování obejít, protože všechny kódy na straně klienta mohou uživatelé upravovat.</span><span class="sxs-lookup"><span data-stu-id="12212-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="12212-123">Totéž platí pro všechny technologie aplikací na straně klienta, včetně javascriptových spa architektur nebo nativních aplikací pro jakýkoli operační systém.</span><span class="sxs-lookup"><span data-stu-id="12212-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="12212-124">Přidejte následující:</span><span class="sxs-lookup"><span data-stu-id="12212-124">Add the following:</span></span>

* <span data-ttu-id="12212-125">Odkaz na balíček pro [Microsoft.AspNetCore.Components.Autorizace](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="12212-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="12212-126">Obor `Microsoft.AspNetCore.Components.Authorization` názvů do souboru *_Imports.razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="12212-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="12212-127">Pro zpracování ověřování je implementace předdefinované `AuthenticationStateProvider` nebo vlastní služby popsána v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="12212-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="12212-128">Další informace o vytváření aplikací <xref:security/blazor/webassembly/index>a konfigurace naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="12212-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="opno-locblazor-server-authentication"></a>Blazor<span data-ttu-id="12212-129">Ověřování serveru</span><span class="sxs-lookup"><span data-stu-id="12212-129"> Server authentication</span></span>

Blazor<span data-ttu-id="12212-130">Serverové aplikace pracují přes připojení v reálném SignalRčase, které je vytvořeno pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="12212-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="12212-131">[Ověřování SignalRv aplikacích založených na](xref:signalr/authn-and-authz) zabezpečení se zpracovává při navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="12212-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="12212-132">Ověřování může být založeno na souboru cookie nebo jiném nožním tokenu.</span><span class="sxs-lookup"><span data-stu-id="12212-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="12212-133">Další informace o vytváření aplikací <xref:security/blazor/server>a konfigurace naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="12212-133">For more information on creating apps and configuration, see <xref:security/blazor/server>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="12212-134">Služba AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="12212-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="12212-135">Vestavěná `AuthenticationStateProvider` služba získává data o stavu ověřování `HttpContext.User`z ASP.NET Core .</span><span class="sxs-lookup"><span data-stu-id="12212-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="12212-136">Takto se integruje stav ověřování s existujícími mechanismy ověřování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12212-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="12212-137">`AuthenticationStateProvider`je základní služba používaná `CascadingAuthenticationState` komponentou a komponentou `AuthorizeView` k získání stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="12212-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="12212-138">Obvykle se nepoužíváte `AuthenticationStateProvider` přímo.</span><span class="sxs-lookup"><span data-stu-id="12212-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="12212-139">Použijte [přístupy authorizeview nebo](#authorizeview-component) [úlohy\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) popsané dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="12212-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="12212-140">Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že komponenta není automaticky upozorněna, pokud se změní základní data stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="12212-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="12212-141">Služba `AuthenticationStateProvider` může poskytnout <xref:System.Security.Claims.ClaimsPrincipal> data aktuálního uživatele, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="12212-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="12212-142">Pokud `user.Identity.IsAuthenticated` `true` je a protože <xref:System.Security.Claims.ClaimsPrincipal>uživatel je , deklarace mohou být výčtu a členství v rolích vyhodnoceny.</span><span class="sxs-lookup"><span data-stu-id="12212-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="12212-143">Další informace o vkládání závislostí (DI) <xref:blazor/dependency-injection> <xref:fundamentals/dependency-injection>a služby naleznete v tématu a .</span><span class="sxs-lookup"><span data-stu-id="12212-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="12212-144">Implementace vlastního zprostředkovatele AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="12212-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="12212-145">Pokud aplikace vyžaduje vlastního poskytovatele, implementujte `AuthenticationStateProvider` a přepište `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="12212-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="12212-146">V Blazor aplikaci WebAssembly `CustomAuthStateProvider` je služba `Main` registrována v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="12212-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="12212-147">V Blazor aplikaci Server `CustomAuthStateProvider` je služba `Startup.ConfigureServices`registrována v :</span><span class="sxs-lookup"><span data-stu-id="12212-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="12212-148">Pomocí `CustomAuthStateProvider` v předchozím příkladu jsou všichni uživatelé ověřeni pomocí uživatelského jména `mrfibuli`.</span><span class="sxs-lookup"><span data-stu-id="12212-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="12212-149">Vystavit stav ověřování jako kaskádový parametr</span><span class="sxs-lookup"><span data-stu-id="12212-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="12212-150">Pokud jsou pro procedurální logiku vyžadována data o stavu ověřování, například při provádění akce aktivované uživatelem, získají data o stavu ověřování definováním kaskádového parametru typu `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="12212-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

<span data-ttu-id="12212-151">Pokud `user.Identity.IsAuthenticated` `true`je , deklarace mohou být výčty a členství v rolích vyhodnoceny.</span><span class="sxs-lookup"><span data-stu-id="12212-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="12212-152">Nastavte `Task<AuthenticationState>` kaskádový parametr pomocí `AuthorizeRouteView` komponent `CascadingAuthenticationState` a v `App` komponentě *(App.razor*):</span><span class="sxs-lookup"><span data-stu-id="12212-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

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

<span data-ttu-id="12212-153">V Blazor aplikaci WebAssembly přidejte služby `Program.Main`pro možnosti a autorizaci do aplikace :</span><span class="sxs-lookup"><span data-stu-id="12212-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="12212-154">V Blazor aplikaci Server jsou již k dispozici služby pro možnosti a autorizaci, takže není nutná žádná další akce.</span><span class="sxs-lookup"><span data-stu-id="12212-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="12212-155">Autorizace</span><span class="sxs-lookup"><span data-stu-id="12212-155">Authorization</span></span>

<span data-ttu-id="12212-156">Po ověření uživatele jsou použita *autorizační* pravidla k řízení toho, co může uživatel dělat.</span><span class="sxs-lookup"><span data-stu-id="12212-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="12212-157">Přístup je obvykle udělen nebo odepřen na základě toho, zda:</span><span class="sxs-lookup"><span data-stu-id="12212-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="12212-158">Uživatel je ověřen (přihlášen).</span><span class="sxs-lookup"><span data-stu-id="12212-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="12212-159">Uživatel je v *roli*.</span><span class="sxs-lookup"><span data-stu-id="12212-159">A user is in a *role*.</span></span>
* <span data-ttu-id="12212-160">Uživatel má *nárok*.</span><span class="sxs-lookup"><span data-stu-id="12212-160">A user has a *claim*.</span></span>
* <span data-ttu-id="12212-161">*Politika* je splněna.</span><span class="sxs-lookup"><span data-stu-id="12212-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="12212-162">Každý z těchto konceptů je stejný jako v aplikaci ASP.NET Core MVC nebo Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="12212-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="12212-163">Další informace o ASP.NET základní zabezpečení naleznete v článcích [v části ASP.NET základní zabezpečení a identity](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="12212-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="12212-164">Součást AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="12212-164">AuthorizeView component</span></span>

<span data-ttu-id="12212-165">Komponenta `AuthorizeView` selektivně zobrazí uživatelské rozhraní v závislosti na tom, zda je uživatel oprávněn jej zobrazit.</span><span class="sxs-lookup"><span data-stu-id="12212-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="12212-166">Tento přístup je užitečný pouze v případě, že potřebujete *zobrazit* data pro uživatele a není nutné používat identitu uživatele v procedurální logice.</span><span class="sxs-lookup"><span data-stu-id="12212-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="12212-167">Komponenta zpřístupňuje proměnnou `context` typu `AuthenticationState`, kterou můžete použít k přístupu k informacím o přihlášeném uživateli:</span><span class="sxs-lookup"><span data-stu-id="12212-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="12212-168">Pokud uživatel není ověřen, můžete také zadat jiný obsah pro zobrazení:</span><span class="sxs-lookup"><span data-stu-id="12212-168">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="12212-169">Komponentu `AuthorizeView` lze použít `NavMenu` v komponentě (*Shared/NavMenu.razor*)`<li>...</li>`k `NavLink`zobrazení položky seznamu ( ) pro , ale všimněte si, že tento přístup odebere pouze položku seznamu z vykresleného výstupu.</span><span class="sxs-lookup"><span data-stu-id="12212-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="12212-170">Nezabrání uživateli v navigaci na komponentu.</span><span class="sxs-lookup"><span data-stu-id="12212-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="12212-171">Obsah a `<Authorized>` `<NotAuthorized>` značky mohou obsahovat libovolné položky, například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="12212-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="12212-172">Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti rozhraní nebo přístup, jsou zahrnuty v části [Autorizace.](#authorization)</span><span class="sxs-lookup"><span data-stu-id="12212-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="12212-173">Pokud nejsou zadány podmínky `AuthorizeView` autorizace, použije výchozí zásady a zachází:</span><span class="sxs-lookup"><span data-stu-id="12212-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="12212-174">Ověření (přihlášení) uživatelé jako autorizovaní.</span><span class="sxs-lookup"><span data-stu-id="12212-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="12212-175">Neověřené (odhlášení) uživatelé jako neoprávnění.</span><span class="sxs-lookup"><span data-stu-id="12212-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="12212-176">Autorizace založená na rolích a zásadách</span><span class="sxs-lookup"><span data-stu-id="12212-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="12212-177">Komponenta `AuthorizeView` podporuje *autorizaci založenou na rolích* nebo *zásadách.*</span><span class="sxs-lookup"><span data-stu-id="12212-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="12212-178">Pro autorizaci založenou `Roles` na rolích použijte parametr:</span><span class="sxs-lookup"><span data-stu-id="12212-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="12212-179">Další informace naleznete v tématu <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="12212-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="12212-180">Pro autorizaci založenou `Policy` na zásadách použijte parametr:</span><span class="sxs-lookup"><span data-stu-id="12212-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="12212-181">Autorizace založená na deklaracích je zvláštní případ autorizace založené na zásadách.</span><span class="sxs-lookup"><span data-stu-id="12212-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="12212-182">Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci.</span><span class="sxs-lookup"><span data-stu-id="12212-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="12212-183">Další informace naleznete v tématu <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="12212-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="12212-184">Tato api lze použít Blazor v Blazor aplikacích Server nebo WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="12212-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="12212-185">Pokud ani `Roles` `Policy` není zadán, `AuthorizeView` použije výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="12212-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="12212-186">Obsah zobrazený během asynchronního ověřování</span><span class="sxs-lookup"><span data-stu-id="12212-186">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="12212-187">umožňuje *asynchronně*určit stav ověřování .</span><span class="sxs-lookup"><span data-stu-id="12212-187"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="12212-188">Primární scénář pro tento Blazor přístup je v aplikacích WebAssembly, které podají požadavek na externí koncový bod pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="12212-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="12212-189">Během ověřování se `AuthorizeView` ve výchozím nastavení nezobrazuje žádný obsah.</span><span class="sxs-lookup"><span data-stu-id="12212-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="12212-190">Chcete-li zobrazit obsah při `<Authorizing>` ověřování, použijte prvek:</span><span class="sxs-lookup"><span data-stu-id="12212-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="12212-191">Tento přístup se obvykle Blazor nevztahuje na serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="12212-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="12212-192">Serverové aplikace znají stav ověřování, jakmile je stav vytvořen.</span><span class="sxs-lookup"><span data-stu-id="12212-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="12212-193">`Authorizing`obsah může být poskytnut Blazor v `AuthorizeView` součásti aplikace Server, ale obsah se nikdy nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="12212-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="12212-194">[Autorizovat] atribut</span><span class="sxs-lookup"><span data-stu-id="12212-194">[Authorize] attribute</span></span>

<span data-ttu-id="12212-195">Atribut `[Authorize]` lze použít v komponentách Razor:</span><span class="sxs-lookup"><span data-stu-id="12212-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="12212-196">Používejte `[Authorize]` pouze `@page` u součástí Blazor dostupných přes router.</span><span class="sxs-lookup"><span data-stu-id="12212-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="12212-197">Autorizace se provádí pouze jako aspekt směrování a *nikoli* pro podřízené součásti vykreslené na stránce.</span><span class="sxs-lookup"><span data-stu-id="12212-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="12212-198">Chcete-li autorizovat zobrazení určitých `AuthorizeView` částí na stránce, použijte místo toho.</span><span class="sxs-lookup"><span data-stu-id="12212-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="12212-199">Atribut `[Authorize]` také podporuje autorizaci založenou na rolích nebo zásadách.</span><span class="sxs-lookup"><span data-stu-id="12212-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="12212-200">Pro autorizaci založenou `Roles` na rolích použijte parametr:</span><span class="sxs-lookup"><span data-stu-id="12212-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="12212-201">Pro autorizaci založenou `Policy` na zásadách použijte parametr:</span><span class="sxs-lookup"><span data-stu-id="12212-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="12212-202">Pokud ani `Roles` `Policy` není zadán, `[Authorize]` používá výchozí zásady, které ve výchozím nastavení je k léčbě:</span><span class="sxs-lookup"><span data-stu-id="12212-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="12212-203">Ověření (přihlášení) uživatelé jako autorizovaní.</span><span class="sxs-lookup"><span data-stu-id="12212-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="12212-204">Neověřené (odhlášení) uživatelé jako neoprávnění.</span><span class="sxs-lookup"><span data-stu-id="12212-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="12212-205">Přizpůsobení neoprávněného obsahu pomocí komponenty Router</span><span class="sxs-lookup"><span data-stu-id="12212-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="12212-206">Komponenta `Router` ve spojení `AuthorizeRouteView` s komponentou umožňuje aplikaci určit vlastní obsah, pokud:</span><span class="sxs-lookup"><span data-stu-id="12212-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="12212-207">Obsah nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="12212-207">Content isn't found.</span></span>
* <span data-ttu-id="12212-208">Uživatel selže `[Authorize]` podmínku použitou na komponentu.</span><span class="sxs-lookup"><span data-stu-id="12212-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="12212-209">Atribut `[Authorize]` je popsán v části [ `[Authorize]` atributu.](#authorize-attribute)</span><span class="sxs-lookup"><span data-stu-id="12212-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="12212-210">Probíhá asynchronní ověřování.</span><span class="sxs-lookup"><span data-stu-id="12212-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="12212-211">Ve výchozí Blazor šabloně projektu `App` Server komponenta *(App.razor)* ukazuje, jak nastavit vlastní obsah:</span><span class="sxs-lookup"><span data-stu-id="12212-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="12212-212">Obsah , `<NotFound>` `<NotAuthorized>`a `<Authorizing>` značky mohou obsahovat libovolné položky, například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="12212-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="12212-213">Pokud `<NotAuthorized>` prvek není zadán, `AuthorizeRouteView` použije následující záložní zprávu:</span><span class="sxs-lookup"><span data-stu-id="12212-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="12212-214">Oznámení o změnách stavu ověřování</span><span class="sxs-lookup"><span data-stu-id="12212-214">Notification about authentication state changes</span></span>

<span data-ttu-id="12212-215">Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například proto, že se uživatel odhlásil nebo jiný `NotifyAuthenticationStateChanged` uživatel `AuthenticationStateProvider` změnil své role), může [vlastní AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) volitelně vyvolat metodu v základní třídě.</span><span class="sxs-lookup"><span data-stu-id="12212-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="12212-216">To upozorní spotřebitele data stavu ověřování (například `AuthorizeView`) k rerender pomocí nových dat.</span><span class="sxs-lookup"><span data-stu-id="12212-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="12212-217">Procedurální logika</span><span class="sxs-lookup"><span data-stu-id="12212-217">Procedural logic</span></span>

<span data-ttu-id="12212-218">Pokud je aplikace povinna zkontrolovat autorizační pravidla jako součást procedurální `Task<AuthenticationState>` logiky, použijte <xref:System.Security.Claims.ClaimsPrincipal>kaskádový parametr typu k získání uživatele .</span><span class="sxs-lookup"><span data-stu-id="12212-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="12212-219">`Task<AuthenticationState>`lze kombinovat s dalšími službami, například `IAuthorizationService`k vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="12212-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="12212-220">V Blazor součásti aplikace WebAssembly `Microsoft.AspNetCore.Authorization` `Microsoft.AspNetCore.Components.Authorization` přidejte obory názvů a:</span><span class="sxs-lookup"><span data-stu-id="12212-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="12212-221">Tyto obory názvů lze poskytnout globálně jejich přidáním do souboru *_Imports.razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="12212-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="12212-222">Autorizace Blazor v aplikacích WebAssembly</span><span class="sxs-lookup"><span data-stu-id="12212-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="12212-223">V Blazor aplikacích WebAssembly lze kontroly autorizace obejít, protože všechny kódy na straně klienta mohou uživatelé upravovat.</span><span class="sxs-lookup"><span data-stu-id="12212-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="12212-224">Totéž platí pro všechny technologie aplikací na straně klienta, včetně javascriptových spa architektur nebo nativních aplikací pro jakýkoli operační systém.</span><span class="sxs-lookup"><span data-stu-id="12212-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="12212-225">**Vždy provádějte kontroly autorizace na serveru v rámci všech koncových bodů rozhraní API, ke kterým přistupuje aplikace na straně klienta.**</span><span class="sxs-lookup"><span data-stu-id="12212-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="12212-226">Další informace naleznete v <xref:security/blazor/webassembly/index>článcích v části .</span><span class="sxs-lookup"><span data-stu-id="12212-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="12212-227">Řešení chyb</span><span class="sxs-lookup"><span data-stu-id="12212-227">Troubleshoot errors</span></span>

<span data-ttu-id="12212-228">Běžné chyby:</span><span class="sxs-lookup"><span data-stu-id="12212-228">Common errors:</span></span>

* <span data-ttu-id="12212-229">**Autorizace vyžaduje kaskádový parametr\<typu Úloha AuthenticationState>. Zvažte použití CascadingAuthenticationState k zadání tohoto.**</span><span class="sxs-lookup"><span data-stu-id="12212-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="12212-230">**`null`hodnota je přijata pro`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="12212-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="12212-231">Je pravděpodobné, že projekt nebyl vytvořen Blazor pomocí šablony serveru s povoleným ověřováním.</span><span class="sxs-lookup"><span data-stu-id="12212-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="12212-232">Zabalte `<CascadingAuthenticationState>` přibližně některé části stromu ui, `App` například v komponentě (*App.razor*) takto:</span><span class="sxs-lookup"><span data-stu-id="12212-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="12212-233">Dodává `CascadingAuthenticationState` `Task<AuthenticationState>` kaskádový parametr, který zase obdrží z podkladové `AuthenticationStateProvider` služby DI.</span><span class="sxs-lookup"><span data-stu-id="12212-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12212-234">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="12212-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="12212-235">[Awesome: BlazorOvěřování](https://github.com/AdrienTorris/awesome-blazor#authentication) společenství ukázkové odkazy</span><span class="sxs-lookup"><span data-stu-id="12212-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
