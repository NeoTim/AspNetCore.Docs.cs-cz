---
title: ASP.NET Core ověřování a autorizace Blazor
author: guardrex
description: Přečtěte si o scénářích ověřování a autorizace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/29/2019
uid: security/blazor/index
ms.openlocfilehash: 8714acbeb6e8a00992a601030811b24f53426b82
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310524"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="6d7d0-103">ASP.NET Core ověřování a autorizace Blazor</span><span class="sxs-lookup"><span data-stu-id="6d7d0-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="6d7d0-104">Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="6d7d0-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="6d7d0-105">ASP.NET Core podporuje konfiguraci a správu zabezpečení v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="6d7d0-106">Scénáře zabezpečení se liší mezi Blazor a klientskými aplikacemi na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-106">Security scenarios differ between Blazor server-side and client-side apps.</span></span> <span data-ttu-id="6d7d0-107">Vzhledem k tomu, že Blazor aplikace na straně serveru běží na serveru, kontroly autorizace můžou určit:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-107">Because Blazor server-side apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="6d7d0-108">Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou k dispozici uživateli).</span><span class="sxs-lookup"><span data-stu-id="6d7d0-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="6d7d0-109">Pravidla přístupu pro oblasti aplikace a součástí</span><span class="sxs-lookup"><span data-stu-id="6d7d0-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="6d7d0-110">Blazor aplikace na straně klienta běží na klientovi.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-110">Blazor client-side apps run on the client.</span></span> <span data-ttu-id="6d7d0-111">Autorizace se používá *jenom* k určení možností uživatelského rozhraní, které se mají zobrazit.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="6d7d0-112">Vzhledem k tomu, že kontroly na straně klienta mohou být upraveny nebo vynechány uživatelem, aplikace Blazor na straně klienta nemůže vyhovět autorizačním pravidlům.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-112">Since client-side checks can be modified or bypassed by a user, a Blazor client-side app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="6d7d0-113">Ověřování</span><span class="sxs-lookup"><span data-stu-id="6d7d0-113">Authentication</span></span>

<span data-ttu-id="6d7d0-114">Blazor používá k vytvoření identity uživatele existující mechanismy ověřování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-114">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="6d7d0-115">Přesný mechanismus závisí na tom, jak je aplikace Blazor hostována, na straně serveru nebo na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-115">The exact mechanism depends on how the Blazor app is hosted, server-side or client-side.</span></span>

### <a name="blazor-server-side-authentication"></a><span data-ttu-id="6d7d0-116">Blazor ověřování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="6d7d0-116">Blazor server-side authentication</span></span>

<span data-ttu-id="6d7d0-117">Blazor aplikace na straně serveru fungují prostřednictvím připojení v reálném čase vytvořeného pomocí signálu.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-117">Blazor server-side apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="6d7d0-118">[Ověřování v aplikacích založených na službě Signal](xref:signalr/authn-and-authz) se zpracovává při navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="6d7d0-119">Ověřování může být založené na souboru cookie nebo nějakém jiném nosným tokenu.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="6d7d0-120">Šablona projektu Blazor na straně serveru může nastavit ověřování při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-120">The Blazor server-side project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6d7d0-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6d7d0-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6d7d0-122">Podle pokynů pro Visual Studio v <xref:blazor/get-started> článku vytvořte nový projekt Blazor na straně serveru s mechanismem ověřování.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism.</span></span>

<span data-ttu-id="6d7d0-123">Po výběru šablony **aplikace Blazor serveru** v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="6d7d0-123">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="6d7d0-124">Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="6d7d0-125">**Bez ověřování**</span><span class="sxs-lookup"><span data-stu-id="6d7d0-125">**No Authentication**</span></span>
* <span data-ttu-id="6d7d0-126">**Jednotlivé uživatelské účty** &ndash; Uživatelské účty je možné uložit:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="6d7d0-127">V aplikaci, která používá systém [identit](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="6d7d0-128">S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="6d7d0-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="6d7d0-129">**Pracovní nebo školní účty**</span><span class="sxs-lookup"><span data-stu-id="6d7d0-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="6d7d0-130">**Ověřování systému Windows**</span><span class="sxs-lookup"><span data-stu-id="6d7d0-130">**Windows Authentication**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6d7d0-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6d7d0-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6d7d0-132">Postupujte podle pokynů Visual Studio Code v <xref:blazor/get-started> článku Vytvoření nového projektu na straně serveru Blazor pomocí mechanismu ověřování:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism:</span></span>

```console
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="6d7d0-133">Přípustné hodnoty ověřování (`{AUTHENTICATION}`) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="6d7d0-134">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="6d7d0-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="6d7d0-135">`{AUTHENTICATION}`osa</span><span class="sxs-lookup"><span data-stu-id="6d7d0-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="6d7d0-136">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="6d7d0-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="6d7d0-137">Jednoho</span><span class="sxs-lookup"><span data-stu-id="6d7d0-137">Individual</span></span><br><span data-ttu-id="6d7d0-138">Uživatelé uložení v aplikaci pomocí ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="6d7d0-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="6d7d0-139">Jednoho</span><span class="sxs-lookup"><span data-stu-id="6d7d0-139">Individual</span></span><br><span data-ttu-id="6d7d0-140">Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="6d7d0-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="6d7d0-141">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="6d7d0-141">Work or School Accounts</span></span><br><span data-ttu-id="6d7d0-142">Ověřování organizace pro jednoho tenanta.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="6d7d0-143">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="6d7d0-143">Work or School Accounts</span></span><br><span data-ttu-id="6d7d0-144">Ověřování organizace pro více tenantů.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="6d7d0-145">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="6d7d0-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="6d7d0-146">Příkaz vytvoří složku s názvem s hodnotou zadanou pro `{APP NAME}` zástupný text a jako název aplikace použije název složky.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="6d7d0-147">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new) v příručce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism:

```console
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

### <a name="blazor-client-side-authentication"></a><span data-ttu-id="6d7d0-148">Blazor ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="6d7d0-148">Blazor client-side authentication</span></span>

<span data-ttu-id="6d7d0-149">V Blazor aplikací na straně klienta se můžou kontroly ověřování obejít, protože všichni můžou upravovat kód na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-149">In Blazor client-side apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="6d7d0-150">Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="6d7d0-151">Implementace vlastní `AuthenticationStateProvider` služby pro Blazor aplikace na straně klienta je popsaná v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-151">Implementation of a custom `AuthenticationStateProvider` service for Blazor client-side apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="6d7d0-152">Služba AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="6d7d0-152">AuthenticationStateProvider service</span></span>

<span data-ttu-id="6d7d0-153">Blazor aplikace na straně serveru obsahují integrovanou `AuthenticationStateProvider` službu, která získává data o stavu ověřování z `HttpContext.User`ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-153">Blazor server-side apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="6d7d0-154">To je způsob, jakým se stav ověřování integruje s existujícími mechanismy ASP.NET Core ověřování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-154">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="6d7d0-155">`AuthenticationStateProvider`je základní službou, kterou `AuthorizeView` komponenta a `CascadingAuthenticationState` komponenta používá k získání stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-155">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="6d7d0-156">Obvykle přímo nepoužíváte `AuthenticationStateProvider` .</span><span class="sxs-lookup"><span data-stu-id="6d7d0-156">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="6d7d0-157">Použijte [komponentu AuthorizeView](#authorizeview-component) nebo přístupy [k<AuthenticationState> úkolům](#expose-the-authentication-state-as-a-cascading-parameter) , které jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-157">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="6d7d0-158">Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že součást není automaticky oznámena v případě, že dojde ke změně podkladových dat stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-158">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="6d7d0-159">Služba může poskytnout <xref:System.Security.Claims.ClaimsPrincipal> data aktuálního uživatele, jak je znázorněno v následujícím příkladu: `AuthenticationStateProvider`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-159">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="6d7d0-160">Pokud `user.Identity.IsAuthenticated` je `true` a<xref:System.Security.Claims.ClaimsPrincipal>vzhledem k tomu, že je uživatel a, mohou být deklarace identity vyhodnoceny a ve vyhodnocování členů.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-160">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="6d7d0-161">Další informace o vkládání závislostí (di) a službách naleznete v <xref:blazor/dependency-injection> tématu <xref:fundamentals/dependency-injection>a.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-161">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="6d7d0-162">Implementace vlastního AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="6d7d0-162">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="6d7d0-163">Pokud vytváříte aplikaci Blazor na straně klienta nebo pokud specifikace vaší aplikace naprosto vyžaduje vlastního poskytovatele, implementujte zprostředkovatele a přepište `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-163">If you're building a Blazor client-side app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="6d7d0-164">Služba je registrovaná v `Startup.ConfigureServices`: `CustomAuthStateProvider`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-164">The `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
}
```

<span data-ttu-id="6d7d0-165">Pomocí nástroje se všechny uživatele ověřují pomocí uživatelského jména `mrfibuli`. `CustomAuthStateProvider`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-165">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="6d7d0-166">Zveřejnit stav ověřování jako kaskádový parametr</span><span class="sxs-lookup"><span data-stu-id="6d7d0-166">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="6d7d0-167">Pokud jsou v procedurální logice požadovány údaje o stavu ověřování, například při provádění akce aktivované uživatelem, Získejte údaje o stavu ověřování definováním kaskádového parametru typu `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-167">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

<span data-ttu-id="6d7d0-168">V takovém případě `user.Identity.IsAuthenticated` můžou být deklarace identity výčtové a ve vyhodnocených rolích. `true`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-168">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="6d7d0-169">Nastavte kaskádový parametr `AuthorizeRouteView` pomocí komponent a `CascadingAuthenticationState`: `Task<AuthenticationState>`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-169">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components:</span></span>

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

## <a name="authorization"></a><span data-ttu-id="6d7d0-170">Autorizace</span><span class="sxs-lookup"><span data-stu-id="6d7d0-170">Authorization</span></span>

<span data-ttu-id="6d7d0-171">Po ověření uživatele se uplatní *autorizační* pravidla, která řídí, co může uživatel dělat.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-171">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="6d7d0-172">Přístup je obvykle udělen nebo odepřen na základě toho, zda:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-172">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="6d7d0-173">Uživatel je ověřený (přihlášený).</span><span class="sxs-lookup"><span data-stu-id="6d7d0-173">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="6d7d0-174">Uživatel je v *roli*.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-174">A user is in a *role*.</span></span>
* <span data-ttu-id="6d7d0-175">Uživatel má *deklaraci identity*.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-175">A user has a *claim*.</span></span>
* <span data-ttu-id="6d7d0-176">Byla splněna *zásada* .</span><span class="sxs-lookup"><span data-stu-id="6d7d0-176">A *policy* is satisfied.</span></span>

<span data-ttu-id="6d7d0-177">Každá z těchto konceptů je stejná jako u ASP.NET Core MVC nebo Razor Pages aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-177">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="6d7d0-178">Další informace o ASP.NET Core zabezpečení najdete v článcích [ASP.NET Core Security and identity](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="6d7d0-178">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="6d7d0-179">Komponenta AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="6d7d0-179">AuthorizeView component</span></span>

<span data-ttu-id="6d7d0-180">`AuthorizeView` Komponenta selektivně zobrazuje uživatelské rozhraní v závislosti na tom, zda je uživatel autorizován pro jeho zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-180">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="6d7d0-181">Tento přístup je užitečný, když potřebujete jenom *Zobrazit* data pro uživatele a nemusíte používat identitu uživatele v procedurální logice.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-181">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="6d7d0-182">Komponenta zveřejňuje `context` proměnnou typu `AuthenticationState`, kterou můžete použít pro přístup k informacím o přihlášeném uživateli:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-182">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="6d7d0-183">V případě, že se uživatel neověřuje, můžete také Dodejte jiný obsah, který se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-183">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="6d7d0-184">Obsah `<Authorized>` a`<NotAuthorized>` může zahrnovat libovolné položky, jako jsou například jiné interaktivní komponenty.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-184">The content of `<Authorized>` and `<NotAuthorized>` can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="6d7d0-185">Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou uvedené v části [autorizace](#authorization) .</span><span class="sxs-lookup"><span data-stu-id="6d7d0-185">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="6d7d0-186">Pokud nejsou zadané autorizační podmínky, `AuthorizeView` použije se výchozí zásada a bude se považovat za:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-186">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="6d7d0-187">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-187">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="6d7d0-188">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-188">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="6d7d0-189">Ověřování na základě rolí a na základě zásad</span><span class="sxs-lookup"><span data-stu-id="6d7d0-189">Role-based and policy-based authorization</span></span>

<span data-ttu-id="6d7d0-190">Komponenta podporuje autorizaci na základě rolí nebo *na základě zásad* . `AuthorizeView`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-190">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="6d7d0-191">Pro autorizaci založenou na rolích `Roles` použijte parametr:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-191">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="6d7d0-192">Další informace naleznete v tématu <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-192">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="6d7d0-193">Pro autorizaci založenou na zásadách použijte `Policy` parametr:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-193">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="6d7d0-194">Ověřování na základě deklarací identity je zvláštní případ ověřování na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-194">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="6d7d0-195">Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-195">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="6d7d0-196">Další informace naleznete v tématu <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-196">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="6d7d0-197">Tato rozhraní API se dají použít buď v Blazor na straně serveru, nebo v Blazor klientských aplikacích.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-197">These APIs can be used in either Blazor server-side or Blazor client-side apps.</span></span>

<span data-ttu-id="6d7d0-198">Pokud není zadán `Policy` ani `AuthorizeView` ani, používá výchozí zásady. `Roles`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-198">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="6d7d0-199">Obsah zobrazený během asynchronního ověřování</span><span class="sxs-lookup"><span data-stu-id="6d7d0-199">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="6d7d0-200">Blazor umožňuje *asynchronní*určení stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-200">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="6d7d0-201">Primární scénář pro tento přístup je v Blazor aplikací na straně klienta, které vytvářejí požadavek na externí koncový bod pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-201">The primary scenario for this approach is in Blazor client-side apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="6d7d0-202">V průběhu ověřování ve `AuthorizeView` výchozím nastavení nezobrazí žádný obsah.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-202">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="6d7d0-203">Chcete-li zobrazit obsah, když dojde k `<Authorizing>` ověřování, použijte element:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-203">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="6d7d0-204">Tento přístup se obvykle nevztahuje na Blazor aplikace na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-204">This approach isn't normally applicable to Blazor server-side apps.</span></span> <span data-ttu-id="6d7d0-205">Blazor aplikace na straně serveru znají stav ověřování, jakmile se naváže stav.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-205">Blazor server-side apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="6d7d0-206">`Authorizing`obsah může být k dispozici v `AuthorizeView` součásti aplikace na straně serveru Blazor, ale obsah se nikdy nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-206">`Authorizing` content can be provided in a Blazor server-side app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="6d7d0-207">[Autorizační] – atribut</span><span class="sxs-lookup"><span data-stu-id="6d7d0-207">[Authorize] attribute</span></span>

<span data-ttu-id="6d7d0-208">Stejně jako aplikace může `[Authorize]` být použita s řadičem MVC nebo stránkou Razor, `[Authorize]` lze také použít s komponentami Razor:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-208">Just like an app can use `[Authorize]` with an MVC controller or Razor page, `[Authorize]` can also be used with Razor Components:</span></span>

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="6d7d0-209">Používejte `[Authorize]` pouze v `@page` součástech, které jsou dostupné prostřednictvím směrovače Blazor.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-209">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="6d7d0-210">Autorizace se provádí jenom jako aspekt směrování, a *ne* pro podřízené komponenty vygenerované v rámci stránky.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-210">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="6d7d0-211">Chcete-li autorizovat zobrazení určitých částí v rámci stránky, použijte `AuthorizeView` místo toho.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-211">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="6d7d0-212">Aby mohla být komponenta zkompilována, může být nutné přidat `@using Microsoft.AspNetCore.Authorization` buď do součásti, nebo do souboru *_Imports. Razor* .</span><span class="sxs-lookup"><span data-stu-id="6d7d0-212">You may need to add `@using Microsoft.AspNetCore.Authorization` either to the component or to the *_Imports.razor* file in order for the component to compile.</span></span>

<span data-ttu-id="6d7d0-213">`[Authorize]` Atribut také podporuje ověřování na základě rolí nebo na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-213">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="6d7d0-214">Pro autorizaci založenou na rolích `Roles` použijte parametr:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-214">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="6d7d0-215">Pro autorizaci založenou na zásadách použijte `Policy` parametr:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-215">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="6d7d0-216">Pokud není zadán `Policy` ani `[Authorize]` ani, používá výchozí zásadu, která ve výchozím nastavení zachází: `Roles`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-216">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="6d7d0-217">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-217">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="6d7d0-218">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-218">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="6d7d0-219">Přizpůsobení neoprávněného obsahu pomocí součásti směrovače</span><span class="sxs-lookup"><span data-stu-id="6d7d0-219">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="6d7d0-220">`Router` Komponenta společně `AuthorizeRouteView` s komponentou umožňuje aplikaci zadat vlastní obsah, pokud:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-220">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="6d7d0-221">Obsah nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-221">Content isn't found.</span></span>
* <span data-ttu-id="6d7d0-222">Uživatel nezdařil `[Authorize]` podmínku použitou pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-222">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="6d7d0-223">Atribut je popsán v části [[autorizační] atributu.](#authorize-attribute) `[Authorize]`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-223">The `[Authorize]` attribute is covered in the [[Authorize] attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="6d7d0-224">Probíhá asynchronní ověřování.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-224">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="6d7d0-225">Ve výchozí šabloně projektu na straně serveru Blazor soubor *App. Razor* ukazuje, jak nastavit vlastní obsah:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-225">In the default Blazor server-side project template, the *App.razor* file demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="6d7d0-226">Obsah `<NotFound>`, `<NotAuthorized>`a můžezahrnovatlibovolnépoložky,jakojsounapříkladjinéinteraktivníkomponenty.`<Authorizing>`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-226">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="6d7d0-227">Pokud `<NotAuthorized>` není zadaný `<AuthorizeRouteView>` , použije se tato záložní zpráva:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-227">If `<NotAuthorized>` isn't specified, the `<AuthorizeRouteView>` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="6d7d0-228">Oznámení o změnách stavu ověřování</span><span class="sxs-lookup"><span data-stu-id="6d7d0-228">Notification about authentication state changes</span></span>

<span data-ttu-id="6d7d0-229">Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například kvůli tomu, že se uživatel odhlásil nebo jiný uživatel změnil své role), vlastní `AuthenticationStateProvider` možnost může volitelně vyvolat metodu `NotifyAuthenticationStateChanged` na `AuthenticationStateProvider` bázi Base. Deník.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-229">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="6d7d0-230">Tím se uživatele upozorní na data stavu ověřování (například `AuthorizeView`) k opakovanému vykreslení pomocí nových dat.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-230">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="6d7d0-231">Procesní logika</span><span class="sxs-lookup"><span data-stu-id="6d7d0-231">Procedural logic</span></span>

<span data-ttu-id="6d7d0-232">Pokud je aplikace nutná k kontrole autorizačních pravidel v rámci procedurální logiky, použijte k získání `Task<AuthenticationState>` <xref:System.Security.Claims.ClaimsPrincipal>uživatele kaskádový parametr typu.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-232">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="6d7d0-233">`Task<AuthenticationState>`lze kombinovat s jinými službami, například `IAuthorizationService`, k vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-233">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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

## <a name="authorization-in-blazor-client-side-apps"></a><span data-ttu-id="6d7d0-234">Autorizace v Blazor klientských aplikacích na straně klienta</span><span class="sxs-lookup"><span data-stu-id="6d7d0-234">Authorization in Blazor client-side apps</span></span>

<span data-ttu-id="6d7d0-235">V aplikacích Blazor na straně klienta se můžou kontroly autorizace obejít, protože všichni můžou upravovat kód na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-235">In Blazor client-side apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="6d7d0-236">Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-236">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="6d7d0-237">**Na serveru vždy provádějte kontroly autorizace v libovolném koncovém bodu rozhraní API, ke kterému přistupovala aplikace na straně klienta.**</span><span class="sxs-lookup"><span data-stu-id="6d7d0-237">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="6d7d0-238">Řešení chyb</span><span class="sxs-lookup"><span data-stu-id="6d7d0-238">Troubleshoot errors</span></span>

<span data-ttu-id="6d7d0-239">Běžné chyby:</span><span class="sxs-lookup"><span data-stu-id="6d7d0-239">Common errors:</span></span>

* <span data-ttu-id="6d7d0-240">**Autorizace vyžaduje kaskádový parametr typu Task\<AuthenticationState >. Zvažte použití CascadingAuthenticationState k zadání.**</span><span class="sxs-lookup"><span data-stu-id="6d7d0-240">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="6d7d0-241">**`null`hodnota je přijata pro`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="6d7d0-241">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="6d7d0-242">Je možné, že projekt nebyl vytvořen pomocí šablony Blazor na straně serveru s povoleným ověřováním.</span><span class="sxs-lookup"><span data-stu-id="6d7d0-242">It's likely that the project wasn't created using a Blazor server-side template with authentication enabled.</span></span> <span data-ttu-id="6d7d0-243">Zabalte kolem některé části stromu uživatelského rozhraní, například v *App. Razor* , následovně: `<CascadingAuthenticationState>`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-243">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="6d7d0-244">Doplní kaskádový parametr, který zase získá z podkladové `AuthenticationStateProvider` služby di. `Task<AuthenticationState>` `CascadingAuthenticationState`</span><span class="sxs-lookup"><span data-stu-id="6d7d0-244">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6d7d0-245">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6d7d0-245">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server-side>
* <xref:security/authentication/windowsauth>
