---
title: ASP.NET Core Blazor ověřování a autorizace
author: guardrex
description: Další informace o Blazor scénářů ověřování a autorizace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/26/2019
uid: security/blazor/index
ms.openlocfilehash: b3bca26e7088a8353084a065f9b9593c9d8e08e6
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406199"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="e9c89-103">ASP.NET Core Blazor ověřování a autorizace</span><span class="sxs-lookup"><span data-stu-id="e9c89-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="e9c89-104">Podle [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="e9c89-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="e9c89-105">ASP.NET Core podporuje konfiguraci a správu zabezpečení v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="e9c89-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="e9c89-106">Scénáře zabezpečení se liší mezi Blazor serverové a klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="e9c89-106">Security scenarios differ between Blazor server-side and client-side apps.</span></span> <span data-ttu-id="e9c89-107">Protože Blazor serverové aplikace spustit na serveru, budou moct určit kontroly autorizace:</span><span class="sxs-lookup"><span data-stu-id="e9c89-107">Because Blazor server-side apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="e9c89-108">Možnosti uživatelského rozhraní zobrazovat uživatele (například položky nabídky, které jsou k dispozici pro uživatele).</span><span class="sxs-lookup"><span data-stu-id="e9c89-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="e9c89-109">Pravidla přístupu pro oblasti aplikace a komponenty.</span><span class="sxs-lookup"><span data-stu-id="e9c89-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="e9c89-110">Aplikace na straně klienta Blazor na klientech.</span><span class="sxs-lookup"><span data-stu-id="e9c89-110">Blazor client-side apps run on the client.</span></span> <span data-ttu-id="e9c89-111">Autorizace je *pouze* používá k určení, které pokud chcete zobrazit možnosti uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e9c89-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="e9c89-112">Protože kontroly na straně klienta může obejít uživatelem nebo upraví, aplikace na straně klienta Blazor nemůže vynutit pravidla autorizace přístupu.</span><span class="sxs-lookup"><span data-stu-id="e9c89-112">Since client-side checks can be modified or bypassed by a user, a Blazor client-side app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="e9c89-113">Ověřování</span><span class="sxs-lookup"><span data-stu-id="e9c89-113">Authentication</span></span>

<span data-ttu-id="e9c89-114">Blazor pomocí stávající mechanismy ověřování ASP.NET Core zřizuje identitu uživatele.</span><span class="sxs-lookup"><span data-stu-id="e9c89-114">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="e9c89-115">Přesný postup závisí na tom, jak je hostitelem aplikace Blazor, na straně serveru nebo na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e9c89-115">The exact mechanism depends on how the Blazor app is hosted, server-side or client-side.</span></span>

### <a name="blazor-server-side-authentication"></a><span data-ttu-id="e9c89-116">Blazor ověřování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="e9c89-116">Blazor server-side authentication</span></span>

<span data-ttu-id="e9c89-117">Aplikace na straně serveru Blazor provoz přes připojení v reálném čase, který je vytvořen pomocí nástroje SignalR.</span><span class="sxs-lookup"><span data-stu-id="e9c89-117">Blazor server-side apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="e9c89-118">[Ověřování v aplikacích založených na SignalR](xref:signalr/authn-and-authz) je zpracována při vytvoření připojení.</span><span class="sxs-lookup"><span data-stu-id="e9c89-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="e9c89-119">Ověřování může být založen na souboru cookie nebo některé jiné nosný token.</span><span class="sxs-lookup"><span data-stu-id="e9c89-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="e9c89-120">Šablona projektu na straně serveru Blazor můžete nastavit ověřování za vás při vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="e9c89-120">The Blazor server-side project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e9c89-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9c89-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e9c89-122">Postupujte podle pokynů sady Visual Studio v <xref:blazor/get-started> článku vytvořte nový projekt na straně serveru Blazor s mechanismus ověřování.</span><span class="sxs-lookup"><span data-stu-id="e9c89-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism.</span></span>

<span data-ttu-id="e9c89-123">Po výběru **Blazor (serverové)** šablony v **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, vyberte **změnu** pod **ověřování** .</span><span class="sxs-lookup"><span data-stu-id="e9c89-123">After choosing the **Blazor (server-side)** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="e9c89-124">Otevře se dialogové okno nabízí stejnou sadu ověřovací mechanismy dostupné pro jiné technologie ASP.NET Core projekty:</span><span class="sxs-lookup"><span data-stu-id="e9c89-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="e9c89-125">**Bez ověřování**</span><span class="sxs-lookup"><span data-stu-id="e9c89-125">**No Authentication**</span></span>
* <span data-ttu-id="e9c89-126">**Individuální uživatelské účty** &ndash; mohou být uloženy uživatelských účtů:</span><span class="sxs-lookup"><span data-stu-id="e9c89-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="e9c89-127">V rámci aplikace pomocí ASP.NET Core [Identity](xref:security/authentication/identity) systému.</span><span class="sxs-lookup"><span data-stu-id="e9c89-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="e9c89-128">S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="e9c89-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="e9c89-129">**Pracovní nebo školní účty**</span><span class="sxs-lookup"><span data-stu-id="e9c89-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="e9c89-130">**Ověřování Windows**</span><span class="sxs-lookup"><span data-stu-id="e9c89-130">**Windows Authentication**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e9c89-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9c89-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e9c89-132">Postupujte podle pokynů Visual Studio Code v <xref:blazor/get-started> článku vytvořte nový projekt na straně serveru Blazor s mechanismus ověřování:</span><span class="sxs-lookup"><span data-stu-id="e9c89-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism:</span></span>

```console
dotnet new blazorserverside -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="e9c89-133">Hodnoty povolenou ověřování (`{AUTHENTICATION}`) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="e9c89-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="e9c89-134">Mechanismus ověřování</span><span class="sxs-lookup"><span data-stu-id="e9c89-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="e9c89-135">`{AUTHENTICATION}` Hodnota</span><span class="sxs-lookup"><span data-stu-id="e9c89-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="e9c89-136">Bez ověřování</span><span class="sxs-lookup"><span data-stu-id="e9c89-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="e9c89-137">Osoba</span><span class="sxs-lookup"><span data-stu-id="e9c89-137">Individual</span></span><br><span data-ttu-id="e9c89-138">Uživatelé uložení v aplikaci s ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="e9c89-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="e9c89-139">Osoba</span><span class="sxs-lookup"><span data-stu-id="e9c89-139">Individual</span></span><br><span data-ttu-id="e9c89-140">Uživatelé ukládají v [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="e9c89-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="e9c89-141">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="e9c89-141">Work or School Accounts</span></span><br><span data-ttu-id="e9c89-142">Ověřování organizace pro jednoho tenanta.</span><span class="sxs-lookup"><span data-stu-id="e9c89-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="e9c89-143">Pracovní nebo školní účty</span><span class="sxs-lookup"><span data-stu-id="e9c89-143">Work or School Accounts</span></span><br><span data-ttu-id="e9c89-144">Ověřování organizace pro více tenantů.</span><span class="sxs-lookup"><span data-stu-id="e9c89-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="e9c89-145">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="e9c89-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="e9c89-146">Tento příkaz vytvoří složku s názvem se hodnota zadaná pro `{APP NAME}` zástupný text a používá název složky názvem aplikace.</span><span class="sxs-lookup"><span data-stu-id="e9c89-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="e9c89-147">Další informace najdete v tématu [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz v příručce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e9c89-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

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
dotnet new blazorserverside -o {APP NAME} -au {AUTHENTICATION}
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

### <a name="blazor-client-side-authentication"></a><span data-ttu-id="e9c89-148">Blazor ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="e9c89-148">Blazor client-side authentication</span></span>

<span data-ttu-id="e9c89-149">V aplikacích Blazor na straně klienta lze vynechat kontroly ověřování vzhledem k tomu, že veškerý kód na straně klienta je možné upravovat prostřednictvím uživatelů.</span><span class="sxs-lookup"><span data-stu-id="e9c89-149">In Blazor client-side apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="e9c89-150">Totéž platí pro všechny aplikace na straně klienta technologií, jako je JavaScript SPA rozhraní nebo nativní aplikace pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="e9c89-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="e9c89-151">Implementace vlastního `AuthenticationStateProvider` služby service for apps Blazor na straně klienta je popsané v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="e9c89-151">Implementation of a custom `AuthenticationStateProvider` service for Blazor client-side apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="e9c89-152">AuthenticationStateProvider služby</span><span class="sxs-lookup"><span data-stu-id="e9c89-152">AuthenticationStateProvider service</span></span>

<span data-ttu-id="e9c89-153">Aplikace na straně serveru Blazor zahrnují integrované `AuthenticationStateProvider` služba, která získává data o stavu ověřování z ASP.NET Core `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="e9c89-153">Blazor server-side apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="e9c89-154">To je, jak stav ověřování integruje do stávající mechanismy ověřování na straně serveru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e9c89-154">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="e9c89-155">`AuthenticationStateProvider` je základní služby používané `AuthorizeView` komponenty a `CascadingAuthenticationState` součást pro získání stavu ověření.</span><span class="sxs-lookup"><span data-stu-id="e9c89-155">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="e9c89-156">Nepoužívejte obvykle `AuthenticationStateProvider` přímo.</span><span class="sxs-lookup"><span data-stu-id="e9c89-156">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="e9c89-157">Použití [AuthorizeView komponenty](#authorizeview-component) nebo [úloh<AuthenticationState> ](#expose-the-authentication-state-as-a-cascading-parameter) přístupů popsaných dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="e9c89-157">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="e9c89-158">Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že není-li základní ověřování stavu změny dat automaticky upozorněn komponentu.</span><span class="sxs-lookup"><span data-stu-id="e9c89-158">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="e9c89-159">`AuthenticationStateProvider` Služba může poskytovat aktuální uživatel <xref:System.Security.Claims.ClaimsPrincipal> data, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e9c89-159">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="e9c89-160">Pokud `user.Identity.IsAuthenticated` je `true` , a proto je uživatel <xref:System.Security.Claims.ClaimsPrincipal>, jsou uvedené deklarace identity a vyhodnocování členství v rolích.</span><span class="sxs-lookup"><span data-stu-id="e9c89-160">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="e9c89-161">Další informace o injektáž závislostí (DI) a služby, najdete v části <xref:blazor/dependency-injection> a <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="e9c89-161">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="e9c89-162">Implementovat vlastní AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="e9c89-162">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="e9c89-163">Pokud jste sestavujete aplikaci na straně klienta Blazor nebo pokud vaše aplikace specifikace naprosto vyžaduje vlastního zprostředkovatele implementaci zprostředkovatele a přepsat `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="e9c89-163">If you're building a Blazor client-side app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="e9c89-164">`CustomAuthStateProvider` Není registrován v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e9c89-164">The `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
}
```

<span data-ttu-id="e9c89-165">Použití `CustomAuthStateProvider`, všichni uživatelé, se ověří pomocí uživatelského jména `mrfibuli`.</span><span class="sxs-lookup"><span data-stu-id="e9c89-165">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="e9c89-166">Stav ověřování vystavit jako parametr šablony</span><span class="sxs-lookup"><span data-stu-id="e9c89-166">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="e9c89-167">Pokud data ověření stavu, je třeba procesní logiky, jako je například provést akci při aktivaci tohoto uživatele získat ověřovací data stavu tak, že definujete šablony parametr typu `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="e9c89-167">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

<span data-ttu-id="e9c89-168">Pokud `user.Identity.IsAuthenticated` je `true`, jsou uvedené deklarace identity a vyhodnocování členství v rolích.</span><span class="sxs-lookup"><span data-stu-id="e9c89-168">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="e9c89-169">Nastavit `Task<AuthenticationState>` kaskádové pomocí parametru `CascadingAuthenticationState` komponenty:</span><span class="sxs-lookup"><span data-stu-id="e9c89-169">Set up the `Task<AuthenticationState>` cascading parameter using the `CascadingAuthenticationState` component:</span></span>

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        <NotFoundContent>
            <h1>Sorry</h1>
            <p>Sorry, there's nothing at this address.</p>
        </NotFoundContent>
    </Router>
</CascadingAuthenticationState>
```

## <a name="authorization"></a><span data-ttu-id="e9c89-170">Autorizace</span><span class="sxs-lookup"><span data-stu-id="e9c89-170">Authorization</span></span>

<span data-ttu-id="e9c89-171">Po ověření uživatele *autorizace* pravidla se používají k řízení, co můžete dělat uživatele.</span><span class="sxs-lookup"><span data-stu-id="e9c89-171">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="e9c89-172">Obvykle povolen nebo odepřen přístup podle toho, jestli:</span><span class="sxs-lookup"><span data-stu-id="e9c89-172">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="e9c89-173">Ověření uživatele (přihlášení).</span><span class="sxs-lookup"><span data-stu-id="e9c89-173">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="e9c89-174">Je uživatel v *role*.</span><span class="sxs-lookup"><span data-stu-id="e9c89-174">A user is in a *role*.</span></span>
* <span data-ttu-id="e9c89-175">Uživatel má *deklarace identity*.</span><span class="sxs-lookup"><span data-stu-id="e9c89-175">A user has a *claim*.</span></span>
* <span data-ttu-id="e9c89-176">A *zásady* je spokojeni.</span><span class="sxs-lookup"><span data-stu-id="e9c89-176">A *policy* is satisfied.</span></span>

<span data-ttu-id="e9c89-177">Každá z těchto konceptů je stejný jako v aplikaci ASP.NET Core MVC nebo stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="e9c89-177">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="e9c89-178">Další informace o zabezpečení ASP.NET Core, najdete v článcích v části [ASP.NET Core zabezpečení a identita](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="e9c89-178">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="e9c89-179">Komponenta AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="e9c89-179">AuthorizeView component</span></span>

<span data-ttu-id="e9c89-180">`AuthorizeView` Komponenty selektivně zobrazí uživatelské rozhraní v závislosti na tom, jestli je uživatel oprávnění a prohlédněte si ho.</span><span class="sxs-lookup"><span data-stu-id="e9c89-180">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="e9c89-181">Tento přístup je užitečný, pokud je potřeba jenom *zobrazit* data uživatele a není nutné používat identitu uživatelů v procesní logiky.</span><span class="sxs-lookup"><span data-stu-id="e9c89-181">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="e9c89-182">Komponenta zpřístupní `context` proměnné typu `AuthenticationState`, který můžete použít pro přístup k informacím o přihlášeného uživatele:</span><span class="sxs-lookup"><span data-stu-id="e9c89-182">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="e9c89-183">Pokud uživatel není ověřen, lze také zadat jiný obsah pro zobrazení:</span><span class="sxs-lookup"><span data-stu-id="e9c89-183">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="e9c89-184">Obsah `<Authorized>` a `<NotAuthorized>` může obsahovat libovolné položky, jako další interaktivní komponenty.</span><span class="sxs-lookup"><span data-stu-id="e9c89-184">The content of `<Authorized>` and `<NotAuthorized>` can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="e9c89-185">Autorizace podmínky, například role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou popsané v [autorizace](#authorization) oddílu.</span><span class="sxs-lookup"><span data-stu-id="e9c89-185">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="e9c89-186">Pokud nejsou zadané podmínky autorizace, `AuthorizeView` využívá výchozí zásady a zpracovává:</span><span class="sxs-lookup"><span data-stu-id="e9c89-186">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="e9c89-187">Ověřeným uživatelům (přihlášeni), jako oprávněnou.</span><span class="sxs-lookup"><span data-stu-id="e9c89-187">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="e9c89-188">Neověřené uživatele (podepsané out) jako neověřené.</span><span class="sxs-lookup"><span data-stu-id="e9c89-188">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="e9c89-189">Ověřování založené na rolích a na základě zásad</span><span class="sxs-lookup"><span data-stu-id="e9c89-189">Role-based and policy-based authorization</span></span>

<span data-ttu-id="e9c89-190">`AuthorizeView` Komponenta podporuje *na základě rolí* nebo *založené na zásadách* autorizace.</span><span class="sxs-lookup"><span data-stu-id="e9c89-190">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="e9c89-191">Autorizace na základě role, použít `Roles` parametr:</span><span class="sxs-lookup"><span data-stu-id="e9c89-191">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="e9c89-192">Další informace naleznete v tématu <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="e9c89-192">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="e9c89-193">Na základě zásad autorizace, použijte `Policy` parametr:</span><span class="sxs-lookup"><span data-stu-id="e9c89-193">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="e9c89-194">Autorizace na základě deklarací identity je zvláštní případ autorizace na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="e9c89-194">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="e9c89-195">Například můžete definovat zásady, které vyžaduje, aby uživatelé měli určité deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="e9c89-195">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="e9c89-196">Další informace naleznete v tématu <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="e9c89-196">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="e9c89-197">Tato rozhraní API je možné v Blazor serverové nebo klientské aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="e9c89-197">These APIs can be used in either Blazor server-side or Blazor client-side apps.</span></span>

<span data-ttu-id="e9c89-198">Pokud ani `Roles` ani `Policy` není zadána, `AuthorizeView` využívá výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="e9c89-198">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="e9c89-199">Obsah zobrazený při asynchronním ověřování</span><span class="sxs-lookup"><span data-stu-id="e9c89-199">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="e9c89-200">Blazor umožňuje pro ověřování stavu bude určen *asynchronně*.</span><span class="sxs-lookup"><span data-stu-id="e9c89-200">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="e9c89-201">Primární scénáře pro tento přístup je v aplikacích Blazor na straně klienta, které vytvoříte žádost o externí koncový bod pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="e9c89-201">The primary scenario for this approach is in Blazor client-side apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="e9c89-202">Probíhá ověřování, `AuthorizeView` žádný obsah se zobrazí ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="e9c89-202">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="e9c89-203">Chcete-li zobrazit obsah, když dojde k ověření, použijte `<Authorizing>` element:</span><span class="sxs-lookup"><span data-stu-id="e9c89-203">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="e9c89-204">Tento přístup se obvykle pro Blazor serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="e9c89-204">This approach isn't normally applicable to Blazor server-side apps.</span></span> <span data-ttu-id="e9c89-205">Stav ověřování Blazor serverové aplikace vědět, co nejdříve pokládáme stav, stav.</span><span class="sxs-lookup"><span data-stu-id="e9c89-205">Blazor server-side apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="e9c89-206">`Authorizing` obsah lze zadat v aplikaci na straně serveru Blazor `AuthorizeView` součásti, ale obsah je nikdy zobrazeny.</span><span class="sxs-lookup"><span data-stu-id="e9c89-206">`Authorizing` content can be provided in a Blazor server-side app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="e9c89-207">Atribut [Povolit]</span><span class="sxs-lookup"><span data-stu-id="e9c89-207">[Authorize] attribute</span></span>

<span data-ttu-id="e9c89-208">Stejně jako aplikace můžete použít `[Authorize]` s kontroler MVC nebo stránky Razor `[Authorize]` lze také použít s komponentami Razor:</span><span class="sxs-lookup"><span data-stu-id="e9c89-208">Just like an app can use `[Authorize]` with an MVC controller or Razor page, `[Authorize]` can also be used with Razor Components:</span></span>

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="e9c89-209">Používejte pouze `[Authorize]` na `@page` součásti kontaktovat prostřednictvím směrovače Blazor.</span><span class="sxs-lookup"><span data-stu-id="e9c89-209">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="e9c89-210">Se autorizace provádí pouze jako určitý aspekt směrování a *není* pro podřízené součásti vykreslen v rámci stránky.</span><span class="sxs-lookup"><span data-stu-id="e9c89-210">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="e9c89-211">Chcete-li povolit zobrazení konkrétní součástí v rámci stránky, použijte `AuthorizeView` místo.</span><span class="sxs-lookup"><span data-stu-id="e9c89-211">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="e9c89-212">Budete muset přidat `@using Microsoft.AspNetCore.Authorization` součásti nebo položky *_Imports.razor* souboru v pořadí pro komponentu pro kompilaci.</span><span class="sxs-lookup"><span data-stu-id="e9c89-212">You may need to add `@using Microsoft.AspNetCore.Authorization` either to the component or to the *_Imports.razor* file in order for the component to compile.</span></span>

<span data-ttu-id="e9c89-213">`[Authorize]` Atribut také podporuje ověřování na základě rolí nebo na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="e9c89-213">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="e9c89-214">Autorizace na základě role, použít `Roles` parametr:</span><span class="sxs-lookup"><span data-stu-id="e9c89-214">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="e9c89-215">Na základě zásad autorizace, použijte `Policy` parametr:</span><span class="sxs-lookup"><span data-stu-id="e9c89-215">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="e9c89-216">Pokud ani `Roles` ani `Policy` není zadána, `[Authorize]` využívá výchozí zásady, které ve výchozím nastavení se zachází:</span><span class="sxs-lookup"><span data-stu-id="e9c89-216">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="e9c89-217">Ověřeným uživatelům (přihlášeni), jako oprávněnou.</span><span class="sxs-lookup"><span data-stu-id="e9c89-217">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="e9c89-218">Neověřené uživatele (podepsané out) jako neověřené.</span><span class="sxs-lookup"><span data-stu-id="e9c89-218">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="e9c89-219">Přizpůsobit neoprávněné obsah s komponentou směrovače</span><span class="sxs-lookup"><span data-stu-id="e9c89-219">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="e9c89-220">`Router` Komponenta umožňuje aplikaci určit vlastní obsah, pokud:</span><span class="sxs-lookup"><span data-stu-id="e9c89-220">The `Router` component allows the app to specify custom content if:</span></span>

* <span data-ttu-id="e9c89-221">Obsah nebyl nalezen.</span><span class="sxs-lookup"><span data-stu-id="e9c89-221">Content isn't found.</span></span>
* <span data-ttu-id="e9c89-222">Uživatel `[Authorize]` podmínku použít pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="e9c89-222">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="e9c89-223">`[Authorize]` Se věnuje atribut [atribut [Authorize]](#authorize-attribute) části.</span><span class="sxs-lookup"><span data-stu-id="e9c89-223">The `[Authorize]` attribute is covered in the [[Authorize] attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="e9c89-224">Probíhá asynchronní ověřování.</span><span class="sxs-lookup"><span data-stu-id="e9c89-224">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="e9c89-225">Ve výchozí šablona Blazor projektu na straně serveru *App.razor* souboru ukazuje, jak nastavit vlastní obsah:</span><span class="sxs-lookup"><span data-stu-id="e9c89-225">In the default Blazor server-side project template, the *App.razor* file demonstrates how to set custom content:</span></span>

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        <NotFoundContent>
            <h1>Sorry</h1>
            <p>Sorry, there's nothing at this address.</p>
        </NotFoundContent>
        <NotAuthorizedContent>
            <h1>Sorry</h1>
            <p>You're not authorized to reach this page.</p>
            <p>You may need to log in as a different user.</p>
        </NotAuthorizedContent>
        <AuthorizingContent>
            <h1>Authentication in progress</h1>
            <p>Only visible while authentication is in progress.</p>
        </AuthorizingContent>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="e9c89-226">Obsah `<NotFoundContent>`, `<NotAuthorizedContent>`, a `<AuthorizingContent>` může obsahovat libovolné položky, jako další interaktivní komponenty.</span><span class="sxs-lookup"><span data-stu-id="e9c89-226">The content of `<NotFoundContent>`, `<NotAuthorizedContent>`, and `<AuthorizingContent>` can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="e9c89-227">Pokud `<NotAuthorizedContent>` není zadán, směrovač používá záložní následující zpráva:</span><span class="sxs-lookup"><span data-stu-id="e9c89-227">If `<NotAuthorizedContent>` isn't specified, the router uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="e9c89-228">Oznámení o změně stavu ověřování</span><span class="sxs-lookup"><span data-stu-id="e9c89-228">Notification about authentication state changes</span></span>

<span data-ttu-id="e9c89-229">Pokud aplikace zjistí, že došlo ke změně podkladová data ověření stavu (například proto odhlášení uživatele nebo jiný uživatel změnil své role), vlastní `AuthenticationStateProvider` můžete případně vyvolat metodu `NotifyAuthenticationStateChanged` na `AuthenticationStateProvider` základní Třída.</span><span class="sxs-lookup"><span data-stu-id="e9c89-229">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="e9c89-230">To upozorní spotřebitelé dat stavu ověřování (například `AuthorizeView`) k rerender nová data.</span><span class="sxs-lookup"><span data-stu-id="e9c89-230">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="e9c89-231">Procesní logiky</span><span class="sxs-lookup"><span data-stu-id="e9c89-231">Procedural logic</span></span>

<span data-ttu-id="e9c89-232">Pokud aplikace je potřeba zkontrolovat autorizační pravidla, jako součást procesní logiky, pomocí kaskádových akcí parametr typu `Task<AuthenticationState>` získat uživatele <xref:System.Security.Claims.ClaimsPrincipal>.</span><span class="sxs-lookup"><span data-stu-id="e9c89-232">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e9c89-233">`Task<AuthenticationState>` je možné kombinovat s dalšími službami, jako například `IAuthorizationService`, k vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="e9c89-233">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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

## <a name="authorization-in-blazor-client-side-apps"></a><span data-ttu-id="e9c89-234">Autorizace v aplikacích Blazor na straně klienta</span><span class="sxs-lookup"><span data-stu-id="e9c89-234">Authorization in Blazor client-side apps</span></span>

<span data-ttu-id="e9c89-235">V aplikacích Blazor na straně klienta lze vynechat kontroly autorizace vzhledem k tomu, že veškerý kód na straně klienta je možné upravovat prostřednictvím uživatelů.</span><span class="sxs-lookup"><span data-stu-id="e9c89-235">In Blazor client-side apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="e9c89-236">Totéž platí pro všechny aplikace na straně klienta technologií, jako je JavaScript SPA rozhraní nebo nativní aplikace pro libovolný operační systém.</span><span class="sxs-lookup"><span data-stu-id="e9c89-236">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="e9c89-237">**Vždy provádějte kontroly autorizace na serveru v rámci žádné koncové body rozhraní API přistupuje aplikace na straně klienta.**</span><span class="sxs-lookup"><span data-stu-id="e9c89-237">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="e9c89-238">Řešení potíží s chybami</span><span class="sxs-lookup"><span data-stu-id="e9c89-238">Troubleshoot errors</span></span>

<span data-ttu-id="e9c89-239">Běžné chyby:</span><span class="sxs-lookup"><span data-stu-id="e9c89-239">Common errors:</span></span>

* <span data-ttu-id="e9c89-240">**Vyžaduje ověřování šablony parametr typu úloh\<AuthenticationState >. Zvažte použití CascadingAuthenticationState zadat.**</span><span class="sxs-lookup"><span data-stu-id="e9c89-240">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="e9c89-241">**`null` je přijímána hodnota pro `authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="e9c89-241">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="e9c89-242">Je pravděpodobné, že projekt nebyl vytvořen pomocí šablony Blazor na straně serveru s povoleným ověřováním.</span><span class="sxs-lookup"><span data-stu-id="e9c89-242">It's likely that the project wasn't created using a Blazor server-side template with authentication enabled.</span></span> <span data-ttu-id="e9c89-243">Zabalení `<CascadingAuthenticationState>` některé části uživatelského rozhraní stromu, například v *App.razor* následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="e9c89-243">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="e9c89-244">`CascadingAuthenticationState` Poskytuje `Task<AuthenticationState>` šablony parametr, který pak přijme ze základního `AuthenticationStateProvider` DI služby.</span><span class="sxs-lookup"><span data-stu-id="e9c89-244">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e9c89-245">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e9c89-245">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
