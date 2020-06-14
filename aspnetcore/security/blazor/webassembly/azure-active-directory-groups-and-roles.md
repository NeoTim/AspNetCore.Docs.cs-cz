---
title: ASP.NET Core Blazor WebAssembly s Azure Active Directorymi skupinami a rolemi
author: guardrex
description: Naučte se, jak nakonfigurovat Blazor WebAssembly pro použití Azure Active Directorych skupin a rolí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/aad-groups-roles
ms.openlocfilehash: 87cdf02a6f6babc869d90658e6a7cd54db73bb68
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756025"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="1b0a8-103">Skupiny Azure AD, role pro správu a uživatelsky definované role</span><span class="sxs-lookup"><span data-stu-id="1b0a8-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="1b0a8-104">Od [Luke Latham](https://github.com/guardrex) a [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="1b0a8-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="1b0a8-105">Azure Active Directory (AAD) poskytuje několik autorizačních přístupů, které lze kombinovat s ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="1b0a8-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="1b0a8-106">Uživatelem definované skupiny</span><span class="sxs-lookup"><span data-stu-id="1b0a8-106">User-defined groups</span></span>
  * <span data-ttu-id="1b0a8-107">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="1b0a8-107">Security</span></span>
  * <span data-ttu-id="1b0a8-108">O365</span><span class="sxs-lookup"><span data-stu-id="1b0a8-108">O365</span></span>
  * <span data-ttu-id="1b0a8-109">Distribuce</span><span class="sxs-lookup"><span data-stu-id="1b0a8-109">Distribution</span></span>
* <span data-ttu-id="1b0a8-110">Role</span><span class="sxs-lookup"><span data-stu-id="1b0a8-110">Roles</span></span>
  * <span data-ttu-id="1b0a8-111">Předdefinované role pro správu</span><span class="sxs-lookup"><span data-stu-id="1b0a8-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="1b0a8-112">Uživatelsky definované role</span><span class="sxs-lookup"><span data-stu-id="1b0a8-112">User-defined roles</span></span>

<span data-ttu-id="1b0a8-113">Pokyny v tomto článku se týkají Blazor scénářů nasazení AAD WebAssembly popsaných v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="1b0a8-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="1b0a8-114">Samostatná aplikace využívající účty Microsoft</span><span class="sxs-lookup"><span data-stu-id="1b0a8-114">Standalone with Microsoft Accounts</span></span>](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="1b0a8-115">Samostatná aplikace využívající Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="1b0a8-115">Standalone with AAD</span></span>](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="1b0a8-116">Hostovaná aplikace využívající Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="1b0a8-116">Hosted with AAD</span></span>](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="1b0a8-117">Uživatelsky definované skupiny a předdefinované role pro správu</span><span class="sxs-lookup"><span data-stu-id="1b0a8-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="1b0a8-118">Postup konfigurace aplikace v Azure Portal k poskytnutí `groups` deklarace identity členství najdete v následujících článcích Azure.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="1b0a8-119">Přiřaďte uživatele k uživatelem definovaným skupinám AAD a integrovaným rolím pro správu.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="1b0a8-120">Role používající skupiny zabezpečení Azure AD</span><span class="sxs-lookup"><span data-stu-id="1b0a8-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="1b0a8-121">groupMembershipClaims – atribut</span><span class="sxs-lookup"><span data-stu-id="1b0a8-121">groupMembershipClaims attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="1b0a8-122">V následujících příkladech se předpokládá, že je uživatel přiřazený k předdefinované roli *správce fakturace* AAD.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="1b0a8-123">Jediná `groups` deklarace ODESÍLANÁ AAD prezentuje skupiny uživatelů a role jako ID objektů (GUID) v poli JSON.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="1b0a8-124">Aplikace musí převést pole JSON skupin a rolí na jednotlivé `group` deklarace identity, pro které může aplikace sestavovat [zásady](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="1b0a8-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="1b0a8-125">Rozšíříte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> tak, aby zahrnovalo vlastnosti pole pro skupiny a role.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="1b0a8-126">*CustomUserAccount.cs*:</span><span class="sxs-lookup"><span data-stu-id="1b0a8-126">*CustomUserAccount.cs*:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

<span data-ttu-id="1b0a8-127">Vytvořte vlastní objekt pro vytváření uživatelů v samostatné aplikaci nebo klientské aplikaci hostovaného řešení.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="1b0a8-128">Následující objekt pro vytváření je také nakonfigurovaný pro zpracování `roles` polí deklarací identity, která jsou popsaná v části [uživatelsky definované role](#user-defined-roles) :</span><span class="sxs-lookup"><span data-stu-id="1b0a8-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="1b0a8-129">Není nutné zadávat kód pro odebrání původní `groups` deklarace identity, protože je automaticky odebrána rozhraním Framework.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="1b0a8-130">Registrace továrny v `Program.Main` (*program.cs*) samostatné aplikace nebo klientské aplikace hostovaného řešení:</span><span class="sxs-lookup"><span data-stu-id="1b0a8-130">Register the factory in `Program.Main` (*Program.cs*) of the standalone app or Client app of a Hosted solution:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="1b0a8-131">Vytvořte [zásadu](xref:security/authorization/policies) pro každou skupinu nebo roli v `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="1b0a8-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="1b0a8-132">Následující příklad vytvoří zásadu pro předdefinovanou *fakturační roli správce* služby AAD:</span><span class="sxs-lookup"><span data-stu-id="1b0a8-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="1b0a8-133">Úplný seznam ID objektů role AAD najdete v části [ID skupin rolí AAD](#aad-adminstrative-role-group-ids) .</span><span class="sxs-lookup"><span data-stu-id="1b0a8-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="1b0a8-134">V následujících příkladech používá aplikace k autorizaci uživatele předchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="1b0a8-135">[Komponenta AuthorizeView](xref:security/blazor/index#authorizeview-component) spolupracuje s touto zásadou:</span><span class="sxs-lookup"><span data-stu-id="1b0a8-135">The [AuthorizeView component](xref:security/blazor/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="1b0a8-136">Přístup k celé komponentě může být založen na zásadách pomocí [ `[Authorize]` direktivy atributu](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="1b0a8-136">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="1b0a8-137">Pokud uživatel není přihlášený, bude přesměrován na přihlašovací stránku AAD a pak se po přihlášení znovu k součásti.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="1b0a8-138">Kontrolu zásad lze také [provést v kódu s procedurální logikou](xref:security/blazor/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="1b0a8-138">A policy check can also be [performed in code with procedural logic](xref:security/blazor/index#procedural-logic):</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

### <a name="user-defined-roles"></a><span data-ttu-id="1b0a8-139">Uživatelsky definované role</span><span class="sxs-lookup"><span data-stu-id="1b0a8-139">User-defined roles</span></span>

<span data-ttu-id="1b0a8-140">Aplikace zaregistrovaná v AAD se taky dá nakonfigurovat tak, aby používala uživatelsky definované role.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="1b0a8-141">Pokud chcete aplikaci v Azure Portal nakonfigurovat tak, aby poskytovala `roles` deklaraci identity členství, přečtěte si téma [Postup: Přidání rolí aplikace do aplikace a jejich přijetí v tokenu](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="1b0a8-142">Následující příklad předpokládá, že je aplikace nakonfigurovaná se dvěma rolemi:</span><span class="sxs-lookup"><span data-stu-id="1b0a8-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="1b0a8-143">I když nemůžete přiřadit role ke skupinám zabezpečení bez Azure AD Premium účtu, můžete přiřadit uživatele k rolím a získat `roles` deklaraci identity pro uživatele se standardním účtem Azure.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="1b0a8-144">Pokyny v této části nevyžadují účet Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="1b0a8-145">V Azure Portal je přiřazeno více rolí tím, že **_znovu přidáte uživatele_** pro každé přiřazení další role.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="1b0a8-146">Jediná deklarace, kterou `roles` odesílá AAD, prezentuje uživatelsky definované role jako `appRoles` `value` s v poli JSON.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="1b0a8-147">Aplikace musí převést pole rolí JSON na jednotlivé `role` deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="1b0a8-148">V `CustomUserFactory` části [uživatelsky definované skupiny a předdefinované role pro správu AAD](#user-defined-groups-and-built-in-administrative-roles) je nastavené tak, aby se jednalo o `roles` deklaraci identity s hodnotou pole JSON.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="1b0a8-149">Přidejte a zaregistrujte se `CustomUserFactory` do samostatné aplikace nebo klientské aplikace hostovaného řešení, jak je znázorněno v části [uživatelsky definované skupiny a předdefinované role pro správu AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="1b0a8-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="1b0a8-150">Není nutné zadávat kód pro odebrání původní `roles` deklarace identity, protože je automaticky odebrána rozhraním Framework.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="1b0a8-151">V `Program.Main` samostatné aplikaci nebo klientské aplikaci hostovaného řešení zadejte deklaraci identity s názvem `role` jako deklaraci identity role:</span><span class="sxs-lookup"><span data-stu-id="1b0a8-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="1b0a8-152">Přístupy k autorizaci komponent jsou v tuto chvíli funkční.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="1b0a8-153">Kterýkoli z autorizačních mechanismů v součástech může použít `admin` roli k autorizaci uživatele:</span><span class="sxs-lookup"><span data-stu-id="1b0a8-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="1b0a8-154">[AuthorizeView – komponenta](xref:security/blazor/index#authorizeview-component) (příklad: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="1b0a8-154">[AuthorizeView component](xref:security/blazor/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="1b0a8-155">[ `[Authorize]` direktiva atributu](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (příklad: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="1b0a8-155">[`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="1b0a8-156">[Procedurální logika](xref:security/blazor/index#procedural-logic) (příklad: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="1b0a8-156">[Procedural logic](xref:security/blazor/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="1b0a8-157">Podporuje se vícenásobné testy rolí:</span><span class="sxs-lookup"><span data-stu-id="1b0a8-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="1b0a8-158">ID skupiny rolí pro správu AAD</span><span class="sxs-lookup"><span data-stu-id="1b0a8-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="1b0a8-159">ID objektů prezentovaná v následující tabulce se používají k vytváření [zásad](xref:security/authorization/policies) pro `group` deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="1b0a8-160">Zásady umožňují aplikaci ověřovat uživatele pro různé aktivity v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1b0a8-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="1b0a8-161">Další informace najdete v části [uživatelsky definované skupiny a předdefinované role pro správu AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="1b0a8-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="1b0a8-162">Role správce AAD</span><span class="sxs-lookup"><span data-stu-id="1b0a8-162">AAD Administrative Role</span></span> | <span data-ttu-id="1b0a8-163">ID objektu</span><span class="sxs-lookup"><span data-stu-id="1b0a8-163">Object ID</span></span>
--- | ---
<span data-ttu-id="1b0a8-164">Správce aplikace</span><span class="sxs-lookup"><span data-stu-id="1b0a8-164">Application administrator</span></span> | <span data-ttu-id="1b0a8-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="1b0a8-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="1b0a8-166">Vývojář aplikace</span><span class="sxs-lookup"><span data-stu-id="1b0a8-166">Application developer</span></span> | <span data-ttu-id="1b0a8-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="1b0a8-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="1b0a8-168">Správce ověřování</span><span class="sxs-lookup"><span data-stu-id="1b0a8-168">Authentication administrator</span></span> | <span data-ttu-id="1b0a8-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="1b0a8-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="1b0a8-170">Správce Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="1b0a8-170">Azure DevOps administrator</span></span> | <span data-ttu-id="1b0a8-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="1b0a8-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="1b0a8-172">Správce Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="1b0a8-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="1b0a8-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="1b0a8-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="1b0a8-174">B2C IEF, správce sady klíčů</span><span class="sxs-lookup"><span data-stu-id="1b0a8-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="1b0a8-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="1b0a8-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="1b0a8-176">Správce zásad IEF B2C</span><span class="sxs-lookup"><span data-stu-id="1b0a8-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="1b0a8-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="1b0a8-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="1b0a8-178">Správce toku B2C uživatele</span><span class="sxs-lookup"><span data-stu-id="1b0a8-178">B2C user flow administrator</span></span> | <span data-ttu-id="1b0a8-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="1b0a8-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="1b0a8-180">Správce atributů toku uživatele B2C</span><span class="sxs-lookup"><span data-stu-id="1b0a8-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="1b0a8-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="1b0a8-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="1b0a8-182">Správce fakturace</span><span class="sxs-lookup"><span data-stu-id="1b0a8-182">Billing administrator</span></span> | <span data-ttu-id="1b0a8-183">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="1b0a8-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="1b0a8-184">Správce cloudové aplikace</span><span class="sxs-lookup"><span data-stu-id="1b0a8-184">Cloud application administrator</span></span> | <span data-ttu-id="1b0a8-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="1b0a8-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="1b0a8-186">Správce cloudového zařízení</span><span class="sxs-lookup"><span data-stu-id="1b0a8-186">Cloud device administrator</span></span> | <span data-ttu-id="1b0a8-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="1b0a8-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="1b0a8-188">Správce dodržování předpisů</span><span class="sxs-lookup"><span data-stu-id="1b0a8-188">Compliance administrator</span></span> | <span data-ttu-id="1b0a8-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="1b0a8-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="1b0a8-190">Správce dat dodržování předpisů</span><span class="sxs-lookup"><span data-stu-id="1b0a8-190">Compliance data administrator</span></span> | <span data-ttu-id="1b0a8-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="1b0a8-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="1b0a8-192">Správce podmíněného přístupu</span><span class="sxs-lookup"><span data-stu-id="1b0a8-192">Conditional Access administrator</span></span> | <span data-ttu-id="1b0a8-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="1b0a8-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="1b0a8-194">Schvalovatel přístupu k bezpečnostnímu modulu zákazníka</span><span class="sxs-lookup"><span data-stu-id="1b0a8-194">Customer LockBox access approver</span></span> | <span data-ttu-id="1b0a8-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="1b0a8-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="1b0a8-196">Správce Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="1b0a8-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="1b0a8-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="1b0a8-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="1b0a8-198">Čtečky adresářů</span><span class="sxs-lookup"><span data-stu-id="1b0a8-198">Directory readers</span></span> | <span data-ttu-id="1b0a8-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="1b0a8-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="1b0a8-200">Správce Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="1b0a8-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="1b0a8-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="1b0a8-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="1b0a8-202">Správce Exchange</span><span class="sxs-lookup"><span data-stu-id="1b0a8-202">Exchange administrator</span></span> | <span data-ttu-id="1b0a8-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="1b0a8-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="1b0a8-204">IdentitySprávce externího poskytovatele</span><span class="sxs-lookup"><span data-stu-id="1b0a8-204">External Identity Provider administrator</span></span> | <span data-ttu-id="1b0a8-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="1b0a8-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="1b0a8-206">Globální správce</span><span class="sxs-lookup"><span data-stu-id="1b0a8-206">Global administrator</span></span> | <span data-ttu-id="1b0a8-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="1b0a8-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="1b0a8-208">Globální čtenář</span><span class="sxs-lookup"><span data-stu-id="1b0a8-208">Global reader</span></span> | <span data-ttu-id="1b0a8-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="1b0a8-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="1b0a8-210">Správce skupin</span><span class="sxs-lookup"><span data-stu-id="1b0a8-210">Groups administrator</span></span> | <span data-ttu-id="1b0a8-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="1b0a8-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="1b0a8-212">Pozvánka hosta</span><span class="sxs-lookup"><span data-stu-id="1b0a8-212">Guest inviter</span></span> | <span data-ttu-id="1b0a8-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="1b0a8-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="1b0a8-214">Správce helpdesku</span><span class="sxs-lookup"><span data-stu-id="1b0a8-214">Helpdesk administrator</span></span> | <span data-ttu-id="1b0a8-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="1b0a8-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="1b0a8-216">Správce Intune</span><span class="sxs-lookup"><span data-stu-id="1b0a8-216">Intune administrator</span></span> | <span data-ttu-id="1b0a8-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="1b0a8-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="1b0a8-218">Správce Kaizala</span><span class="sxs-lookup"><span data-stu-id="1b0a8-218">Kaizala administrator</span></span> | <span data-ttu-id="1b0a8-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="1b0a8-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="1b0a8-220">Správce licencí</span><span class="sxs-lookup"><span data-stu-id="1b0a8-220">License administrator</span></span> | <span data-ttu-id="1b0a8-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="1b0a8-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="1b0a8-222">Čtenář ochrany osobních údajů centra zpráv</span><span class="sxs-lookup"><span data-stu-id="1b0a8-222">Message center privacy reader</span></span> | <span data-ttu-id="1b0a8-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="1b0a8-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="1b0a8-224">Čtenář centra zpráv</span><span class="sxs-lookup"><span data-stu-id="1b0a8-224">Message center reader</span></span> | <span data-ttu-id="1b0a8-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="1b0a8-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="1b0a8-226">Správce aplikací Office</span><span class="sxs-lookup"><span data-stu-id="1b0a8-226">Office apps administrator</span></span> | <span data-ttu-id="1b0a8-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="1b0a8-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="1b0a8-228">Správce hesel</span><span class="sxs-lookup"><span data-stu-id="1b0a8-228">Password administrator</span></span> | <span data-ttu-id="1b0a8-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="1b0a8-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="1b0a8-230">Správce Power BI</span><span class="sxs-lookup"><span data-stu-id="1b0a8-230">Power BI administrator</span></span> | <span data-ttu-id="1b0a8-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="1b0a8-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="1b0a8-232">Správce platforma Power</span><span class="sxs-lookup"><span data-stu-id="1b0a8-232">Power platform administrator</span></span> | <span data-ttu-id="1b0a8-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="1b0a8-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="1b0a8-234">Správce privilegovaného ověřování</span><span class="sxs-lookup"><span data-stu-id="1b0a8-234">Privileged authentication administrator</span></span> | <span data-ttu-id="1b0a8-235">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="1b0a8-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="1b0a8-236">Správce privilegovaných rolí</span><span class="sxs-lookup"><span data-stu-id="1b0a8-236">Privileged role administrator</span></span> | <span data-ttu-id="1b0a8-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="1b0a8-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="1b0a8-238">Čtečka sestav</span><span class="sxs-lookup"><span data-stu-id="1b0a8-238">Reports reader</span></span> | <span data-ttu-id="1b0a8-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="1b0a8-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="1b0a8-240">Správce hledání</span><span class="sxs-lookup"><span data-stu-id="1b0a8-240">Search administrator</span></span> | <span data-ttu-id="1b0a8-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="1b0a8-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="1b0a8-242">Editor hledání</span><span class="sxs-lookup"><span data-stu-id="1b0a8-242">Search editor</span></span> | <span data-ttu-id="1b0a8-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="1b0a8-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="1b0a8-244">Správce zabezpečení</span><span class="sxs-lookup"><span data-stu-id="1b0a8-244">Security administrator</span></span> | <span data-ttu-id="1b0a8-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="1b0a8-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="1b0a8-246">Operátor zabezpečení</span><span class="sxs-lookup"><span data-stu-id="1b0a8-246">Security operator</span></span> | <span data-ttu-id="1b0a8-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="1b0a8-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="1b0a8-248">Čtenář zabezpečení</span><span class="sxs-lookup"><span data-stu-id="1b0a8-248">Security reader</span></span> | <span data-ttu-id="1b0a8-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="1b0a8-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="1b0a8-250">Správce služeb</span><span class="sxs-lookup"><span data-stu-id="1b0a8-250">Service support administrator</span></span> | <span data-ttu-id="1b0a8-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="1b0a8-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="1b0a8-252">Správce SharePointu</span><span class="sxs-lookup"><span data-stu-id="1b0a8-252">SharePoint administrator</span></span> | <span data-ttu-id="1b0a8-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="1b0a8-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="1b0a8-254">Správce Skypu pro firmy</span><span class="sxs-lookup"><span data-stu-id="1b0a8-254">Skype for Business administrator</span></span> | <span data-ttu-id="1b0a8-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="1b0a8-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="1b0a8-256">Správce komunikace týmů</span><span class="sxs-lookup"><span data-stu-id="1b0a8-256">Teams Communications Administrator</span></span> | <span data-ttu-id="1b0a8-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="1b0a8-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="1b0a8-258">Týmy Communications support inženýr</span><span class="sxs-lookup"><span data-stu-id="1b0a8-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="1b0a8-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="1b0a8-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="1b0a8-260">Týmy – specialisté komunikace</span><span class="sxs-lookup"><span data-stu-id="1b0a8-260">Teams Communications Specialist</span></span> | <span data-ttu-id="1b0a8-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="1b0a8-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="1b0a8-262">Správce služby Teams</span><span class="sxs-lookup"><span data-stu-id="1b0a8-262">Teams Service Administrator</span></span> | <span data-ttu-id="1b0a8-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="1b0a8-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="1b0a8-264">Správce uživatelů</span><span class="sxs-lookup"><span data-stu-id="1b0a8-264">User administrator</span></span> | <span data-ttu-id="1b0a8-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="1b0a8-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b0a8-266">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1b0a8-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
