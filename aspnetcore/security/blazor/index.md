---
title: ASP.NET Core ověřování a autorizace Blazor
author: guardrex
description: Přečtěte si o scénářích ověřování a autorizace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: security/blazor/index
ms.openlocfilehash: 51fb1f9984878fceee0b207d02a02622c3ba191d
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168347"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="ca30f-103">ASP.NET Core ověřování a autorizace Blazor</span><span class="sxs-lookup"><span data-stu-id="ca30f-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="ca30f-104">Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="ca30f-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ca30f-105">ASP.NET Core podporuje konfiguraci a správu zabezpečení v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="ca30f-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="ca30f-106">Scénáře zabezpečení se liší mezi Blazor serverem a Blazor aplikacemi WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="ca30f-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="ca30f-107">Vzhledem k tomu, že aplikace Blazor serveru běží na serveru, kontroly autorizace můžou určit:</span><span class="sxs-lookup"><span data-stu-id="ca30f-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="ca30f-108">Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou k dispozici uživateli).</span><span class="sxs-lookup"><span data-stu-id="ca30f-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="ca30f-109">Pravidla přístupu pro oblasti aplikace a součástí</span><span class="sxs-lookup"><span data-stu-id="ca30f-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="ca30f-110">Blazor aplikace WebAssembly běží na klientovi.</span><span class="sxs-lookup"><span data-stu-id="ca30f-110">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="ca30f-111">Autorizace se používá *jenom* k určení možností uživatelského rozhraní, které se mají zobrazit.</span><span class="sxs-lookup"><span data-stu-id="ca30f-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="ca30f-112">Vzhledem k tomu, že kontroly na straně klienta mohou být upraveny nebo vynechány uživatelem, aplikace Blazor WebAssembly nemůže vyhovět autorizačním pravidlům.</span><span class="sxs-lookup"><span data-stu-id="ca30f-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="ca30f-113">Ověřování</span><span class="sxs-lookup"><span data-stu-id="ca30f-113">Authentication</span></span>

<span data-ttu-id="ca30f-114">Blazor používá k vytvoření identity uživatele existující mechanismy ověřování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca30f-114">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="ca30f-115">Přesný mechanismus závisí na tom, jak je aplikace Blazor hostovaná, Blazor Server nebo Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="ca30f-115">The exact mechanism depends on how the Blazor app is hosted, Blazor Server or Blazor WebAssembly.</span></span>

### <a name="blazor-server-authentication"></a><span data-ttu-id="ca30f-116">Ověřování serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="ca30f-116">Blazor Server authentication</span></span>

<span data-ttu-id="ca30f-117">Blazor serverové aplikace pracují přes připojení v reálném čase, které je vytvořené pomocí signálu.</span><span class="sxs-lookup"><span data-stu-id="ca30f-117">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="ca30f-118">[Ověřování v aplikacích založených na službě Signal](xref:signalr/authn-and-authz) se zpracovává při navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="ca30f-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="ca30f-119">Ověřování může být založené na souboru cookie nebo nějakém jiném nosným tokenu.</span><span class="sxs-lookup"><span data-stu-id="ca30f-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="ca30f-120">Šablona projektu serveru Blazor může pro vás nastavit ověřování při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="ca30f-120">The Blazor Server project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ca30f-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca30f-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ca30f-122">Pokud chcete vytvořit nový projekt serveru Blazor <xref:blazor/get-started> s ověřovacím mechanismem, postupujte podle pokynů pro Visual Studio v článku.</span><span class="sxs-lookup"><span data-stu-id="ca30f-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="ca30f-123">Po výběru šablony **aplikace Blazor serveru** v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="ca30f-123">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="ca30f-124">Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ca30f-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="ca30f-125">**Bez ověřování**</span><span class="sxs-lookup"><span data-stu-id="ca30f-125">**No Authentication**</span></span>
* <span data-ttu-id="ca30f-126">**Jednotlivé uživatelské účty** &ndash; Uživatelské účty je možné uložit:</span><span class="sxs-lookup"><span data-stu-id="ca30f-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="ca30f-127">V aplikaci, která používá systém [identit](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca30f-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="ca30f-128">S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="ca30f-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="ca30f-129">**Pracovní nebo školní účty**</span><span class="sxs-lookup"><span data-stu-id="ca30f-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="ca30f-130">**Ověřování systému Windows**</span><span class="sxs-lookup"><span data-stu-id="ca30f-130">**Windows Authentication**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ca30f-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ca30f-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ca30f-132">Postupujte podle pokynů Visual Studio Code v <xref:blazor/get-started> článku Vytvoření nového projektu serveru Blazor s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="ca30f-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="ca30f-133">Přípustné hodnoty ověřování (`{AUTHENTICATION}`) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="ca30f-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="ca30f-134">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="ca30f-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="ca30f-135">`{AUTHENTICATION}`osa</span><span class="sxs-lookup"><span data-stu-id="ca30f-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="ca30f-136">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="ca30f-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="ca30f-137">Jednoho</span><span class="sxs-lookup"><span data-stu-id="ca30f-137">Individual</span></span><br><span data-ttu-id="ca30f-138">Uživatelé uložení v aplikaci pomocí ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="ca30f-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="ca30f-139">Jednoho</span><span class="sxs-lookup"><span data-stu-id="ca30f-139">Individual</span></span><br><span data-ttu-id="ca30f-140">Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="ca30f-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="ca30f-141">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="ca30f-141">Work or School Accounts</span></span><br><span data-ttu-id="ca30f-142">Ověřování organizace pro jednoho tenanta.</span><span class="sxs-lookup"><span data-stu-id="ca30f-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="ca30f-143">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="ca30f-143">Work or School Accounts</span></span><br><span data-ttu-id="ca30f-144">Ověřování organizace pro více tenantů.</span><span class="sxs-lookup"><span data-stu-id="ca30f-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="ca30f-145">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="ca30f-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="ca30f-146">Příkaz vytvoří složku s názvem s hodnotou zadanou pro `{APP NAME}` zástupný text a jako název aplikace použije název složky.</span><span class="sxs-lookup"><span data-stu-id="ca30f-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="ca30f-147">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new) v příručce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca30f-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.

| Authentication mechanism                                                                 | `{AUTHENTICATION}` value |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| No Authentication                                                                        | `None`                   |
| Individual<br>Users stored in the app with ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Work or School Accounts<br>Organizational authentication for a single tenant.            | `SingleOrg`              |
| Work or School Accounts<br>Organizational authentication for multiple tenants.           | `MultiOrg`               |
| Windows Authentication                                                                   | `Windows`                |

The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name. For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.

-->

---

### <a name="blazor-webassembly-authentication"></a><span data-ttu-id="ca30f-148">Blazor ověřování WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ca30f-148">Blazor WebAssembly authentication</span></span>

<span data-ttu-id="ca30f-149">V aplikacích Blazor WebAssembly lze kontroly ověřování obejít, protože všechny kódy na straně klienta mohou být změněny uživateli.</span><span class="sxs-lookup"><span data-stu-id="ca30f-149">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="ca30f-150">Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="ca30f-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="ca30f-151">Implementace vlastní `AuthenticationStateProvider` služby pro aplikace WebAssembly v Blazor je popsaná v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="ca30f-151">Implementation of a custom `AuthenticationStateProvider` service for Blazor WebAssembly apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="ca30f-152">Služba AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="ca30f-152">AuthenticationStateProvider service</span></span>

<span data-ttu-id="ca30f-153">Serverové aplikace Blazor obsahují integrovanou `AuthenticationStateProvider` službu, která získává data o stavu ověřování z `HttpContext.User`ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca30f-153">Blazor Server apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="ca30f-154">To je způsob, jakým se stav ověřování integruje s existujícími mechanismy ASP.NET Core ověřování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="ca30f-154">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="ca30f-155">`AuthenticationStateProvider`je základní službou, kterou `AuthorizeView` komponenta a `CascadingAuthenticationState` komponenta používá k získání stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="ca30f-155">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="ca30f-156">Obvykle přímo nepoužíváte `AuthenticationStateProvider` .</span><span class="sxs-lookup"><span data-stu-id="ca30f-156">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="ca30f-157">Použijte [komponentu AuthorizeView](#authorizeview-component) nebo přístupy [k<AuthenticationState> úkolům](#expose-the-authentication-state-as-a-cascading-parameter) , které jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="ca30f-157">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="ca30f-158">Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že součást není automaticky oznámena v případě, že dojde ke změně podkladových dat stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="ca30f-158">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="ca30f-159">Služba může poskytnout <xref:System.Security.Claims.ClaimsPrincipal> data aktuálního uživatele, jak je znázorněno v následujícím příkladu: `AuthenticationStateProvider`</span><span class="sxs-lookup"><span data-stu-id="ca30f-159">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```cshtml
@page "/"
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="@LogUsername">Write user info to console</button>

@code {
    private async Task LogUsername()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

<span data-ttu-id="ca30f-160">Pokud `user.Identity.IsAuthenticated` je `true` a<xref:System.Security.Claims.ClaimsPrincipal>vzhledem k tomu, že je uživatel a, mohou být deklarace identity vyhodnoceny a ve vyhodnocování členů.</span><span class="sxs-lookup"><span data-stu-id="ca30f-160">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="ca30f-161">Další informace o vkládání závislostí (di) a službách naleznete v <xref:blazor/dependency-injection> tématu <xref:fundamentals/dependency-injection>a.</span><span class="sxs-lookup"><span data-stu-id="ca30f-161">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="ca30f-162">Implementace vlastního AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="ca30f-162">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="ca30f-163">Pokud vytváříte aplikaci WebAssembly Blazor nebo pokud specifikace vaší aplikace naprosto vyžaduje vlastního poskytovatele, implementujte zprostředkovatele a přepište `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="ca30f-163">If you're building a Blazor WebAssembly app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

```csharp
class CustomAuthStateProvider : AuthenticationStateProvider
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

<span data-ttu-id="ca30f-164">Služba je registrovaná v `Startup.ConfigureServices`: `CustomAuthStateProvider`</span><span class="sxs-lookup"><span data-stu-id="ca30f-164">The `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
}
```

<span data-ttu-id="ca30f-165">Pomocí nástroje se všechny uživatele ověřují pomocí uživatelského jména `mrfibuli`. `CustomAuthStateProvider`</span><span class="sxs-lookup"><span data-stu-id="ca30f-165">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="ca30f-166">Zveřejnit stav ověřování jako kaskádový parametr</span><span class="sxs-lookup"><span data-stu-id="ca30f-166">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="ca30f-167">Pokud jsou v procedurální logice požadovány údaje o stavu ověřování, například při provádění akce aktivované uživatelem, Získejte údaje o stavu ověřování definováním kaskádového parametru typu `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="ca30f-167">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```cshtml
@page "/"

<button @onclick="@LogUsername">Log username</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

<span data-ttu-id="ca30f-168">V takovém případě `user.Identity.IsAuthenticated` můžou být deklarace identity výčtové a ve vyhodnocených rolích. `true`</span><span class="sxs-lookup"><span data-stu-id="ca30f-168">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="ca30f-169">Nastavte kaskádový parametr `AuthorizeRouteView` pomocí komponent a `CascadingAuthenticationState`: `Task<AuthenticationState>`</span><span class="sxs-lookup"><span data-stu-id="ca30f-169">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components:</span></span>

```cshtml
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

## <a name="authorization"></a><span data-ttu-id="ca30f-170">Autorizace</span><span class="sxs-lookup"><span data-stu-id="ca30f-170">Authorization</span></span>

<span data-ttu-id="ca30f-171">Po ověření uživatele se uplatní *autorizační* pravidla, která řídí, co může uživatel dělat.</span><span class="sxs-lookup"><span data-stu-id="ca30f-171">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="ca30f-172">Přístup je obvykle udělen nebo odepřen na základě toho, zda:</span><span class="sxs-lookup"><span data-stu-id="ca30f-172">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="ca30f-173">Uživatel je ověřený (přihlášený).</span><span class="sxs-lookup"><span data-stu-id="ca30f-173">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="ca30f-174">Uživatel je v *roli*.</span><span class="sxs-lookup"><span data-stu-id="ca30f-174">A user is in a *role*.</span></span>
* <span data-ttu-id="ca30f-175">Uživatel má *deklaraci identity*.</span><span class="sxs-lookup"><span data-stu-id="ca30f-175">A user has a *claim*.</span></span>
* <span data-ttu-id="ca30f-176">Byla splněna *zásada* .</span><span class="sxs-lookup"><span data-stu-id="ca30f-176">A *policy* is satisfied.</span></span>

<span data-ttu-id="ca30f-177">Každá z těchto konceptů je stejná jako u ASP.NET Core MVC nebo Razor Pages aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca30f-177">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="ca30f-178">Další informace o ASP.NET Core zabezpečení najdete v článcích [ASP.NET Core Security and identity](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="ca30f-178">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="ca30f-179">Komponenta AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="ca30f-179">AuthorizeView component</span></span>

<span data-ttu-id="ca30f-180">`AuthorizeView` Komponenta selektivně zobrazuje uživatelské rozhraní v závislosti na tom, zda je uživatel autorizován pro jeho zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ca30f-180">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="ca30f-181">Tento přístup je užitečný, když potřebujete jenom *Zobrazit* data pro uživatele a nemusíte používat identitu uživatele v procedurální logice.</span><span class="sxs-lookup"><span data-stu-id="ca30f-181">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="ca30f-182">Komponenta zveřejňuje `context` proměnnou typu `AuthenticationState`, kterou můžete použít pro přístup k informacím o přihlášeném uživateli:</span><span class="sxs-lookup"><span data-stu-id="ca30f-182">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="ca30f-183">V případě, že se uživatel neověřuje, můžete také Dodejte jiný obsah, který se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="ca30f-183">You can also supply different content for display if the user isn't authenticated:</span></span>

```cshtml
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

<span data-ttu-id="ca30f-184">Obsah `<Authorized>` značek a `<NotAuthorized>` může obsahovat libovolné položky, jako jsou například jiné interaktivní komponenty.</span><span class="sxs-lookup"><span data-stu-id="ca30f-184">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="ca30f-185">Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou uvedené v části [autorizace](#authorization) .</span><span class="sxs-lookup"><span data-stu-id="ca30f-185">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="ca30f-186">Pokud nejsou zadané autorizační podmínky, `AuthorizeView` použije se výchozí zásada a bude se považovat za:</span><span class="sxs-lookup"><span data-stu-id="ca30f-186">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="ca30f-187">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="ca30f-187">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="ca30f-188">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="ca30f-188">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="ca30f-189">Ověřování na základě rolí a na základě zásad</span><span class="sxs-lookup"><span data-stu-id="ca30f-189">Role-based and policy-based authorization</span></span>

<span data-ttu-id="ca30f-190">Komponenta podporuje autorizaci na základě rolí nebo *na základě zásad* . `AuthorizeView`</span><span class="sxs-lookup"><span data-stu-id="ca30f-190">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="ca30f-191">Pro autorizaci založenou na rolích `Roles` použijte parametr:</span><span class="sxs-lookup"><span data-stu-id="ca30f-191">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="ca30f-192">Další informace naleznete v tématu <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="ca30f-192">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="ca30f-193">Pro autorizaci založenou na zásadách použijte `Policy` parametr:</span><span class="sxs-lookup"><span data-stu-id="ca30f-193">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="ca30f-194">Ověřování na základě deklarací identity je zvláštní případ ověřování na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="ca30f-194">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="ca30f-195">Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="ca30f-195">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="ca30f-196">Další informace naleznete v tématu <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="ca30f-196">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="ca30f-197">Tato rozhraní API se dají použít buď v Blazor serveru, nebo v aplikacích Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="ca30f-197">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="ca30f-198">Pokud není zadán `Policy` ani `AuthorizeView` ani, používá výchozí zásady. `Roles`</span><span class="sxs-lookup"><span data-stu-id="ca30f-198">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="ca30f-199">Obsah zobrazený během asynchronního ověřování</span><span class="sxs-lookup"><span data-stu-id="ca30f-199">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="ca30f-200">Blazor umožňuje *asynchronní*určení stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="ca30f-200">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="ca30f-201">Primární scénář pro tento přístup je v aplikacích Blazor WebAssembly, které vytvářejí požadavek na externí koncový bod pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="ca30f-201">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="ca30f-202">V průběhu ověřování ve `AuthorizeView` výchozím nastavení nezobrazí žádný obsah.</span><span class="sxs-lookup"><span data-stu-id="ca30f-202">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="ca30f-203">Chcete-li zobrazit obsah, když dojde k `<Authorizing>` ověřování, použijte element:</span><span class="sxs-lookup"><span data-stu-id="ca30f-203">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```cshtml
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

<span data-ttu-id="ca30f-204">Tento přístup se obvykle nevztahuje na aplikace serveru Blazor.</span><span class="sxs-lookup"><span data-stu-id="ca30f-204">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="ca30f-205">Aplikace Blazor serveru znají stav ověřování, jakmile se naváže stav.</span><span class="sxs-lookup"><span data-stu-id="ca30f-205">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="ca30f-206">`Authorizing`obsah může být k dispozici v `AuthorizeView` součásti aplikace serveru Blazor, ale obsah se nikdy nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="ca30f-206">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="ca30f-207">[Autorizační] – atribut</span><span class="sxs-lookup"><span data-stu-id="ca30f-207">[Authorize] attribute</span></span>

<span data-ttu-id="ca30f-208">Stejně jako aplikace může `[Authorize]` být použita s řadičem MVC nebo stránkou Razor, `[Authorize]` lze také použít s komponentami Razor:</span><span class="sxs-lookup"><span data-stu-id="ca30f-208">Just like an app can use `[Authorize]` with an MVC controller or Razor page, `[Authorize]` can also be used with Razor Components:</span></span>

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="ca30f-209">Používejte `[Authorize]` pouze v `@page` součástech, které jsou dostupné prostřednictvím směrovače Blazor.</span><span class="sxs-lookup"><span data-stu-id="ca30f-209">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="ca30f-210">Autorizace se provádí jenom jako aspekt směrování, a *ne* pro podřízené komponenty vygenerované v rámci stránky.</span><span class="sxs-lookup"><span data-stu-id="ca30f-210">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="ca30f-211">Chcete-li autorizovat zobrazení určitých částí v rámci stránky, použijte `AuthorizeView` místo toho.</span><span class="sxs-lookup"><span data-stu-id="ca30f-211">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="ca30f-212">Aby mohla být komponenta zkompilována, může být nutné přidat `@using Microsoft.AspNetCore.Authorization` buď do součásti, nebo do souboru *_Imports. Razor* .</span><span class="sxs-lookup"><span data-stu-id="ca30f-212">You may need to add `@using Microsoft.AspNetCore.Authorization` either to the component or to the *_Imports.razor* file in order for the component to compile.</span></span>

<span data-ttu-id="ca30f-213">`[Authorize]` Atribut také podporuje ověřování na základě rolí nebo na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="ca30f-213">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="ca30f-214">Pro autorizaci založenou na rolích `Roles` použijte parametr:</span><span class="sxs-lookup"><span data-stu-id="ca30f-214">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="ca30f-215">Pro autorizaci založenou na zásadách použijte `Policy` parametr:</span><span class="sxs-lookup"><span data-stu-id="ca30f-215">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="ca30f-216">Pokud není zadán `Policy` ani `[Authorize]` ani, používá výchozí zásadu, která ve výchozím nastavení zachází: `Roles`</span><span class="sxs-lookup"><span data-stu-id="ca30f-216">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="ca30f-217">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="ca30f-217">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="ca30f-218">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="ca30f-218">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="ca30f-219">Přizpůsobení neoprávněného obsahu pomocí součásti směrovače</span><span class="sxs-lookup"><span data-stu-id="ca30f-219">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="ca30f-220">`Router` Komponenta společně `AuthorizeRouteView` s komponentou umožňuje aplikaci zadat vlastní obsah, pokud:</span><span class="sxs-lookup"><span data-stu-id="ca30f-220">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="ca30f-221">Obsah nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="ca30f-221">Content isn't found.</span></span>
* <span data-ttu-id="ca30f-222">Uživatel nezdařil `[Authorize]` podmínku použitou pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="ca30f-222">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="ca30f-223">Atribut je popsán v části [[autorizační] atributu.](#authorize-attribute) `[Authorize]`</span><span class="sxs-lookup"><span data-stu-id="ca30f-223">The `[Authorize]` attribute is covered in the [[Authorize] attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="ca30f-224">Probíhá asynchronní ověřování.</span><span class="sxs-lookup"><span data-stu-id="ca30f-224">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="ca30f-225">Ve výchozí šabloně projektu serveru Blazor soubor *App. Razor* ukazuje, jak nastavit vlastní obsah:</span><span class="sxs-lookup"><span data-stu-id="ca30f-225">In the default Blazor Server project template, the *App.razor* file demonstrates how to set custom content:</span></span>

```cshtml
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

<span data-ttu-id="ca30f-226">Obsah `<NotFound>`značek, `<NotAuthorized>`a `<Authorizing>` může obsahovat libovolné položky, jako jsou například jiné interaktivní komponenty.</span><span class="sxs-lookup"><span data-stu-id="ca30f-226">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="ca30f-227">Pokud není `AuthorizeRouteView` element zadán, používá následující záložní zprávu: `<NotAuthorized>`</span><span class="sxs-lookup"><span data-stu-id="ca30f-227">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="ca30f-228">Oznámení o změnách stavu ověřování</span><span class="sxs-lookup"><span data-stu-id="ca30f-228">Notification about authentication state changes</span></span>

<span data-ttu-id="ca30f-229">Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například kvůli tomu, že se uživatel odhlásil nebo jiný uživatel změnil své role), vlastní `AuthenticationStateProvider` možnost může volitelně vyvolat metodu `NotifyAuthenticationStateChanged` na `AuthenticationStateProvider` bázi Base. Deník.</span><span class="sxs-lookup"><span data-stu-id="ca30f-229">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="ca30f-230">Tím se uživatele upozorní na data stavu ověřování (například `AuthorizeView`) k opakovanému vykreslení pomocí nových dat.</span><span class="sxs-lookup"><span data-stu-id="ca30f-230">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="ca30f-231">Procesní logika</span><span class="sxs-lookup"><span data-stu-id="ca30f-231">Procedural logic</span></span>

<span data-ttu-id="ca30f-232">Pokud je aplikace nutná k kontrole autorizačních pravidel v rámci procedurální logiky, použijte k získání `Task<AuthenticationState>` <xref:System.Security.Claims.ClaimsPrincipal>uživatele kaskádový parametr typu.</span><span class="sxs-lookup"><span data-stu-id="ca30f-232">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="ca30f-233">`Task<AuthenticationState>`lze kombinovat s jinými službami, například `IAuthorizationService`, k vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="ca30f-233">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```cshtml
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

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="ca30f-234">Autorizace v Blazorch aplikacích WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ca30f-234">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="ca30f-235">V aplikacích Blazor WebAssembly lze kontroly autorizace obejít, protože všechny kódy na straně klienta mohou být změněny uživateli.</span><span class="sxs-lookup"><span data-stu-id="ca30f-235">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="ca30f-236">Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="ca30f-236">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="ca30f-237">**Na serveru vždy provádějte kontroly autorizace v libovolném koncovém bodu rozhraní API, ke kterému přistupovala aplikace na straně klienta.**</span><span class="sxs-lookup"><span data-stu-id="ca30f-237">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="ca30f-238">Řešení chyb</span><span class="sxs-lookup"><span data-stu-id="ca30f-238">Troubleshoot errors</span></span>

<span data-ttu-id="ca30f-239">Běžné chyby:</span><span class="sxs-lookup"><span data-stu-id="ca30f-239">Common errors:</span></span>

* <span data-ttu-id="ca30f-240">**Autorizace vyžaduje kaskádový parametr typu Task\<AuthenticationState >. Zvažte použití CascadingAuthenticationState k zadání.**</span><span class="sxs-lookup"><span data-stu-id="ca30f-240">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="ca30f-241">**`null`hodnota je přijata pro`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="ca30f-241">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="ca30f-242">Je možné, že projekt nebyl vytvořen pomocí šablony serveru Blazor s povoleným ověřováním.</span><span class="sxs-lookup"><span data-stu-id="ca30f-242">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="ca30f-243">Zabalte kolem některé části stromu uživatelského rozhraní, například v *App. Razor* , následovně: `<CascadingAuthenticationState>`</span><span class="sxs-lookup"><span data-stu-id="ca30f-243">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="ca30f-244">Doplní kaskádový parametr, který zase získá z podkladové `AuthenticationStateProvider` služby di. `Task<AuthenticationState>` `CascadingAuthenticationState`</span><span class="sxs-lookup"><span data-stu-id="ca30f-244">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca30f-245">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ca30f-245">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
