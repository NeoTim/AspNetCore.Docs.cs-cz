---
title: ASP.NET Core ověřování a autorizace Blazor
author: guardrex
description: Přečtěte si o scénářích ověřování a autorizace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: security/blazor/index
ms.openlocfilehash: 85a6a32ea068e6cd00ebb71bdf7fe0bd06b77618
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391317"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="7eba1-103">ASP.NET Core ověřování a autorizace Blazor</span><span class="sxs-lookup"><span data-stu-id="7eba1-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="7eba1-104">Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="7eba1-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7eba1-105">ASP.NET Core podporuje konfiguraci a správu zabezpečení v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="7eba1-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="7eba1-106">Scénáře zabezpečení se liší mezi Blazor serverem a Blazor aplikacemi WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="7eba1-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="7eba1-107">Vzhledem k tomu, že aplikace Blazor serveru běží na serveru, kontroly autorizace můžou určit:</span><span class="sxs-lookup"><span data-stu-id="7eba1-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="7eba1-108">Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou k dispozici uživateli).</span><span class="sxs-lookup"><span data-stu-id="7eba1-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="7eba1-109">Pravidla přístupu pro oblasti aplikace a součástí</span><span class="sxs-lookup"><span data-stu-id="7eba1-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="7eba1-110">Blazor aplikace WebAssembly běží na klientovi.</span><span class="sxs-lookup"><span data-stu-id="7eba1-110">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="7eba1-111">Autorizace se používá *jenom* k určení možností uživatelského rozhraní, které se mají zobrazit.</span><span class="sxs-lookup"><span data-stu-id="7eba1-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="7eba1-112">Vzhledem k tomu, že kontroly na straně klienta mohou být upraveny nebo vynechány uživatelem, aplikace Blazor WebAssembly nemůže vyhovět autorizačním pravidlům.</span><span class="sxs-lookup"><span data-stu-id="7eba1-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="7eba1-113">Ověřování</span><span class="sxs-lookup"><span data-stu-id="7eba1-113">Authentication</span></span>

<span data-ttu-id="7eba1-114">Blazor používá k vytvoření identity uživatele existující mechanismy ověřování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7eba1-114">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="7eba1-115">Přesný mechanismus závisí na tom, jak je aplikace Blazor hostovaná, Blazor Server nebo Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="7eba1-115">The exact mechanism depends on how the Blazor app is hosted, Blazor Server or Blazor WebAssembly.</span></span>

### <a name="blazor-server-authentication"></a><span data-ttu-id="7eba1-116">Ověřování serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="7eba1-116">Blazor Server authentication</span></span>

<span data-ttu-id="7eba1-117">Blazor serverové aplikace pracují přes připojení v reálném čase, které je vytvořené pomocí signálu.</span><span class="sxs-lookup"><span data-stu-id="7eba1-117">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="7eba1-118">[Ověřování v aplikacích založených na službě Signal](xref:signalr/authn-and-authz) se zpracovává při navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="7eba1-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="7eba1-119">Ověřování může být založené na souboru cookie nebo nějakém jiném nosným tokenu.</span><span class="sxs-lookup"><span data-stu-id="7eba1-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="7eba1-120">Šablona projektu serveru Blazor může pro vás nastavit ověřování při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="7eba1-120">The Blazor Server project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7eba1-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7eba1-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7eba1-122">Postupujte podle pokynů pro Visual Studio v článku <xref:blazor/get-started> a vytvořte nový projekt serveru Blazor s mechanismem ověřování.</span><span class="sxs-lookup"><span data-stu-id="7eba1-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="7eba1-123">Po výběru šablony **aplikace Blazor serveru** v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="7eba1-123">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="7eba1-124">Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="7eba1-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="7eba1-125">**Bez ověřování**</span><span class="sxs-lookup"><span data-stu-id="7eba1-125">**No Authentication**</span></span>
* <span data-ttu-id="7eba1-126">**Jednotlivé uživatelské účty** &ndash; uživatelské účty mohou být uloženy:</span><span class="sxs-lookup"><span data-stu-id="7eba1-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="7eba1-127">V aplikaci, která používá systém [identit](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7eba1-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="7eba1-128">S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="7eba1-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="7eba1-129">**Pracovní nebo školní účty**</span><span class="sxs-lookup"><span data-stu-id="7eba1-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="7eba1-130">**Ověřování systému Windows**</span><span class="sxs-lookup"><span data-stu-id="7eba1-130">**Windows Authentication**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7eba1-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7eba1-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7eba1-132">Postupujte podle pokynů Visual Studio Code v článku <xref:blazor/get-started> a vytvořte nový projekt serveru Blazor s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="7eba1-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="7eba1-133">Přípustné hodnoty ověřování (`{AUTHENTICATION}`) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="7eba1-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="7eba1-134">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="7eba1-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="7eba1-135">hodnota `{AUTHENTICATION}`</span><span class="sxs-lookup"><span data-stu-id="7eba1-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="7eba1-136">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="7eba1-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="7eba1-137">Jednoho</span><span class="sxs-lookup"><span data-stu-id="7eba1-137">Individual</span></span><br><span data-ttu-id="7eba1-138">Uživatelé uložení v aplikaci pomocí ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="7eba1-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="7eba1-139">Jednoho</span><span class="sxs-lookup"><span data-stu-id="7eba1-139">Individual</span></span><br><span data-ttu-id="7eba1-140">Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="7eba1-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="7eba1-141">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="7eba1-141">Work or School Accounts</span></span><br><span data-ttu-id="7eba1-142">Ověřování organizace pro jednoho tenanta.</span><span class="sxs-lookup"><span data-stu-id="7eba1-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="7eba1-143">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="7eba1-143">Work or School Accounts</span></span><br><span data-ttu-id="7eba1-144">Ověřování organizace pro více tenantů.</span><span class="sxs-lookup"><span data-stu-id="7eba1-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="7eba1-145">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="7eba1-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="7eba1-146">Příkaz vytvoří složku s názvem s hodnotou poskytnutou pro zástupný symbol `{APP NAME}` a jako název aplikace použije název složky.</span><span class="sxs-lookup"><span data-stu-id="7eba1-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="7eba1-147">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new) v příručce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7eba1-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

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

### <a name="blazor-webassembly-authentication"></a><span data-ttu-id="7eba1-148">Blazor ověřování WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7eba1-148">Blazor WebAssembly authentication</span></span>

<span data-ttu-id="7eba1-149">V aplikacích Blazor WebAssembly lze kontroly ověřování obejít, protože všechny kódy na straně klienta mohou být změněny uživateli.</span><span class="sxs-lookup"><span data-stu-id="7eba1-149">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="7eba1-150">Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="7eba1-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="7eba1-151">Přidejte odkaz na balíček pro [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) do souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="7eba1-151">Add a package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>

<span data-ttu-id="7eba1-152">Implementace vlastní služby `AuthenticationStateProvider` pro aplikace Blazor WebAssembly se zabývá následujícími oddíly.</span><span class="sxs-lookup"><span data-stu-id="7eba1-152">Implementation of a custom `AuthenticationStateProvider` service for Blazor WebAssembly apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="7eba1-153">Služba AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="7eba1-153">AuthenticationStateProvider service</span></span>

<span data-ttu-id="7eba1-154">Mezi serverové aplikace Blazor patří integrovaná služba @no__t 0, která získává data o stavu ověřování ze ASP.NET Core `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="7eba1-154">Blazor Server apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="7eba1-155">To je způsob, jakým se stav ověřování integruje s existujícími mechanismy ASP.NET Core ověřování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="7eba1-155">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="7eba1-156">`AuthenticationStateProvider` je základní služba, kterou komponenta `AuthorizeView` a komponenta `CascadingAuthenticationState` používá k získání stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="7eba1-156">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="7eba1-157">Přímo nepoužíváte `AuthenticationStateProvider` přímo.</span><span class="sxs-lookup"><span data-stu-id="7eba1-157">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="7eba1-158">Použijte [AuthorizeView součást](#authorizeview-component) nebo [úlohu @ no__t-2](#expose-the-authentication-state-as-a-cascading-parameter) popsanou dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="7eba1-158">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="7eba1-159">Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že komponenta není automaticky oznámena, pokud se změní příslušná data stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="7eba1-159">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="7eba1-160">Služba `AuthenticationStateProvider` může poskytovat data <xref:System.Security.Claims.ClaimsPrincipal> aktuálního uživatele, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="7eba1-160">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```cshtml
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
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

<span data-ttu-id="7eba1-161">Pokud je `user.Identity.IsAuthenticated` `true` a protože je to <xref:System.Security.Claims.ClaimsPrincipal>, mohou být deklarace identity vyhodnoceny a členství v rolích.</span><span class="sxs-lookup"><span data-stu-id="7eba1-161">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="7eba1-162">Další informace o vkládání závislostí (DI) a službách najdete v tématu <xref:blazor/dependency-injection> a <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="7eba1-162">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="7eba1-163">Implementace vlastního AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="7eba1-163">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="7eba1-164">Pokud vytváříte aplikaci WebAssembly Blazor nebo pokud specifikace vaší aplikace naprosto vyžaduje vlastního poskytovatele, implementujte zprostředkovatele a přepište `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="7eba1-164">If you're building a Blazor WebAssembly app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

namespace BlazorSample.Services
{
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
}
```

<span data-ttu-id="7eba1-165">Služba `CustomAuthStateProvider` je zaregistrovaná v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7eba1-165">The `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Components.Authorization;
// using BlazorSample.Services;

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="7eba1-166">Pomocí `CustomAuthStateProvider` se všechny uživatele ověřují pomocí uživatelského jména `mrfibuli`.</span><span class="sxs-lookup"><span data-stu-id="7eba1-166">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="7eba1-167">Zveřejnit stav ověřování jako kaskádový parametr</span><span class="sxs-lookup"><span data-stu-id="7eba1-167">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="7eba1-168">Pokud jsou v procedurální logice požadovány údaje o stavu ověřování, například při provádění akce aktivované uživatelem, Získejte údaje o stavu ověřování definováním kaskádového parametru typu `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="7eba1-168">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

> [!NOTE]
> <span data-ttu-id="7eba1-169">V součásti aplikace WebAssembly v Blazor přidejte obor názvů `Microsoft.AspNetCore.Components.Authorization` (`@using Microsoft.AspNetCore.Components.Authorization`).</span><span class="sxs-lookup"><span data-stu-id="7eba1-169">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Components.Authorization` namespace (`@using Microsoft.AspNetCore.Components.Authorization`).</span></span>

<span data-ttu-id="7eba1-170">Pokud je `user.Identity.IsAuthenticated` `true`, můžou být deklarace identity výčtované a ve vyhodnocených rolích se vytvořilo členství.</span><span class="sxs-lookup"><span data-stu-id="7eba1-170">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="7eba1-171">Pomocí komponent `AuthorizeRouteView` a `CascadingAuthenticationState` nastavte kaskádový parametr `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="7eba1-171">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components:</span></span>

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

## <a name="authorization"></a><span data-ttu-id="7eba1-172">Autorizace</span><span class="sxs-lookup"><span data-stu-id="7eba1-172">Authorization</span></span>

<span data-ttu-id="7eba1-173">Po ověření uživatele se uplatní *autorizační* pravidla, která řídí, co může uživatel dělat.</span><span class="sxs-lookup"><span data-stu-id="7eba1-173">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="7eba1-174">Přístup je obvykle udělen nebo odepřen na základě toho, zda:</span><span class="sxs-lookup"><span data-stu-id="7eba1-174">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="7eba1-175">Uživatel je ověřený (přihlášený).</span><span class="sxs-lookup"><span data-stu-id="7eba1-175">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="7eba1-176">Uživatel je v *roli*.</span><span class="sxs-lookup"><span data-stu-id="7eba1-176">A user is in a *role*.</span></span>
* <span data-ttu-id="7eba1-177">Uživatel má *deklaraci identity*.</span><span class="sxs-lookup"><span data-stu-id="7eba1-177">A user has a *claim*.</span></span>
* <span data-ttu-id="7eba1-178">Byla splněna *zásada* .</span><span class="sxs-lookup"><span data-stu-id="7eba1-178">A *policy* is satisfied.</span></span>

<span data-ttu-id="7eba1-179">Každá z těchto konceptů je stejná jako u ASP.NET Core MVC nebo Razor Pages aplikace.</span><span class="sxs-lookup"><span data-stu-id="7eba1-179">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="7eba1-180">Další informace o ASP.NET Core zabezpečení najdete v článcích [ASP.NET Core Security and identity](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="7eba1-180">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="7eba1-181">Komponenta AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="7eba1-181">AuthorizeView component</span></span>

<span data-ttu-id="7eba1-182">Komponenta `AuthorizeView` selektivně zobrazuje uživatelské rozhraní v závislosti na tom, zda je uživatel oprávněn k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="7eba1-182">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="7eba1-183">Tento přístup je užitečný, když potřebujete jenom *Zobrazit* data pro uživatele a nemusíte používat identitu uživatele v procedurální logice.</span><span class="sxs-lookup"><span data-stu-id="7eba1-183">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="7eba1-184">Tato součást zpřístupňuje proměnnou `context` typu `AuthenticationState`, kterou můžete použít pro přístup k informacím o přihlášeném uživateli:</span><span class="sxs-lookup"><span data-stu-id="7eba1-184">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="7eba1-185">V případě, že se uživatel neověřuje, můžete také Dodejte jiný obsah, který se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="7eba1-185">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="7eba1-186">Obsah značek `<Authorized>` a `<NotAuthorized>` může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="7eba1-186">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="7eba1-187">Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou uvedené v části [autorizace](#authorization) .</span><span class="sxs-lookup"><span data-stu-id="7eba1-187">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="7eba1-188">Pokud nejsou zadané autorizační podmínky, `AuthorizeView` používá výchozí zásady a zpracovává se:</span><span class="sxs-lookup"><span data-stu-id="7eba1-188">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="7eba1-189">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="7eba1-189">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="7eba1-190">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="7eba1-190">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="7eba1-191">Ověřování na základě rolí a na základě zásad</span><span class="sxs-lookup"><span data-stu-id="7eba1-191">Role-based and policy-based authorization</span></span>

<span data-ttu-id="7eba1-192">Komponenta `AuthorizeView` podporuje ověřování na základě *rolí* nebo *na základě zásad* .</span><span class="sxs-lookup"><span data-stu-id="7eba1-192">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="7eba1-193">U ověřování na základě rolí použijte parametr `Roles`:</span><span class="sxs-lookup"><span data-stu-id="7eba1-193">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="7eba1-194">Další informace najdete v tématu <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="7eba1-194">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="7eba1-195">Pro autorizaci založenou na zásadách použijte parametr `Policy`:</span><span class="sxs-lookup"><span data-stu-id="7eba1-195">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="7eba1-196">Ověřování na základě deklarací identity je zvláštní případ ověřování na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="7eba1-196">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="7eba1-197">Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="7eba1-197">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="7eba1-198">Další informace najdete v tématu <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="7eba1-198">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="7eba1-199">Tato rozhraní API se dají použít buď v Blazor serveru, nebo v aplikacích Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="7eba1-199">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="7eba1-200">Pokud není zadána žádná hodnota `Roles` ani `Policy`, používá `AuthorizeView` výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="7eba1-200">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="7eba1-201">Obsah zobrazený během asynchronního ověřování</span><span class="sxs-lookup"><span data-stu-id="7eba1-201">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="7eba1-202">Blazor umožňuje *asynchronní*určení stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="7eba1-202">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="7eba1-203">Primární scénář pro tento přístup je v aplikacích Blazor WebAssembly, které vytvářejí požadavek na externí koncový bod pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="7eba1-203">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="7eba1-204">Při ověřování probíhá `AuthorizeView` ve výchozím nastavení nezobrazí žádný obsah.</span><span class="sxs-lookup"><span data-stu-id="7eba1-204">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="7eba1-205">Chcete-li zobrazit obsah, když dojde k ověřování, použijte `<Authorizing>` elementu:</span><span class="sxs-lookup"><span data-stu-id="7eba1-205">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="7eba1-206">Tento přístup se obvykle nevztahuje na aplikace serveru Blazor.</span><span class="sxs-lookup"><span data-stu-id="7eba1-206">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="7eba1-207">Aplikace Blazor serveru znají stav ověřování, jakmile se naváže stav.</span><span class="sxs-lookup"><span data-stu-id="7eba1-207">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="7eba1-208">obsah `Authorizing` se dá zadat v součásti `AuthorizeView` aplikace serveru Blazor, ale obsah se nikdy nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="7eba1-208">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="7eba1-209">[Autorizační] – atribut</span><span class="sxs-lookup"><span data-stu-id="7eba1-209">[Authorize] attribute</span></span>

<span data-ttu-id="7eba1-210">Atribut `[Authorize]` lze použít v součástech Razor:</span><span class="sxs-lookup"><span data-stu-id="7eba1-210">The `[Authorize]` attribute can be used in Razor components:</span></span>

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> <span data-ttu-id="7eba1-211">V součásti aplikace WebAssembly v Blazor přidejte do příkladů v této části obor názvů `Microsoft.AspNetCore.Authorization` (`@using Microsoft.AspNetCore.Authorization`).</span><span class="sxs-lookup"><span data-stu-id="7eba1-211">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` namespace (`@using Microsoft.AspNetCore.Authorization`) to the examples in this section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7eba1-212">Pro součásti `@page`, které jsou dostupné prostřednictvím směrovače Blazor, použijte jenom `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="7eba1-212">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="7eba1-213">Autorizace se provádí jenom jako aspekt směrování, a *ne* pro podřízené komponenty vygenerované v rámci stránky.</span><span class="sxs-lookup"><span data-stu-id="7eba1-213">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="7eba1-214">K ověření zobrazení určitých částí na stránce použijte místo toho `AuthorizeView`.</span><span class="sxs-lookup"><span data-stu-id="7eba1-214">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="7eba1-215">Atribut `[Authorize]` také podporuje autorizaci založenou na rolích nebo na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="7eba1-215">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="7eba1-216">U ověřování na základě rolí použijte parametr `Roles`:</span><span class="sxs-lookup"><span data-stu-id="7eba1-216">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="7eba1-217">Pro autorizaci založenou na zásadách použijte parametr `Policy`:</span><span class="sxs-lookup"><span data-stu-id="7eba1-217">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="7eba1-218">Pokud není zadána žádná `Roles` ani `Policy`, používá `[Authorize]` výchozí zásadu, která ve výchozím nastavení zachází:</span><span class="sxs-lookup"><span data-stu-id="7eba1-218">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="7eba1-219">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="7eba1-219">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="7eba1-220">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="7eba1-220">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="7eba1-221">Přizpůsobení neoprávněného obsahu pomocí součásti směrovače</span><span class="sxs-lookup"><span data-stu-id="7eba1-221">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="7eba1-222">Komponenta `Router` v kombinaci s komponentou `AuthorizeRouteView` umožňuje aplikaci zadat vlastní obsah, pokud:</span><span class="sxs-lookup"><span data-stu-id="7eba1-222">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="7eba1-223">Obsah nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="7eba1-223">Content isn't found.</span></span>
* <span data-ttu-id="7eba1-224">Uživatel nevydá podmínku `[Authorize]`, která se pro komponentu aplikuje.</span><span class="sxs-lookup"><span data-stu-id="7eba1-224">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="7eba1-225">Atribut `[Authorize]` je popsán v části [[autorizační] atributu](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="7eba1-225">The `[Authorize]` attribute is covered in the [[Authorize] attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="7eba1-226">Probíhá asynchronní ověřování.</span><span class="sxs-lookup"><span data-stu-id="7eba1-226">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="7eba1-227">Ve výchozí šabloně projektu serveru Blazor soubor *App. Razor* ukazuje, jak nastavit vlastní obsah:</span><span class="sxs-lookup"><span data-stu-id="7eba1-227">In the default Blazor Server project template, the *App.razor* file demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="7eba1-228">Obsah značek `<NotFound>`, `<NotAuthorized>` a `<Authorizing>` může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="7eba1-228">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="7eba1-229">Pokud není zadán prvek `<NotAuthorized>`, používá `AuthorizeRouteView` následující záložní zprávu:</span><span class="sxs-lookup"><span data-stu-id="7eba1-229">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="7eba1-230">Oznámení o změnách stavu ověřování</span><span class="sxs-lookup"><span data-stu-id="7eba1-230">Notification about authentication state changes</span></span>

<span data-ttu-id="7eba1-231">Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například kvůli tomu, že se uživatel odhlásil nebo jiný uživatel změnil své role), vlastní `AuthenticationStateProvider` může volitelně vyvolat metodu `NotifyAuthenticationStateChanged` na základní třídě `AuthenticationStateProvider`.</span><span class="sxs-lookup"><span data-stu-id="7eba1-231">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="7eba1-232">Tím se uživatelům upozorní na data stavu ověřování (například `AuthorizeView`), která se mají znovu vykreslovat pomocí nových dat.</span><span class="sxs-lookup"><span data-stu-id="7eba1-232">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="7eba1-233">Procesní logika</span><span class="sxs-lookup"><span data-stu-id="7eba1-233">Procedural logic</span></span>

<span data-ttu-id="7eba1-234">Pokud je aplikace nutná k kontrole autorizačních pravidel v rámci procedurální logiky, použijte k získání <xref:System.Security.Claims.ClaimsPrincipal> kaskádový parametr typu `Task<AuthenticationState>`.</span><span class="sxs-lookup"><span data-stu-id="7eba1-234">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="7eba1-235">`Task<AuthenticationState>` se dá zkombinovat s jinými službami, jako je například `IAuthorizationService`, k vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="7eba1-235">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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

> [!NOTE]
> <span data-ttu-id="7eba1-236">Do komponenty aplikace Blazor WebAssembly přidejte obory názvů `Microsoft.AspNetCore.Authorization` a `Microsoft.AspNetCore.Components.Authorization`:</span><span class="sxs-lookup"><span data-stu-id="7eba1-236">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```cshtml
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="7eba1-237">Autorizace v Blazorch aplikacích WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7eba1-237">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="7eba1-238">V aplikacích Blazor WebAssembly lze kontroly autorizace obejít, protože všechny kódy na straně klienta mohou být změněny uživateli.</span><span class="sxs-lookup"><span data-stu-id="7eba1-238">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="7eba1-239">Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="7eba1-239">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="7eba1-240">**Na serveru vždy provádějte kontroly autorizace v libovolném koncovém bodu rozhraní API, ke kterému přistupovala aplikace na straně klienta.**</span><span class="sxs-lookup"><span data-stu-id="7eba1-240">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="7eba1-241">Řešení chyb</span><span class="sxs-lookup"><span data-stu-id="7eba1-241">Troubleshoot errors</span></span>

<span data-ttu-id="7eba1-242">Běžné chyby:</span><span class="sxs-lookup"><span data-stu-id="7eba1-242">Common errors:</span></span>

* <span data-ttu-id="7eba1-243">**Autorizace vyžaduje kaskádový parametr typu Task @ no__t-1AuthenticationState >. Zvažte použití CascadingAuthenticationState k zadání.**</span><span class="sxs-lookup"><span data-stu-id="7eba1-243">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="7eba1-244">**pro `authenticationStateTask` je přijata hodnota `null`.**</span><span class="sxs-lookup"><span data-stu-id="7eba1-244">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="7eba1-245">Je možné, že projekt nebyl vytvořen pomocí šablony serveru Blazor s povoleným ověřováním.</span><span class="sxs-lookup"><span data-stu-id="7eba1-245">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="7eba1-246">Zabalte `<CascadingAuthenticationState>` kolem některé části stromu uživatelského rozhraní, například v *App. Razor* následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="7eba1-246">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="7eba1-247">@No__t-0 dodá kaskádový parametr `Task<AuthenticationState>`, který pak získá ze základní služby `AuthenticationStateProvider` DI.</span><span class="sxs-lookup"><span data-stu-id="7eba1-247">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7eba1-248">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7eba1-248">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
