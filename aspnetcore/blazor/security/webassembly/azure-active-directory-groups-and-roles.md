---
title: ASP.NET Core Blazor WebAssembly se Azure Active Directorymi skupinami a rolemi
author: guardrex
description: Naučte se konfigurovat Blazor WebAssembly , aby používaly Azure Active Directory skupiny a role.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 81114768a3600544dda46efbc886e2f56932aba7
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592926"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="dfbe6-103">Skupiny Azure AD, role pro správu a uživatelsky definované role</span><span class="sxs-lookup"><span data-stu-id="dfbe6-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="dfbe6-104">Od [Luke Latham](https://github.com/guardrex) a [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="dfbe6-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="dfbe6-105">Azure Active Directory (AAD) poskytuje několik autorizačních přístupů, které je možné kombinovat ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="dfbe6-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="dfbe6-106">Uživatelem definované skupiny</span><span class="sxs-lookup"><span data-stu-id="dfbe6-106">User-defined groups</span></span>
  * <span data-ttu-id="dfbe6-107">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="dfbe6-107">Security</span></span>
  * <span data-ttu-id="dfbe6-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="dfbe6-108">Microsoft 365</span></span>
  * <span data-ttu-id="dfbe6-109">Distribuce</span><span class="sxs-lookup"><span data-stu-id="dfbe6-109">Distribution</span></span>
* <span data-ttu-id="dfbe6-110">Role</span><span class="sxs-lookup"><span data-stu-id="dfbe6-110">Roles</span></span>
  * <span data-ttu-id="dfbe6-111">Předdefinované role pro správu</span><span class="sxs-lookup"><span data-stu-id="dfbe6-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="dfbe6-112">Uživatelsky definované role</span><span class="sxs-lookup"><span data-stu-id="dfbe6-112">User-defined roles</span></span>

<span data-ttu-id="dfbe6-113">Pokyny v tomto článku se týkají Blazor WebAssembly scénářů nasazení AAD popsaných v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="dfbe6-114">Samostatná aplikace využívající účty Microsoft</span><span class="sxs-lookup"><span data-stu-id="dfbe6-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="dfbe6-115">Samostatná aplikace využívající Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="dfbe6-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="dfbe6-116">Hostovaná aplikace využívající Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="dfbe6-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="dfbe6-117">Oprávnění rozhraní API pro Microsoft Graph</span><span class="sxs-lookup"><span data-stu-id="dfbe6-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="dfbe6-118">Pro každého uživatele aplikace s více než pěti integrovanou rolí správce AAD a členstvím ve skupině zabezpečení je vyžadováno volání [rozhraní API Microsoft Graph](/graph/use-the-api) .</span><span class="sxs-lookup"><span data-stu-id="dfbe6-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five built-in AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="dfbe6-119">Pokud chcete povolit Graph API volání, poskytněte samostatnou nebo klientskou aplikaci hostovaného Blazor řešení kterékoli z následujících [Graph API oprávnění](/graph/permissions-reference) v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="dfbe6-120">`Directory.Read.All` je oprávnění s minimálním oprávněním a je použito pro příklad popsaný v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="dfbe6-121">Uživatelsky definované skupiny a předdefinované role pro správu</span><span class="sxs-lookup"><span data-stu-id="dfbe6-121">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="dfbe6-122">Postup konfigurace aplikace v Azure Portal k poskytnutí `groups` deklarace identity členství najdete v následujících článcích Azure.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="dfbe6-123">Přiřaďte uživatele k uživatelem definovaným skupinám AAD a integrovaným rolím pro správu.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-123">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="dfbe6-124">Role používající skupiny zabezpečení Azure AD</span><span class="sxs-lookup"><span data-stu-id="dfbe6-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="dfbe6-125">`groupMembershipClaims` přidělen</span><span class="sxs-lookup"><span data-stu-id="dfbe6-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="dfbe6-126">V následujících příkladech se předpokládá, že je uživatel přiřazený k předdefinované roli *správce fakturace* AAD.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-126">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="dfbe6-127">Jediná `groups` deklarace ODESÍLANÁ AAD prezentuje skupiny uživatelů a role jako ID objektů (GUID) v poli JSON.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="dfbe6-128">Aplikace musí převést pole JSON skupin a rolí na jednotlivé `group` deklarace identity, pro které může aplikace sestavovat [zásady](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="dfbe6-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="dfbe6-129">Když počet přiřazených předdefinovaných rolí správy Azure a uživatelem definovaných skupin přesáhne pět, AAD pošle `hasgroups` deklaraci identity s `true` hodnotou namísto odeslání `groups` deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-129">When the number of assigned built-in Azure Administrative Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="dfbe6-130">Každá aplikace, která může mít více než pět rolí a skupin přiřazených uživatelům, musí vytvořit samostatné Graph API volání, aby získala role a skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="dfbe6-131">Ukázková implementace uvedená v tomto článku se zabývá tímto scénářem.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="dfbe6-132">Další informace najdete v `groups` `hasgroups` článku o deklaracích identity v tématu [tokeny přístupu k platformě Microsoft Identity Platform: deklarace datové části](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="dfbe6-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="dfbe6-133">Rozšíříte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> tak, aby zahrnovalo vlastnosti pole pro skupiny a role.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="dfbe6-134">Přiřaďte k jednotlivým vlastnostem prázdné pole, aby kontrola `null` není nutná, pokud jsou tyto vlastnosti použity ve `foreach` smyčcech později.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="dfbe6-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-135">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

<span data-ttu-id="dfbe6-136">V samostatné aplikaci nebo klientské aplikaci hostovaného Blazor řešení vytvořte vlastní <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> třídu.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="dfbe6-137">Použijte správný obor (oprávnění) pro Graph API volání, která získávají informace o rolích a skupinách.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="dfbe6-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

<span data-ttu-id="dfbe6-139">V `Program.Main` ( `Program.cs` ) přidejte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementační službu a přidejte s názvem <xref:System.Net.Http.HttpClient> pro vytváření Graph APIch požadavků.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="dfbe6-140">Následující příklad pojmenuje klienta `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="dfbe6-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="dfbe6-141">Vytvořte třídy adresářových objektů AAD pro příjem rolí a skupin Open Data Protocol (OData) z Graph API volání.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="dfbe6-142">OData přijde ve formátu JSON a volání <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> naplní instanci `DirectoryObjects` třídy.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="dfbe6-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-143">`DirectoryObjects.cs`:</span></span>

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

<span data-ttu-id="dfbe6-144">Vytvořte vlastní objekt pro vytváření uživatelů, který bude zpracovávat deklarace rolí a skupin.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="dfbe6-145">Následující příklad implementace také zpracovává `roles` pole deklarací, které je popsáno v části [uživatelsky definované role](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="dfbe6-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="dfbe6-146">Pokud `hasgroups` je tato deklarace identity přítomná, <xref:System.Net.Http.HttpClient> použije se k tomu autorizovaný požadavek na Graph API, aby se získaly role a skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="dfbe6-147">Tato implementace používá Identity koncový bod platformy Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([dokumentace k rozhraní API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="dfbe6-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="dfbe6-148">Pokyny v tomto tématu budou aktualizovány pro Identity verzi v 2.0, pokud jsou balíčky MSAL upgradovány pro verze 2.0.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="dfbe6-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-149">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
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

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="dfbe6-150">Není nutné zadávat kód pro odebrání původní `groups` deklarace identity, pokud je k dispozici, protože je automaticky odebrána rozhraním.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="dfbe6-151">Přístup v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="dfbe6-152">Přidá vlastní <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> třídu pro připojení přístupových tokenů k odchozím žádostem.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="dfbe6-153">Přidá s názvem <xref:System.Net.Http.HttpClient> , aby vyžádal webové rozhraní API na zabezpečený externí koncový bod webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="dfbe6-154"><xref:System.Net.Http.HttpClient>K provádění autorizovaných požadavků používá pojmenovaný.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="dfbe6-155">Obecné pokrytí tohoto přístupu najdete v <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> článku.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="dfbe6-156">Zaregistrujte továrnu v `Program.Main` ( `Program.cs` ) samostatné aplikace nebo klientské aplikace hostovaného Blazor řešení.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="dfbe6-157">Souhlas s `Directory.Read.All` oborem oprávnění jako další obor pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="dfbe6-158">Vytvořte [zásadu](xref:security/authorization/policies) pro každou skupinu nebo roli v `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="dfbe6-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="dfbe6-159">Následující příklad vytvoří zásadu pro předdefinovanou *fakturační roli správce* služby AAD:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-159">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="dfbe6-160">Úplný seznam ID objektů role AAD najdete v části [ID skupin rolí AAD](#aad-adminstrative-role-group-ids) .</span><span class="sxs-lookup"><span data-stu-id="dfbe6-160">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="dfbe6-161">V následujících příkladech používá aplikace k autorizaci uživatele předchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="dfbe6-162">[ `AuthorizeView` Komponenta](xref:blazor/security/index#authorizeview-component) spolupracuje se zásadami:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="dfbe6-163">Přístup k celé komponentě může být založen na zásadách pomocí [ `[Authorize]` direktivy atributu](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="dfbe6-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="dfbe6-164">Pokud uživatel není přihlášený, bude přesměrován na přihlašovací stránku AAD a pak se po přihlášení znovu k součásti.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="dfbe6-165">Kontrolu zásad lze také [provést v kódu s procedurální logikou](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="dfbe6-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="dfbe6-166">Uživatelsky definované role</span><span class="sxs-lookup"><span data-stu-id="dfbe6-166">User-defined roles</span></span>

<span data-ttu-id="dfbe6-167">Aplikace zaregistrovaná v AAD se taky dá nakonfigurovat tak, aby používala uživatelsky definované role.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-167">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="dfbe6-168">Pokud chcete aplikaci v Azure Portal nakonfigurovat tak, aby poskytovala `roles` deklaraci identity členství, přečtěte si téma [Postup: Přidání rolí aplikace do aplikace a jejich přijetí v tokenu](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-168">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="dfbe6-169">Následující příklad předpokládá, že je aplikace nakonfigurovaná se dvěma rolemi:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-169">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="dfbe6-170">I když nemůžete přiřadit role ke skupinám zabezpečení bez Azure AD Premium účtu, můžete přiřadit uživatele k rolím a získat `roles` deklaraci identity pro uživatele se standardním účtem Azure.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-170">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="dfbe6-171">Pokyny v této části nevyžadují účet Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-171">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="dfbe6-172">V Azure Portal je přiřazeno více rolí tím, že **_znovu přidáte uživatele_** pro každé přiřazení další role.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-172">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="dfbe6-173">Jediná deklarace, kterou `roles` odesílá AAD, prezentuje uživatelsky definované role jako `appRoles` `value` s v poli JSON.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-173">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="dfbe6-174">Aplikace musí převést pole rolí JSON na jednotlivé `role` deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-174">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="dfbe6-175">V `CustomUserFactory` části [uživatelsky definované skupiny a předdefinované role pro správu AAD](#user-defined-groups-and-built-in-administrative-roles) je nastavené tak, aby se jednalo o `roles` deklaraci identity s hodnotou pole JSON.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-175">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="dfbe6-176">Přidejte a zaregistrujte se `CustomUserFactory` do samostatné aplikace nebo klientské aplikace hostovaného Blazor řešení, jak je znázorněno v části [uživatelsky definované skupiny a předdefinované role pro správu AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="dfbe6-176">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="dfbe6-177">Není nutné zadávat kód pro odebrání původní `roles` deklarace identity, protože je automaticky odebrána rozhraním Framework.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-177">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="dfbe6-178">V `Program.Main` samostatné aplikaci nebo klientské aplikaci hostovaného Blazor řešení zadejte deklaraci identity s názvem `role` jako deklaraci identity role:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-178">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="dfbe6-179">Přístupy k autorizaci komponent jsou v tuto chvíli funkční.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-179">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="dfbe6-180">Kterýkoli z autorizačních mechanismů v součástech může použít `admin` roli k autorizaci uživatele:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-180">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="dfbe6-181">[ `AuthorizeView` součást](xref:blazor/security/index#authorizeview-component) (příklad: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="dfbe6-181">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="dfbe6-182">[ `[Authorize]` direktiva atributu](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (příklad: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="dfbe6-182">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="dfbe6-183">[Procedurální logika](xref:blazor/security/index#procedural-logic) (příklad: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="dfbe6-183">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="dfbe6-184">Podporuje se vícenásobné testy rolí:</span><span class="sxs-lookup"><span data-stu-id="dfbe6-184">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="dfbe6-185">ID skupiny rolí pro správu AAD</span><span class="sxs-lookup"><span data-stu-id="dfbe6-185">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="dfbe6-186">ID objektů prezentovaná v následující tabulce se používají k vytváření [zásad](xref:security/authorization/policies) pro `group` deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-186">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="dfbe6-187">Zásady umožňují aplikaci ověřovat uživatele pro různé aktivity v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="dfbe6-187">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="dfbe6-188">Další informace najdete v části [uživatelsky definované skupiny a předdefinované role pro správu AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="dfbe6-188">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="dfbe6-189">Role správce AAD</span><span class="sxs-lookup"><span data-stu-id="dfbe6-189">AAD Administrative Role</span></span> | <span data-ttu-id="dfbe6-190">ID objektu</span><span class="sxs-lookup"><span data-stu-id="dfbe6-190">Object ID</span></span>
--- | ---
<span data-ttu-id="dfbe6-191">Správce aplikace</span><span class="sxs-lookup"><span data-stu-id="dfbe6-191">Application administrator</span></span> | <span data-ttu-id="dfbe6-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="dfbe6-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="dfbe6-193">Vývojář aplikace</span><span class="sxs-lookup"><span data-stu-id="dfbe6-193">Application developer</span></span> | <span data-ttu-id="dfbe6-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="dfbe6-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="dfbe6-195">Správce ověřování</span><span class="sxs-lookup"><span data-stu-id="dfbe6-195">Authentication administrator</span></span> | <span data-ttu-id="dfbe6-196">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="dfbe6-196">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="dfbe6-197">Správce Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="dfbe6-197">Azure DevOps administrator</span></span> | <span data-ttu-id="dfbe6-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="dfbe6-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="dfbe6-199">Správce Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="dfbe6-199">Azure Information Protection administrator</span></span> | <span data-ttu-id="dfbe6-200">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="dfbe6-200">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="dfbe6-201">B2C IEF, správce sady klíčů</span><span class="sxs-lookup"><span data-stu-id="dfbe6-201">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="dfbe6-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="dfbe6-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="dfbe6-203">Správce zásad IEF B2C</span><span class="sxs-lookup"><span data-stu-id="dfbe6-203">B2C IEF Policy administrator</span></span> | <span data-ttu-id="dfbe6-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="dfbe6-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="dfbe6-205">Správce toku B2C uživatele</span><span class="sxs-lookup"><span data-stu-id="dfbe6-205">B2C user flow administrator</span></span> | <span data-ttu-id="dfbe6-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="dfbe6-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="dfbe6-207">Správce atributů toku uživatele B2C</span><span class="sxs-lookup"><span data-stu-id="dfbe6-207">B2C user flow attribute administrator</span></span> | <span data-ttu-id="dfbe6-208">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="dfbe6-208">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="dfbe6-209">Správce fakturace</span><span class="sxs-lookup"><span data-stu-id="dfbe6-209">Billing administrator</span></span> | <span data-ttu-id="dfbe6-210">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="dfbe6-210">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="dfbe6-211">Správce cloudové aplikace</span><span class="sxs-lookup"><span data-stu-id="dfbe6-211">Cloud application administrator</span></span> | <span data-ttu-id="dfbe6-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="dfbe6-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="dfbe6-213">Správce cloudového zařízení</span><span class="sxs-lookup"><span data-stu-id="dfbe6-213">Cloud device administrator</span></span> | <span data-ttu-id="dfbe6-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="dfbe6-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="dfbe6-215">Správce dodržování předpisů</span><span class="sxs-lookup"><span data-stu-id="dfbe6-215">Compliance administrator</span></span> | <span data-ttu-id="dfbe6-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="dfbe6-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="dfbe6-217">Správce dat dodržování předpisů</span><span class="sxs-lookup"><span data-stu-id="dfbe6-217">Compliance data administrator</span></span> | <span data-ttu-id="dfbe6-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="dfbe6-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="dfbe6-219">Správce podmíněného přístupu</span><span class="sxs-lookup"><span data-stu-id="dfbe6-219">Conditional Access administrator</span></span> | <span data-ttu-id="dfbe6-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="dfbe6-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="dfbe6-221">Schvalovatel přístupu k bezpečnostnímu modulu zákazníka</span><span class="sxs-lookup"><span data-stu-id="dfbe6-221">Customer LockBox access approver</span></span> | <span data-ttu-id="dfbe6-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="dfbe6-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="dfbe6-223">Správce Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="dfbe6-223">Desktop Analytics administrator</span></span> | <span data-ttu-id="dfbe6-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="dfbe6-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="dfbe6-225">Čtečky adresářů</span><span class="sxs-lookup"><span data-stu-id="dfbe6-225">Directory readers</span></span> | <span data-ttu-id="dfbe6-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="dfbe6-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="dfbe6-227">Správce Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="dfbe6-227">Dynamics 365 administrator</span></span> | <span data-ttu-id="dfbe6-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="dfbe6-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="dfbe6-229">Správce Exchange</span><span class="sxs-lookup"><span data-stu-id="dfbe6-229">Exchange administrator</span></span> | <span data-ttu-id="dfbe6-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="dfbe6-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="dfbe6-231">IdentitySprávce externího poskytovatele</span><span class="sxs-lookup"><span data-stu-id="dfbe6-231">External Identity Provider administrator</span></span> | <span data-ttu-id="dfbe6-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="dfbe6-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="dfbe6-233">Globální správce</span><span class="sxs-lookup"><span data-stu-id="dfbe6-233">Global administrator</span></span> | <span data-ttu-id="dfbe6-234">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="dfbe6-234">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="dfbe6-235">Globální čtenář</span><span class="sxs-lookup"><span data-stu-id="dfbe6-235">Global reader</span></span> | <span data-ttu-id="dfbe6-236">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="dfbe6-236">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="dfbe6-237">Správce skupin</span><span class="sxs-lookup"><span data-stu-id="dfbe6-237">Groups administrator</span></span> | <span data-ttu-id="dfbe6-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="dfbe6-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="dfbe6-239">Pozvánka hosta</span><span class="sxs-lookup"><span data-stu-id="dfbe6-239">Guest inviter</span></span> | <span data-ttu-id="dfbe6-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="dfbe6-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="dfbe6-241">Správce helpdesku</span><span class="sxs-lookup"><span data-stu-id="dfbe6-241">Helpdesk administrator</span></span> | <span data-ttu-id="dfbe6-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="dfbe6-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="dfbe6-243">Správce Intune</span><span class="sxs-lookup"><span data-stu-id="dfbe6-243">Intune administrator</span></span> | <span data-ttu-id="dfbe6-244">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="dfbe6-244">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="dfbe6-245">Správce Kaizala</span><span class="sxs-lookup"><span data-stu-id="dfbe6-245">Kaizala administrator</span></span> | <span data-ttu-id="dfbe6-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="dfbe6-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="dfbe6-247">Správce licencí</span><span class="sxs-lookup"><span data-stu-id="dfbe6-247">License administrator</span></span> | <span data-ttu-id="dfbe6-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="dfbe6-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="dfbe6-249">Čtenář ochrany osobních údajů centra zpráv</span><span class="sxs-lookup"><span data-stu-id="dfbe6-249">Message center privacy reader</span></span> | <span data-ttu-id="dfbe6-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="dfbe6-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="dfbe6-251">Čtenář centra zpráv</span><span class="sxs-lookup"><span data-stu-id="dfbe6-251">Message center reader</span></span> | <span data-ttu-id="dfbe6-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="dfbe6-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="dfbe6-253">Správce aplikací Office</span><span class="sxs-lookup"><span data-stu-id="dfbe6-253">Office apps administrator</span></span> | <span data-ttu-id="dfbe6-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="dfbe6-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="dfbe6-255">Správce hesel</span><span class="sxs-lookup"><span data-stu-id="dfbe6-255">Password administrator</span></span> | <span data-ttu-id="dfbe6-256">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="dfbe6-256">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="dfbe6-257">Správce Power BI</span><span class="sxs-lookup"><span data-stu-id="dfbe6-257">Power BI administrator</span></span> | <span data-ttu-id="dfbe6-258">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="dfbe6-258">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="dfbe6-259">Správce platforma Power</span><span class="sxs-lookup"><span data-stu-id="dfbe6-259">Power platform administrator</span></span> | <span data-ttu-id="dfbe6-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="dfbe6-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="dfbe6-261">Správce privilegovaného ověřování</span><span class="sxs-lookup"><span data-stu-id="dfbe6-261">Privileged authentication administrator</span></span> | <span data-ttu-id="dfbe6-262">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="dfbe6-262">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="dfbe6-263">Správce privilegovaných rolí</span><span class="sxs-lookup"><span data-stu-id="dfbe6-263">Privileged role administrator</span></span> | <span data-ttu-id="dfbe6-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="dfbe6-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="dfbe6-265">Čtečka sestav</span><span class="sxs-lookup"><span data-stu-id="dfbe6-265">Reports reader</span></span> | <span data-ttu-id="dfbe6-266">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="dfbe6-266">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="dfbe6-267">Správce hledání</span><span class="sxs-lookup"><span data-stu-id="dfbe6-267">Search administrator</span></span> | <span data-ttu-id="dfbe6-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="dfbe6-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="dfbe6-269">Editor hledání</span><span class="sxs-lookup"><span data-stu-id="dfbe6-269">Search editor</span></span> | <span data-ttu-id="dfbe6-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="dfbe6-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="dfbe6-271">Správce zabezpečení</span><span class="sxs-lookup"><span data-stu-id="dfbe6-271">Security administrator</span></span> | <span data-ttu-id="dfbe6-272">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="dfbe6-272">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="dfbe6-273">Operátor zabezpečení</span><span class="sxs-lookup"><span data-stu-id="dfbe6-273">Security operator</span></span> | <span data-ttu-id="dfbe6-274">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="dfbe6-274">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="dfbe6-275">Čtenář zabezpečení</span><span class="sxs-lookup"><span data-stu-id="dfbe6-275">Security reader</span></span> | <span data-ttu-id="dfbe6-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="dfbe6-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="dfbe6-277">Správce služeb</span><span class="sxs-lookup"><span data-stu-id="dfbe6-277">Service support administrator</span></span> | <span data-ttu-id="dfbe6-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="dfbe6-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="dfbe6-279">Správce SharePointu</span><span class="sxs-lookup"><span data-stu-id="dfbe6-279">SharePoint administrator</span></span> | <span data-ttu-id="dfbe6-280">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="dfbe6-280">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="dfbe6-281">Správce Skypu pro firmy</span><span class="sxs-lookup"><span data-stu-id="dfbe6-281">Skype for Business administrator</span></span> | <span data-ttu-id="dfbe6-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="dfbe6-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="dfbe6-283">Správce komunikace týmů</span><span class="sxs-lookup"><span data-stu-id="dfbe6-283">Teams Communications Administrator</span></span> | <span data-ttu-id="dfbe6-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="dfbe6-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="dfbe6-285">Týmy Communications support inženýr</span><span class="sxs-lookup"><span data-stu-id="dfbe6-285">Teams Communications Support Engineer</span></span> | <span data-ttu-id="dfbe6-286">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="dfbe6-286">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="dfbe6-287">Týmy – specialisté komunikace</span><span class="sxs-lookup"><span data-stu-id="dfbe6-287">Teams Communications Specialist</span></span> | <span data-ttu-id="dfbe6-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="dfbe6-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="dfbe6-289">Správce služby Teams</span><span class="sxs-lookup"><span data-stu-id="dfbe6-289">Teams Service Administrator</span></span> | <span data-ttu-id="dfbe6-290">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="dfbe6-290">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="dfbe6-291">Správce uživatelů</span><span class="sxs-lookup"><span data-stu-id="dfbe6-291">User administrator</span></span> | <span data-ttu-id="dfbe6-292">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="dfbe6-292">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dfbe6-293">Další materiály</span><span class="sxs-lookup"><span data-stu-id="dfbe6-293">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
