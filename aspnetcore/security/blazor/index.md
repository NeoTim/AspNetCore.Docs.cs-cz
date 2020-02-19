---
title: Ověřování a autorizace ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o Blazor scénářích ověřování a autorizace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: c07ffdbd5df58d6b3d19a5d75ce224d830101eac
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447422"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="c3223-103">ASP.NET Core ověřování a autorizace Blazor</span><span class="sxs-lookup"><span data-stu-id="c3223-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="c3223-104">Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="c3223-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="c3223-105">ASP.NET Core podporuje konfiguraci a správu zabezpečení v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="c3223-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="c3223-106">Scénáře zabezpečení se liší mezi Blazor serverem a Blazor aplikacemi WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="c3223-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="c3223-107">Vzhledem k tomu, že aplikace Blazor serveru běží na serveru, kontroly autorizace můžou určit:</span><span class="sxs-lookup"><span data-stu-id="c3223-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="c3223-108">Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou k dispozici uživateli).</span><span class="sxs-lookup"><span data-stu-id="c3223-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="c3223-109">Pravidla přístupu pro oblasti aplikace a součástí</span><span class="sxs-lookup"><span data-stu-id="c3223-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="c3223-110">Blazor aplikace WebAssembly běží na klientovi.</span><span class="sxs-lookup"><span data-stu-id="c3223-110">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="c3223-111">Autorizace se používá *jenom* k určení možností uživatelského rozhraní, které se mají zobrazit.</span><span class="sxs-lookup"><span data-stu-id="c3223-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="c3223-112">Vzhledem k tomu, že kontroly na straně klienta mohou být upraveny nebo vynechány uživatelem, aplikace Blazor WebAssembly nemůže vyhovět autorizačním pravidlům.</span><span class="sxs-lookup"><span data-stu-id="c3223-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="c3223-113">Ověřování</span><span class="sxs-lookup"><span data-stu-id="c3223-113">Authentication</span></span>

<span data-ttu-id="c3223-114">Blazor používá k vytvoření identity uživatele existující mechanismy ověřování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c3223-114">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="c3223-115">Přesný mechanismus závisí na tom, jak je aplikace Blazor hostovaná, Blazor Server nebo Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="c3223-115">The exact mechanism depends on how the Blazor app is hosted, Blazor Server or Blazor WebAssembly.</span></span>

### <a name="blazor-server-authentication"></a><span data-ttu-id="c3223-116">Ověřování serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="c3223-116">Blazor Server authentication</span></span>

<span data-ttu-id="c3223-117">Blazor serverové aplikace pracují přes připojení v reálném čase, které je vytvořené pomocí signálu.</span><span class="sxs-lookup"><span data-stu-id="c3223-117">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="c3223-118">[Ověřování v aplikacích založených na službě Signal](xref:signalr/authn-and-authz) se zpracovává při navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="c3223-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="c3223-119">Ověřování může být založené na souboru cookie nebo nějakém jiném nosným tokenu.</span><span class="sxs-lookup"><span data-stu-id="c3223-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="c3223-120">Šablona projektu serveru Blazor může pro vás nastavit ověřování při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="c3223-120">The Blazor Server project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3223-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3223-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c3223-122">Pokud chcete vytvořit nový projekt serveru Blazor s mechanismem ověřování, postupujte podle pokynů pro Visual Studio v tématu <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="c3223-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="c3223-123">Po výběru šablony **aplikace Blazor serveru** v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="c3223-123">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="c3223-124">Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c3223-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="c3223-125">**Bez ověřování**</span><span class="sxs-lookup"><span data-stu-id="c3223-125">**No Authentication**</span></span>
* <span data-ttu-id="c3223-126">**Jednotlivé uživatelské účty** &ndash; uživatelských účtů mohou být uloženy:</span><span class="sxs-lookup"><span data-stu-id="c3223-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="c3223-127">V aplikaci, která používá systém [identit](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c3223-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="c3223-128">S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="c3223-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="c3223-129">**Pracovní nebo školní účty**</span><span class="sxs-lookup"><span data-stu-id="c3223-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="c3223-130">**Ověřování systému Windows**</span><span class="sxs-lookup"><span data-stu-id="c3223-130">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3223-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3223-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c3223-132">Postupujte podle pokynů Visual Studio Code v článku <xref:blazor/get-started> a vytvořte nový projekt serveru Blazor s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="c3223-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="c3223-133">V následující tabulce jsou uvedeny přípustné hodnoty ověřování (`{AUTHENTICATION}`).</span><span class="sxs-lookup"><span data-stu-id="c3223-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="c3223-134">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="c3223-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="c3223-135">hodnota `{AUTHENTICATION}`</span><span class="sxs-lookup"><span data-stu-id="c3223-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="c3223-136">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="c3223-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="c3223-137">Jednoho</span><span class="sxs-lookup"><span data-stu-id="c3223-137">Individual</span></span><br><span data-ttu-id="c3223-138">Uživatelé uložení v aplikaci pomocí ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="c3223-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="c3223-139">Jednoho</span><span class="sxs-lookup"><span data-stu-id="c3223-139">Individual</span></span><br><span data-ttu-id="c3223-140">Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="c3223-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="c3223-141">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="c3223-141">Work or School Accounts</span></span><br><span data-ttu-id="c3223-142">Ověřování organizace pro jednoho tenanta.</span><span class="sxs-lookup"><span data-stu-id="c3223-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="c3223-143">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="c3223-143">Work or School Accounts</span></span><br><span data-ttu-id="c3223-144">Ověřování organizace pro více tenantů.</span><span class="sxs-lookup"><span data-stu-id="c3223-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="c3223-145">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="c3223-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="c3223-146">Příkaz vytvoří složku s názvem s hodnotou poskytnutou pro zástupný symbol `{APP NAME}` a jako název aplikace použije název složky.</span><span class="sxs-lookup"><span data-stu-id="c3223-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="c3223-147">Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new) v příručce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c3223-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

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

### <a name="opno-locblazor-webassembly-authentication"></a><span data-ttu-id="c3223-148">ověřování Blazorho WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c3223-148">Blazor WebAssembly authentication</span></span>

<span data-ttu-id="c3223-149">V Blazor aplikace WebAssembly lze kontroly ověřování obejít, protože všechny kódy na straně klienta mohou být změněny uživateli.</span><span class="sxs-lookup"><span data-stu-id="c3223-149">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="c3223-150">Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="c3223-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="c3223-151">Přidejte odkaz na balíček pro [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) do souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="c3223-151">Add a package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>

<span data-ttu-id="c3223-152">Implementace vlastní služby `AuthenticationStateProvider` pro Blazor aplikace WebAssembly je popsaná v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="c3223-152">Implementation of a custom `AuthenticationStateProvider` service for Blazor WebAssembly apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="c3223-153">Služba AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="c3223-153">AuthenticationStateProvider service</span></span>

<span data-ttu-id="c3223-154">mezi aplikace Blazor serveru patří integrovaná `AuthenticationStateProvider` služba, která získává data stavu ověřování ze `HttpContext.User`ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c3223-154">Blazor Server apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="c3223-155">To je způsob, jakým se stav ověřování integruje s existujícími mechanismy ASP.NET Core ověřování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="c3223-155">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="c3223-156">`AuthenticationStateProvider` je základní služba používaná komponentou `AuthorizeView` a `CascadingAuthenticationState` komponentou pro získání stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="c3223-156">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="c3223-157">Nepoužíváte obvykle `AuthenticationStateProvider` přímo.</span><span class="sxs-lookup"><span data-stu-id="c3223-157">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="c3223-158">Použijte [komponentu AuthorizeView](#authorizeview-component) nebo [úkoly<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) přístupy popsané dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="c3223-158">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="c3223-159">Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že komponenta není automaticky oznámena, pokud se změní příslušná data stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="c3223-159">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="c3223-160">Služba `AuthenticationStateProvider` může poskytnout data <xref:System.Security.Claims.ClaimsPrincipal> aktuálního uživatele, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c3223-160">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="LogUsername">Write user info to console</button>

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

<span data-ttu-id="c3223-161">Pokud je `user.Identity.IsAuthenticated` `true` a protože je uživatel <xref:System.Security.Claims.ClaimsPrincipal>, mohou být deklarace identity vyhodnoceny a členství v rolích.</span><span class="sxs-lookup"><span data-stu-id="c3223-161">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="c3223-162">Další informace o vkládání závislostí (DI) a službách najdete v tématu <xref:blazor/dependency-injection> a <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="c3223-162">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="c3223-163">Implementace vlastního AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="c3223-163">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="c3223-164">Pokud vytváříte aplikaci Blazor WebAssembly nebo pokud specifikace vaší aplikace naprosto vyžaduje vlastního poskytovatele, implementujte poskytovatele a přepište `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="c3223-164">If you're building a Blazor WebAssembly app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="c3223-165">V Blazor aplikaci WebAssembly je `CustomAuthStateProvider` služba zaregistrovaná v `Main` *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3223-165">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Blazor.Hosting;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.Extensions.DependencyInjection;
using BlazorSample.Services;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddScoped<AuthenticationStateProvider, 
            CustomAuthStateProvider>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="c3223-166">Pomocí `CustomAuthStateProvider`se všechny uživatele ověřují pomocí uživatelského jména `mrfibuli`.</span><span class="sxs-lookup"><span data-stu-id="c3223-166">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="c3223-167">Zveřejnit stav ověřování jako kaskádový parametr</span><span class="sxs-lookup"><span data-stu-id="c3223-167">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="c3223-168">Pokud jsou v procedurální logice požadovány údaje o stavu ověřování, například při provádění akce aktivované uživatelem, Získejte údaje o stavu ověřování definováním kaskádového parametru typu `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="c3223-168">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

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
> <span data-ttu-id="c3223-169">V Blazor komponenty aplikace WebAssembly přidejte obor názvů `Microsoft.AspNetCore.Components.Authorization` (`@using Microsoft.AspNetCore.Components.Authorization`).</span><span class="sxs-lookup"><span data-stu-id="c3223-169">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Components.Authorization` namespace (`@using Microsoft.AspNetCore.Components.Authorization`).</span></span>

<span data-ttu-id="c3223-170">Pokud je `user.Identity.IsAuthenticated` `true`, můžou být deklarace identity výčtované a ve vyhodnocených rolích se vytvořilo členství.</span><span class="sxs-lookup"><span data-stu-id="c3223-170">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="c3223-171">Nastavte `Task<AuthenticationState>` kaskádový parametr pomocí `AuthorizeRouteView` a `CascadingAuthenticationState` komponenty v souboru *App. Razor* :</span><span class="sxs-lookup"><span data-stu-id="c3223-171">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the *App.razor* file:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization

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

<span data-ttu-id="c3223-172">Přidejte služby pro možnosti a autorizaci `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c3223-172">Add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

## <a name="authorization"></a><span data-ttu-id="c3223-173">Autorizace</span><span class="sxs-lookup"><span data-stu-id="c3223-173">Authorization</span></span>

<span data-ttu-id="c3223-174">Po ověření uživatele se uplatní *autorizační* pravidla, která řídí, co může uživatel dělat.</span><span class="sxs-lookup"><span data-stu-id="c3223-174">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="c3223-175">Přístup je obvykle udělen nebo odepřen na základě toho, zda:</span><span class="sxs-lookup"><span data-stu-id="c3223-175">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="c3223-176">Uživatel je ověřený (přihlášený).</span><span class="sxs-lookup"><span data-stu-id="c3223-176">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="c3223-177">Uživatel je v *roli*.</span><span class="sxs-lookup"><span data-stu-id="c3223-177">A user is in a *role*.</span></span>
* <span data-ttu-id="c3223-178">Uživatel má *deklaraci identity*.</span><span class="sxs-lookup"><span data-stu-id="c3223-178">A user has a *claim*.</span></span>
* <span data-ttu-id="c3223-179">Byla splněna *zásada* .</span><span class="sxs-lookup"><span data-stu-id="c3223-179">A *policy* is satisfied.</span></span>

<span data-ttu-id="c3223-180">Každá z těchto konceptů je stejná jako u ASP.NET Core MVC nebo Razor Pages aplikace.</span><span class="sxs-lookup"><span data-stu-id="c3223-180">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="c3223-181">Další informace o ASP.NET Core zabezpečení najdete v článcích [ASP.NET Core Security and identity](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="c3223-181">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="c3223-182">Komponenta AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="c3223-182">AuthorizeView component</span></span>

<span data-ttu-id="c3223-183">Součást `AuthorizeView` selektivně zobrazuje uživatelské rozhraní v závislosti na tom, zda je uživatel oprávněn k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c3223-183">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="c3223-184">Tento přístup je užitečný, když potřebujete jenom *Zobrazit* data pro uživatele a nemusíte používat identitu uživatele v procedurální logice.</span><span class="sxs-lookup"><span data-stu-id="c3223-184">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="c3223-185">Komponenta zpřístupňuje `context` proměnnou typu `AuthenticationState`, kterou můžete použít pro přístup k informacím o přihlášeném uživateli:</span><span class="sxs-lookup"><span data-stu-id="c3223-185">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="c3223-186">V případě, že se uživatel neověřuje, můžete také Dodejte jiný obsah, který se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="c3223-186">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="c3223-187">Obsah značek `<Authorized>` a `<NotAuthorized>` může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="c3223-187">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="c3223-188">Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou uvedené v části [autorizace](#authorization) .</span><span class="sxs-lookup"><span data-stu-id="c3223-188">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="c3223-189">Pokud nejsou zadané autorizační podmínky, `AuthorizeView` používá výchozí zásady a zpracovává se:</span><span class="sxs-lookup"><span data-stu-id="c3223-189">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="c3223-190">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="c3223-190">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="c3223-191">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="c3223-191">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="c3223-192">Ověřování na základě rolí a na základě zásad</span><span class="sxs-lookup"><span data-stu-id="c3223-192">Role-based and policy-based authorization</span></span>

<span data-ttu-id="c3223-193">Komponenta `AuthorizeView` podporuje autorizaci založenou na *rolích* nebo *na základě zásad* .</span><span class="sxs-lookup"><span data-stu-id="c3223-193">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="c3223-194">U ověřování na základě rolí použijte parametr `Roles`:</span><span class="sxs-lookup"><span data-stu-id="c3223-194">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="c3223-195">Další informace naleznete v tématu <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="c3223-195">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="c3223-196">Pro autorizaci založenou na zásadách použijte parametr `Policy`:</span><span class="sxs-lookup"><span data-stu-id="c3223-196">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="c3223-197">Ověřování na základě deklarací identity je zvláštní případ ověřování na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="c3223-197">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="c3223-198">Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="c3223-198">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="c3223-199">Další informace naleznete v tématu <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="c3223-199">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="c3223-200">Tato rozhraní API se dají použít buď v Blazor serveru, nebo v Blazor aplikace WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="c3223-200">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="c3223-201">Pokud není zadána žádná `Roles` ani `Policy`, `AuthorizeView` použije výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="c3223-201">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="c3223-202">Obsah zobrazený během asynchronního ověřování</span><span class="sxs-lookup"><span data-stu-id="c3223-202">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="c3223-203"> umožňuje *asynchronní*určení stavu ověřování.</span><span class="sxs-lookup"><span data-stu-id="c3223-203"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="c3223-204">Primární scénář pro tento přístup je v Blazor aplikace WebAssembly, které vytvářejí požadavek na externí koncový bod pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="c3223-204">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="c3223-205">Při ověřování probíhá `AuthorizeView` ve výchozím nastavení nezobrazí žádný obsah.</span><span class="sxs-lookup"><span data-stu-id="c3223-205">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="c3223-206">Chcete-li zobrazit obsah, když dojde k ověřování, použijte element `<Authorizing>`:</span><span class="sxs-lookup"><span data-stu-id="c3223-206">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="c3223-207">Tento přístup se obvykle nevztahuje na Blazor serverových aplikací.</span><span class="sxs-lookup"><span data-stu-id="c3223-207">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="c3223-208">aplikace Blazor serveru znají stav ověřování, jakmile se stav naváže.</span><span class="sxs-lookup"><span data-stu-id="c3223-208">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="c3223-209">obsah `Authorizing` lze poskytnout v součásti `AuthorizeView` serverové aplikace Blazor, ale obsah se nikdy nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="c3223-209">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="c3223-210">[Autorizační] – atribut</span><span class="sxs-lookup"><span data-stu-id="c3223-210">[Authorize] attribute</span></span>

<span data-ttu-id="c3223-211">Atribut `[Authorize]` lze použít v součástech Razor:</span><span class="sxs-lookup"><span data-stu-id="c3223-211">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> <span data-ttu-id="c3223-212">V Blazor komponenty aplikace WebAssembly přidejte do příkladů v této části obor názvů `Microsoft.AspNetCore.Authorization` (`@using Microsoft.AspNetCore.Authorization`).</span><span class="sxs-lookup"><span data-stu-id="c3223-212">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` namespace (`@using Microsoft.AspNetCore.Authorization`) to the examples in this section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c3223-213">Pomocí Blazor směrovače dorazí jenom `[Authorize]` na `@page` součásti.</span><span class="sxs-lookup"><span data-stu-id="c3223-213">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="c3223-214">Autorizace se provádí jenom jako aspekt směrování, a *ne* pro podřízené komponenty vygenerované v rámci stránky.</span><span class="sxs-lookup"><span data-stu-id="c3223-214">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="c3223-215">K ověření zobrazení určitých částí na stránce použijte místo toho `AuthorizeView`.</span><span class="sxs-lookup"><span data-stu-id="c3223-215">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="c3223-216">Atribut `[Authorize]` také podporuje ověřování na základě rolí nebo na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="c3223-216">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="c3223-217">U ověřování na základě rolí použijte parametr `Roles`:</span><span class="sxs-lookup"><span data-stu-id="c3223-217">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="c3223-218">Pro autorizaci založenou na zásadách použijte parametr `Policy`:</span><span class="sxs-lookup"><span data-stu-id="c3223-218">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="c3223-219">Pokud není zadána žádná `Roles` ani `Policy`, `[Authorize]` používá výchozí zásadu, která ve výchozím nastavení zachází:</span><span class="sxs-lookup"><span data-stu-id="c3223-219">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="c3223-220">Ověření uživatelé (přihlášeni) jako autorizované.</span><span class="sxs-lookup"><span data-stu-id="c3223-220">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="c3223-221">Neověřené (odhlášené) uživatelé jako neautorizované.</span><span class="sxs-lookup"><span data-stu-id="c3223-221">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="c3223-222">Přizpůsobení neoprávněného obsahu pomocí součásti směrovače</span><span class="sxs-lookup"><span data-stu-id="c3223-222">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="c3223-223">Komponenta `Router` společně s komponentou `AuthorizeRouteView` umožňuje aplikaci zadat vlastní obsah, pokud:</span><span class="sxs-lookup"><span data-stu-id="c3223-223">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="c3223-224">Obsah nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="c3223-224">Content isn't found.</span></span>
* <span data-ttu-id="c3223-225">Uživatel nevydá `[Authorize]` podmínku použitou pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="c3223-225">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="c3223-226">Atribut `[Authorize]` je popsán v části [`[Authorize]` atributu](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="c3223-226">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="c3223-227">Probíhá asynchronní ověřování.</span><span class="sxs-lookup"><span data-stu-id="c3223-227">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="c3223-228">Ve výchozí šabloně projektu Blazor serveru ukazuje soubor *App. Razor* , jak nastavit vlastní obsah:</span><span class="sxs-lookup"><span data-stu-id="c3223-228">In the default Blazor Server project template, the *App.razor* file demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="c3223-229">Obsah značek `<NotFound>`, `<NotAuthorized>`a `<Authorizing>` může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="c3223-229">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="c3223-230">Pokud `<NotAuthorized>` element není zadán, `AuthorizeRouteView` používá následující záložní zprávu:</span><span class="sxs-lookup"><span data-stu-id="c3223-230">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="c3223-231">Oznámení o změnách stavu ověřování</span><span class="sxs-lookup"><span data-stu-id="c3223-231">Notification about authentication state changes</span></span>

<span data-ttu-id="c3223-232">Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například kvůli tomu, že se uživatel odhlásil nebo jiný uživatel změnil své role), vlastní `AuthenticationStateProvider` může volitelně vyvolat metodu `NotifyAuthenticationStateChanged` na `AuthenticationStateProvider` základní třídu.</span><span class="sxs-lookup"><span data-stu-id="c3223-232">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="c3223-233">Tím se uživatelům upozorní na data stavu ověřování (například `AuthorizeView`), která se mají znovu vykreslovat pomocí nových dat.</span><span class="sxs-lookup"><span data-stu-id="c3223-233">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="c3223-234">Procesní logika</span><span class="sxs-lookup"><span data-stu-id="c3223-234">Procedural logic</span></span>

<span data-ttu-id="c3223-235">Pokud je aplikace nutná k kontrole autorizačních pravidel v rámci procedurální logiky, použijte k získání <xref:System.Security.Claims.ClaimsPrincipal>uživatele kaskádový parametr typu `Task<AuthenticationState>`.</span><span class="sxs-lookup"><span data-stu-id="c3223-235">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="c3223-236">`Task<AuthenticationState>` lze kombinovat s jinými službami, jako je například `IAuthorizationService`, k vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="c3223-236">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="c3223-237">Do Blazor komponenty aplikace WebAssembly přidejte obory názvů `Microsoft.AspNetCore.Authorization` a `Microsoft.AspNetCore.Components.Authorization`:</span><span class="sxs-lookup"><span data-stu-id="c3223-237">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="c3223-238">Autorizace v Blazor aplikace WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c3223-238">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="c3223-239">V Blazor aplikacích pro WebAssembly lze kontroly autorizace obejít, protože všechny kódy na straně klienta mohou být změněny uživateli.</span><span class="sxs-lookup"><span data-stu-id="c3223-239">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="c3223-240">Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="c3223-240">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="c3223-241">**Na serveru vždy provádějte kontroly autorizace v libovolném koncovém bodu rozhraní API, ke kterému přistupovala aplikace na straně klienta.**</span><span class="sxs-lookup"><span data-stu-id="c3223-241">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="c3223-242">Řešení chyb</span><span class="sxs-lookup"><span data-stu-id="c3223-242">Troubleshoot errors</span></span>

<span data-ttu-id="c3223-243">Běžné chyby:</span><span class="sxs-lookup"><span data-stu-id="c3223-243">Common errors:</span></span>

* <span data-ttu-id="c3223-244">**Autorizace vyžaduje kaskádový parametr typu Task\<AuthenticationState >. Zvažte použití CascadingAuthenticationState k zadání.**</span><span class="sxs-lookup"><span data-stu-id="c3223-244">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="c3223-245">**`null` hodnota se přijímá pro `authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="c3223-245">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="c3223-246">Je možné, že projekt nebyl vytvořen pomocí šablony serveru Blazor s povoleným ověřováním.</span><span class="sxs-lookup"><span data-stu-id="c3223-246">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="c3223-247">Zabalte `<CascadingAuthenticationState>` kolem některé části stromu uživatelského rozhraní, například v *App. Razor* následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c3223-247">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="c3223-248">`CascadingAuthenticationState` poskytuje `Task<AuthenticationState>` kaskádový parametr, který pak získá od příslušné služby `AuthenticationStateProvider` DI.</span><span class="sxs-lookup"><span data-stu-id="c3223-248">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3223-249">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c3223-249">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="c3223-250">[Super Blazor:](https://github.com/AdrienTorris/awesome-blazor#authentication) ukázkové odkazy komunity ověřování</span><span class="sxs-lookup"><span data-stu-id="c3223-250">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
