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
ms.openlocfilehash: 7a0c606d82dd625c179ec89e22b9313dfa5d18b4
ms.sourcegitcommit: c026bf76a0e14a5ee68983519a63574c674e9ff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91636774"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="44a64-103">Skupiny Azure Active Directory (AAD), role správců a uživatelsky definované role</span><span class="sxs-lookup"><span data-stu-id="44a64-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="44a64-104">Od [Luke Latham](https://github.com/guardrex) a [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="44a64-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="44a64-105">Azure Active Directory (AAD) poskytuje několik autorizačních přístupů, které je možné kombinovat ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="44a64-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="44a64-106">Uživatelem definované skupiny</span><span class="sxs-lookup"><span data-stu-id="44a64-106">User-defined groups</span></span>
  * <span data-ttu-id="44a64-107">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="44a64-107">Security</span></span>
  * <span data-ttu-id="44a64-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="44a64-108">Microsoft 365</span></span>
  * <span data-ttu-id="44a64-109">Distribuce</span><span class="sxs-lookup"><span data-stu-id="44a64-109">Distribution</span></span>
* <span data-ttu-id="44a64-110">Role</span><span class="sxs-lookup"><span data-stu-id="44a64-110">Roles</span></span>
  * <span data-ttu-id="44a64-111">Role správce AAD</span><span class="sxs-lookup"><span data-stu-id="44a64-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="44a64-112">Uživatelsky definované role</span><span class="sxs-lookup"><span data-stu-id="44a64-112">User-defined roles</span></span>

<span data-ttu-id="44a64-113">Pokyny v tomto článku se týkají Blazor WebAssembly scénářů nasazení AAD popsaných v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="44a64-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="44a64-114">Samostatná aplikace využívající účty Microsoft</span><span class="sxs-lookup"><span data-stu-id="44a64-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="44a64-115">Samostatná aplikace využívající Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="44a64-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="44a64-116">Hostovaná aplikace využívající Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="44a64-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="44a64-117">Oprávnění rozhraní API pro Microsoft Graph</span><span class="sxs-lookup"><span data-stu-id="44a64-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="44a64-118">Pro každého uživatele aplikace s více než pěti rolemi správce AAD a členstvím ve skupině zabezpečení je vyžadováno volání [rozhraní API Microsoft Graph](/graph/use-the-api) .</span><span class="sxs-lookup"><span data-stu-id="44a64-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="44a64-119">Pokud chcete povolit Graph API volání, poskytněte samostatnou nebo klientskou aplikaci hostovaného Blazor řešení kterékoli z následujících [Graph API oprávnění](/graph/permissions-reference) v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="44a64-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="44a64-120">`Directory.Read.All` je oprávnění s minimálním oprávněním a je použito pro příklad popsaný v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="44a64-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="44a64-121">Uživatelsky definované skupiny a role správců</span><span class="sxs-lookup"><span data-stu-id="44a64-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="44a64-122">Postup konfigurace aplikace v Azure Portal k poskytnutí `groups` deklarace identity členství najdete v následujících článcích Azure.</span><span class="sxs-lookup"><span data-stu-id="44a64-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="44a64-123">Přiřaďte uživatele k rolím uživatelů (AAD) definovaných uživatelem a správcům AAD.</span><span class="sxs-lookup"><span data-stu-id="44a64-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="44a64-124">Role používající skupiny zabezpečení Azure AD</span><span class="sxs-lookup"><span data-stu-id="44a64-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="44a64-125">`groupMembershipClaims` přidělen</span><span class="sxs-lookup"><span data-stu-id="44a64-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="44a64-126">V následujících příkladech se předpokládá, že je uživatel přiřazený k roli *správce fakturace* AAD.</span><span class="sxs-lookup"><span data-stu-id="44a64-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="44a64-127">Jediná `groups` deklarace ODESÍLANÁ AAD prezentuje skupiny uživatelů a role jako ID objektů (GUID) v poli JSON.</span><span class="sxs-lookup"><span data-stu-id="44a64-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="44a64-128">Aplikace musí převést pole JSON skupin a rolí na jednotlivé `group` deklarace identity, pro které může aplikace sestavovat [zásady](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="44a64-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="44a64-129">Když počet přiřazených rolí správce AAD a uživatelem definovaných skupin přesáhne pět, AAD pošle `hasgroups` deklaraci identity s `true` hodnotou namísto odeslání `groups` deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="44a64-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="44a64-130">Každá aplikace, která může mít více než pět rolí a skupin přiřazených uživatelům, musí vytvořit samostatné Graph API volání, aby získala role a skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="44a64-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="44a64-131">Ukázková implementace uvedená v tomto článku se zabývá tímto scénářem.</span><span class="sxs-lookup"><span data-stu-id="44a64-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="44a64-132">Další informace najdete v `groups` `hasgroups` článku o deklaracích identity v tématu [tokeny přístupu k platformě Microsoft Identity Platform: deklarace datové části](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="44a64-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="44a64-133">Rozšíříte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> tak, aby zahrnovalo vlastnosti pole pro skupiny a role.</span><span class="sxs-lookup"><span data-stu-id="44a64-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="44a64-134">Přiřaďte k jednotlivým vlastnostem prázdné pole, aby kontrola `null` není nutná, pokud jsou tyto vlastnosti použity ve `foreach` smyčcech později.</span><span class="sxs-lookup"><span data-stu-id="44a64-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="44a64-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="44a64-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="44a64-136">V samostatné aplikaci nebo klientské aplikaci hostovaného Blazor řešení vytvořte vlastní <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> třídu.</span><span class="sxs-lookup"><span data-stu-id="44a64-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="44a64-137">Použijte správný obor (oprávnění) pro Graph API volání, která získávají informace o rolích a skupinách.</span><span class="sxs-lookup"><span data-stu-id="44a64-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="44a64-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="44a64-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="44a64-139">V `Program.Main` ( `Program.cs` ) přidejte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementační službu a přidejte s názvem <xref:System.Net.Http.HttpClient> pro vytváření Graph APIch požadavků.</span><span class="sxs-lookup"><span data-stu-id="44a64-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="44a64-140">Následující příklad pojmenuje klienta `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="44a64-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="44a64-141">Vytvořte třídy adresářových objektů AAD pro příjem rolí a skupin Open Data Protocol (OData) z Graph API volání.</span><span class="sxs-lookup"><span data-stu-id="44a64-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="44a64-142">OData přijde ve formátu JSON a volání <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> naplní instanci `DirectoryObjects` třídy.</span><span class="sxs-lookup"><span data-stu-id="44a64-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="44a64-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="44a64-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="44a64-144">Vytvořte vlastní objekt pro vytváření uživatelů, který bude zpracovávat deklarace rolí a skupin.</span><span class="sxs-lookup"><span data-stu-id="44a64-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="44a64-145">Následující příklad implementace také zpracovává `roles` pole deklarací, které je popsáno v části [uživatelsky definované role](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="44a64-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="44a64-146">Pokud `hasgroups` je tato deklarace identity přítomná, <xref:System.Net.Http.HttpClient> použije se k tomu autorizovaný požadavek na Graph API, aby se získaly role a skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="44a64-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="44a64-147">Tato implementace používá Identity koncový bod platformy Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([dokumentace k rozhraní API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="44a64-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="44a64-148">Pokyny v tomto tématu budou aktualizovány pro Identity verzi v 2.0, pokud jsou balíčky MSAL upgradovány pro verze 2.0.</span><span class="sxs-lookup"><span data-stu-id="44a64-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="44a64-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="44a64-149">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="44a64-150">Není nutné zadávat kód pro odebrání původní `groups` deklarace identity, pokud je k dispozici, protože je automaticky odebrána rozhraním.</span><span class="sxs-lookup"><span data-stu-id="44a64-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="44a64-151">Přístup v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="44a64-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="44a64-152">Přidá vlastní <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> třídu pro připojení přístupových tokenů k odchozím žádostem.</span><span class="sxs-lookup"><span data-stu-id="44a64-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="44a64-153">Přidá s názvem <xref:System.Net.Http.HttpClient> , aby vyžádal webové rozhraní API na zabezpečený externí koncový bod webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44a64-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="44a64-154"><xref:System.Net.Http.HttpClient>K provádění autorizovaných požadavků používá pojmenovaný.</span><span class="sxs-lookup"><span data-stu-id="44a64-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="44a64-155">Obecné pokrytí tohoto přístupu najdete v <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> článku.</span><span class="sxs-lookup"><span data-stu-id="44a64-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="44a64-156">Zaregistrujte továrnu v `Program.Main` ( `Program.cs` ) samostatné aplikace nebo klientské aplikace hostovaného Blazor řešení.</span><span class="sxs-lookup"><span data-stu-id="44a64-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="44a64-157">Souhlas s `Directory.Read.All` oborem oprávnění jako další obor pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="44a64-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

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

<span data-ttu-id="44a64-158">Vytvořte [zásadu](xref:security/authorization/policies) pro každou skupinu nebo roli v `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="44a64-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="44a64-159">Následující příklad vytvoří zásadu pro roli *správce fakturace* AAD:</span><span class="sxs-lookup"><span data-stu-id="44a64-159">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="44a64-160">Úplný seznam ID objektů role AAD najdete v části [ID objektů role správce AAD](#aad-administrator-role-object-ids) .</span><span class="sxs-lookup"><span data-stu-id="44a64-160">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="44a64-161">V následujících příkladech používá aplikace k autorizaci uživatele předchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="44a64-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="44a64-162">[ `AuthorizeView` Komponenta](xref:blazor/security/index#authorizeview-component) spolupracuje se zásadami:</span><span class="sxs-lookup"><span data-stu-id="44a64-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
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

<span data-ttu-id="44a64-163">Přístup k celé komponentě může být založen na zásadách pomocí [ `[Authorize]` direktivy atributu](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="44a64-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="44a64-164">Pokud uživatel není přihlášený, bude přesměrován na přihlašovací stránku AAD a pak se po přihlášení znovu k součásti.</span><span class="sxs-lookup"><span data-stu-id="44a64-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="44a64-165">Kontrolu zásad lze také [provést v kódu s procedurální logikou](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="44a64-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="44a64-166">Autorizace přístupu k rozhraní API serveru pro uživatelem definované skupiny a role správců</span><span class="sxs-lookup"><span data-stu-id="44a64-166">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="44a64-167">Kromě autorizace uživatelů v aplikaci WebAssembly na straně klienta pro přístup k stránkám a prostředkům může rozhraní API serveru autorizovat přístup uživatelů k zabezpečeným koncovým bodům rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44a64-167">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="44a64-168">Po ověření přístupového tokenu uživatele *serverem* aplikace:</span><span class="sxs-lookup"><span data-stu-id="44a64-168">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="44a64-169">Aplikace používá pro získání přístupového tokenu pro Graph API neproměnlivou [deklaraci identifikátoru objektu uživatele ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) od tokenu JWT ( `id_token` ).</span><span class="sxs-lookup"><span data-stu-id="44a64-169">The app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from the JWT (`id_token`) to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="44a64-170">Graph API volání získá členství uživatele v uživatelsky definované skupině zabezpečení Azure a správců role správce.</span><span class="sxs-lookup"><span data-stu-id="44a64-170">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships.</span></span>
* <span data-ttu-id="44a64-171">Členství slouží k navázání `group` deklarací.</span><span class="sxs-lookup"><span data-stu-id="44a64-171">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="44a64-172">[Zásady autorizace](xref:security/authorization/policies) se dají použít k omezení přístupu uživatelů k koncovým bodům rozhraní API serveru.</span><span class="sxs-lookup"><span data-stu-id="44a64-172">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints.</span></span>

> [!NOTE]
> <span data-ttu-id="44a64-173">Tyto pokyny aktuálně nezahrnují autorizaci uživatelů na základě [uživatelem definovaných rolí AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="44a64-173">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

### <a name="packages"></a><span data-ttu-id="44a64-174">Balíčky</span><span class="sxs-lookup"><span data-stu-id="44a64-174">Packages</span></span>

<span data-ttu-id="44a64-175">Přidejte odkazy na balíčky do aplikace *serveru* pro následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="44a64-175">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="44a64-176">Microsoft. Graph</span><span class="sxs-lookup"><span data-stu-id="44a64-176">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="44a64-177">[Společnost Microsoft. Identity Model. clients. Active Directory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="44a64-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="44a64-178">Konfigurace Azure</span><span class="sxs-lookup"><span data-stu-id="44a64-178">Azure configuration</span></span>

* <span data-ttu-id="44a64-179">Potvrďte, že registrace aplikace *serveru* má přístup rozhraní API k oprávnění Graph API pro `Directory.Read.All` , což je úroveň přístupu s nejnižšími oprávněními pro skupiny zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="44a64-179">Confirm that the *Server* app registration is given API access to the Graph API permission for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="44a64-180">Po provedení přiřazení oprávnění potvrďte, že se na oprávnění vztahují souhlas správce.</span><span class="sxs-lookup"><span data-stu-id="44a64-180">Confirm that admin consent is applied to the permission after making the permission assignment.</span></span>
* <span data-ttu-id="44a64-181">Přiřaďte *serverové* aplikaci nový tajný klíč klienta.</span><span class="sxs-lookup"><span data-stu-id="44a64-181">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="44a64-182">Poznamenejte si tajný kód pro konfiguraci aplikace v části [nastavení aplikace](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="44a64-182">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="44a64-183">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="44a64-183">App settings</span></span>

<span data-ttu-id="44a64-184">V souboru nastavení aplikace ( `appsettings.json` nebo `appsettings.Production.json` ) vytvořte `ClientSecret` záznam s tajným klíčem klienta *serverové* aplikace z Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="44a64-184">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="44a64-185">Například:</span><span class="sxs-lookup"><span data-stu-id="44a64-185">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a><span data-ttu-id="44a64-186">Zásady autorizace</span><span class="sxs-lookup"><span data-stu-id="44a64-186">Authorization policies</span></span>

<span data-ttu-id="44a64-187">Vytvořte [zásady autorizace](xref:security/authorization/policies) pro skupiny zabezpečení AAD a role správce AAD v *serverové* aplikaci `Startup.ConfigureServices` ( `Startup.cs` ) založené na ID objektu skupiny a [ID objektu role správce AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="44a64-187">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="44a64-188">Například zásada role správce fakturace Azure má následující konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="44a64-188">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="44a64-189">Další informace naleznete v tématu <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="44a64-189">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="44a64-190">Přístup k řadiči</span><span class="sxs-lookup"><span data-stu-id="44a64-190">Controller access</span></span>

<span data-ttu-id="44a64-191">Vyžadovat zásady na řadičích *serverové* aplikace.</span><span class="sxs-lookup"><span data-stu-id="44a64-191">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="44a64-192">Následující příklad omezuje přístup k fakturačním datům z nástroje `BillingDataController` do Azure fakturace správců s názvem zásady `BillingAdmin` , jak je nakonfigurováno v části [zásady autorizace](#authorization-policies) :</span><span class="sxs-lookup"><span data-stu-id="44a64-192">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a><span data-ttu-id="44a64-193">Konfigurace služeb</span><span class="sxs-lookup"><span data-stu-id="44a64-193">Service configuration</span></span>

<span data-ttu-id="44a64-194">V metodě *serverové* aplikace `Startup.ConfigureServices` přidejte logiku, aby Graph API volání a navázala `group` deklarace identity uživatelů pro skupiny zabezpečení a role uživatele.</span><span class="sxs-lookup"><span data-stu-id="44a64-194">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="44a64-195">Vzorový kód v této části používá Active Directory Authentication Library (ADAL), která je založená na Identity platformě Microsoft Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="44a64-195">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span> <span data-ttu-id="44a64-196">Toto téma bude aktualizováno pro Identity verzi v 2.0 pro .NET 5.</span><span class="sxs-lookup"><span data-stu-id="44a64-196">This topic will be updated for Identity v2.0 for .NET 5.</span></span> <span data-ttu-id="44a64-197">Sledujte průběh této práce monitorováním [[RC1] Microsoft Identity Platform 2,0 pro Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span><span class="sxs-lookup"><span data-stu-id="44a64-197">Track progress on this work by monitoring [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span></span>

<span data-ttu-id="44a64-198">Pro kód ve `Startup` třídě *serverové* aplikace jsou vyžadovány další obory názvů.</span><span class="sxs-lookup"><span data-stu-id="44a64-198">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="44a64-199">Následující sada `using` příkazů obsahuje požadované obory názvů pro kód, který následuje v této části:</span><span class="sxs-lookup"><span data-stu-id="44a64-199">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="44a64-200">Při konfiguraci <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="44a64-200">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="44a64-201">Volitelně můžete zahrnout zpracování pro <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="44a64-201">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="44a64-202">Aplikace může například protokolovat neúspěšné ověření.</span><span class="sxs-lookup"><span data-stu-id="44a64-202">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="44a64-203">V nástroji <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> proveďte Graph API volání pro získání skupin a rolí uživatele.</span><span class="sxs-lookup"><span data-stu-id="44a64-203">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="44a64-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> poskytuje identifikovatelné osobní údaje (PII) v protokolování zpráv.</span><span class="sxs-lookup"><span data-stu-id="44a64-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="44a64-205">Aktivujte jenom PII pro ladění pomocí testovacích uživatelských účtů.</span><span class="sxs-lookup"><span data-stu-id="44a64-205">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="44a64-206">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="44a64-206">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="44a64-207">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="44a64-207">In the preceding example:</span></span>

* <span data-ttu-id="44a64-208">Byl proveden pokus o získání tichého tokenu ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ), protože přístupový token již mohl být uložen v mezipaměti tokenů ADAL.</span><span class="sxs-lookup"><span data-stu-id="44a64-208">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="44a64-209">Je rychlejší získat token z mezipaměti, než požádat o nový token.</span><span class="sxs-lookup"><span data-stu-id="44a64-209">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="44a64-210">Pokud přístupový token není získaný z mezipaměti ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> nebo <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> je vyvolaný), provede se uživatelský kontrolní výraz ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) s přihlašovacími údaji klienta ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ), aby získal token jménem uživatele ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ).</span><span class="sxs-lookup"><span data-stu-id="44a64-210">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="44a64-211">V dalším kroku `Microsoft.Graph.GraphServiceClient` může pokračovat v použití tokenu k provedení volání Graph API.</span><span class="sxs-lookup"><span data-stu-id="44a64-211">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="44a64-212">Token se umístí do mezipaměti tokenů ADAL.</span><span class="sxs-lookup"><span data-stu-id="44a64-212">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="44a64-213">U budoucích Graph API volání stejného uživatele se token získává z mezipaměti v tichém režimu pomocí <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="44a64-213">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

<span data-ttu-id="44a64-214">Kód v <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nezískává přenosné členství.</span><span class="sxs-lookup"><span data-stu-id="44a64-214">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="44a64-215">Chcete-li změnit kód pro získání přímého a přenosného členství:</span><span class="sxs-lookup"><span data-stu-id="44a64-215">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="44a64-216">Pro řádek kódu:</span><span class="sxs-lookup"><span data-stu-id="44a64-216">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="44a64-217">Nahraďte předchozí řádek za:</span><span class="sxs-lookup"><span data-stu-id="44a64-217">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="44a64-218">Pro řádek kódu:</span><span class="sxs-lookup"><span data-stu-id="44a64-218">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="44a64-219">Nahraďte předchozí řádek za:</span><span class="sxs-lookup"><span data-stu-id="44a64-219">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="44a64-220">Kód v nástroji <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nerozlišuje mezi skupinami zabezpečení AAD a rolemi správce AAD při vytváření deklarací identity.</span><span class="sxs-lookup"><span data-stu-id="44a64-220">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="44a64-221">Aby aplikace rozlišila mezi skupinami a rolemi, podívejte se `entry.ODataType` při iteraci mezi skupinami a rolemi.</span><span class="sxs-lookup"><span data-stu-id="44a64-221">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="44a64-222">Chcete-li vytvořit samostatnou skupinu zabezpečení a deklarace identity role, použijte kód podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="44a64-222">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="44a64-223">Uživatelsky definované role</span><span class="sxs-lookup"><span data-stu-id="44a64-223">User-defined roles</span></span>

<span data-ttu-id="44a64-224">Aplikace zaregistrovaná v AAD se taky dá nakonfigurovat tak, aby používala uživatelsky definované role.</span><span class="sxs-lookup"><span data-stu-id="44a64-224">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="44a64-225">Pokud chcete aplikaci v Azure Portal nakonfigurovat tak, aby poskytovala `roles` deklaraci identity členství, přečtěte si téma [Postup: Přidání rolí aplikace do aplikace a jejich přijetí v tokenu](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) v dokumentaci k Azure.</span><span class="sxs-lookup"><span data-stu-id="44a64-225">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="44a64-226">Následující příklad předpokládá, že je aplikace nakonfigurovaná se dvěma rolemi:</span><span class="sxs-lookup"><span data-stu-id="44a64-226">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="44a64-227">I když nemůžete přiřadit role ke skupinám zabezpečení bez Azure AD Premium účtu, můžete přiřadit uživatele k rolím a získat `roles` deklaraci identity pro uživatele se standardním účtem Azure.</span><span class="sxs-lookup"><span data-stu-id="44a64-227">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="44a64-228">Pokyny v této části nevyžadují účet Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="44a64-228">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="44a64-229">V Azure Portal je přiřazeno více rolí tím, že **_znovu přidáte uživatele_** pro každé přiřazení další role.</span><span class="sxs-lookup"><span data-stu-id="44a64-229">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="44a64-230">Jediná deklarace, kterou `roles` odesílá AAD, prezentuje uživatelsky definované role jako `appRoles` `value` s v poli JSON.</span><span class="sxs-lookup"><span data-stu-id="44a64-230">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="44a64-231">Aplikace musí převést pole rolí JSON na jednotlivé `role` deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="44a64-231">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="44a64-232">V `CustomUserFactory` části [uživatelsky definované skupiny a role správce AAD](#user-defined-groups-and-administrator-roles) je nastavené tak, aby se jednalo o `roles` deklaraci identity s hodnotou pole JSON.</span><span class="sxs-lookup"><span data-stu-id="44a64-232">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="44a64-233">Přidejte a zaregistrujte se `CustomUserFactory` do samostatné aplikace nebo klientské aplikace hostovaného Blazor řešení, jak je znázorněno v části [uživatelsky definované skupiny a role správců AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="44a64-233">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="44a64-234">Není nutné zadávat kód pro odebrání původní `roles` deklarace identity, protože je automaticky odebrána rozhraním Framework.</span><span class="sxs-lookup"><span data-stu-id="44a64-234">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="44a64-235">V `Program.Main` samostatné aplikaci nebo klientské aplikaci hostovaného Blazor řešení zadejte deklaraci identity s názvem `role` jako deklaraci identity role:</span><span class="sxs-lookup"><span data-stu-id="44a64-235">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="44a64-236">Přístupy k autorizaci komponent jsou v tuto chvíli funkční.</span><span class="sxs-lookup"><span data-stu-id="44a64-236">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="44a64-237">Kterýkoli z autorizačních mechanismů v součástech může použít `admin` roli k autorizaci uživatele:</span><span class="sxs-lookup"><span data-stu-id="44a64-237">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="44a64-238">[ `AuthorizeView` součást](xref:blazor/security/index#authorizeview-component) (příklad: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="44a64-238">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="44a64-239">[ `[Authorize]` direktiva atributu](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (příklad: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="44a64-239">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="44a64-240">[Procedurální logika](xref:blazor/security/index#procedural-logic) (příklad: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="44a64-240">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="44a64-241">Podporuje se vícenásobné testy rolí:</span><span class="sxs-lookup"><span data-stu-id="44a64-241">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="44a64-242">ID objektů role správce AAD</span><span class="sxs-lookup"><span data-stu-id="44a64-242">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="44a64-243">ID objektů prezentovaná v následující tabulce se používají k vytváření [zásad](xref:security/authorization/policies) pro `group` deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="44a64-243">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="44a64-244">Zásady umožňují aplikaci ověřovat uživatele pro různé aktivity v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="44a64-244">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="44a64-245">Další informace najdete v části [uživatelsky definované skupiny a role správců AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="44a64-245">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="44a64-246">Role správce AAD</span><span class="sxs-lookup"><span data-stu-id="44a64-246">AAD Administrator Role</span></span> | <span data-ttu-id="44a64-247">ID objektu</span><span class="sxs-lookup"><span data-stu-id="44a64-247">Object ID</span></span>
--- | ---
<span data-ttu-id="44a64-248">Správce aplikace</span><span class="sxs-lookup"><span data-stu-id="44a64-248">Application administrator</span></span> | <span data-ttu-id="44a64-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="44a64-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="44a64-250">Vývojář aplikace</span><span class="sxs-lookup"><span data-stu-id="44a64-250">Application developer</span></span> | <span data-ttu-id="44a64-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="44a64-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="44a64-252">Správce ověřování</span><span class="sxs-lookup"><span data-stu-id="44a64-252">Authentication administrator</span></span> | <span data-ttu-id="44a64-253">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="44a64-253">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="44a64-254">Správce Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="44a64-254">Azure DevOps administrator</span></span> | <span data-ttu-id="44a64-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="44a64-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="44a64-256">Správce Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="44a64-256">Azure Information Protection administrator</span></span> | <span data-ttu-id="44a64-257">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="44a64-257">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="44a64-258">B2C IEF, správce sady klíčů</span><span class="sxs-lookup"><span data-stu-id="44a64-258">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="44a64-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="44a64-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="44a64-260">Správce zásad IEF B2C</span><span class="sxs-lookup"><span data-stu-id="44a64-260">B2C IEF Policy administrator</span></span> | <span data-ttu-id="44a64-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="44a64-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="44a64-262">Správce toku B2C uživatele</span><span class="sxs-lookup"><span data-stu-id="44a64-262">B2C user flow administrator</span></span> | <span data-ttu-id="44a64-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="44a64-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="44a64-264">Správce atributů toku uživatele B2C</span><span class="sxs-lookup"><span data-stu-id="44a64-264">B2C user flow attribute administrator</span></span> | <span data-ttu-id="44a64-265">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="44a64-265">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="44a64-266">Správce fakturace</span><span class="sxs-lookup"><span data-stu-id="44a64-266">Billing administrator</span></span> | <span data-ttu-id="44a64-267">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="44a64-267">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="44a64-268">Správce cloudové aplikace</span><span class="sxs-lookup"><span data-stu-id="44a64-268">Cloud application administrator</span></span> | <span data-ttu-id="44a64-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="44a64-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="44a64-270">Správce cloudového zařízení</span><span class="sxs-lookup"><span data-stu-id="44a64-270">Cloud device administrator</span></span> | <span data-ttu-id="44a64-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="44a64-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="44a64-272">Správce dodržování předpisů</span><span class="sxs-lookup"><span data-stu-id="44a64-272">Compliance administrator</span></span> | <span data-ttu-id="44a64-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="44a64-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="44a64-274">Správce dat dodržování předpisů</span><span class="sxs-lookup"><span data-stu-id="44a64-274">Compliance data administrator</span></span> | <span data-ttu-id="44a64-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="44a64-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="44a64-276">Správce podmíněného přístupu</span><span class="sxs-lookup"><span data-stu-id="44a64-276">Conditional Access administrator</span></span> | <span data-ttu-id="44a64-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="44a64-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="44a64-278">Schvalovatel přístupu k bezpečnostnímu modulu zákazníka</span><span class="sxs-lookup"><span data-stu-id="44a64-278">Customer LockBox access approver</span></span> | <span data-ttu-id="44a64-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="44a64-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="44a64-280">Správce Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="44a64-280">Desktop Analytics administrator</span></span> | <span data-ttu-id="44a64-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="44a64-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="44a64-282">Čtečky adresářů</span><span class="sxs-lookup"><span data-stu-id="44a64-282">Directory readers</span></span> | <span data-ttu-id="44a64-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="44a64-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="44a64-284">Správce Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="44a64-284">Dynamics 365 administrator</span></span> | <span data-ttu-id="44a64-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="44a64-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="44a64-286">Správce Exchange</span><span class="sxs-lookup"><span data-stu-id="44a64-286">Exchange administrator</span></span> | <span data-ttu-id="44a64-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="44a64-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="44a64-288">IdentitySprávce externího poskytovatele</span><span class="sxs-lookup"><span data-stu-id="44a64-288">External Identity Provider administrator</span></span> | <span data-ttu-id="44a64-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="44a64-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="44a64-290">Globální správce</span><span class="sxs-lookup"><span data-stu-id="44a64-290">Global administrator</span></span> | <span data-ttu-id="44a64-291">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="44a64-291">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="44a64-292">Globální čtenář</span><span class="sxs-lookup"><span data-stu-id="44a64-292">Global reader</span></span> | <span data-ttu-id="44a64-293">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="44a64-293">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="44a64-294">Správce skupin</span><span class="sxs-lookup"><span data-stu-id="44a64-294">Groups administrator</span></span> | <span data-ttu-id="44a64-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="44a64-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="44a64-296">Pozvánka hosta</span><span class="sxs-lookup"><span data-stu-id="44a64-296">Guest inviter</span></span> | <span data-ttu-id="44a64-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="44a64-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="44a64-298">Správce helpdesku</span><span class="sxs-lookup"><span data-stu-id="44a64-298">Helpdesk administrator</span></span> | <span data-ttu-id="44a64-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="44a64-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="44a64-300">Správce Intune</span><span class="sxs-lookup"><span data-stu-id="44a64-300">Intune administrator</span></span> | <span data-ttu-id="44a64-301">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="44a64-301">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="44a64-302">Správce Kaizala</span><span class="sxs-lookup"><span data-stu-id="44a64-302">Kaizala administrator</span></span> | <span data-ttu-id="44a64-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="44a64-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="44a64-304">Správce licencí</span><span class="sxs-lookup"><span data-stu-id="44a64-304">License administrator</span></span> | <span data-ttu-id="44a64-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="44a64-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="44a64-306">Čtenář ochrany osobních údajů centra zpráv</span><span class="sxs-lookup"><span data-stu-id="44a64-306">Message center privacy reader</span></span> | <span data-ttu-id="44a64-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="44a64-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="44a64-308">Čtenář centra zpráv</span><span class="sxs-lookup"><span data-stu-id="44a64-308">Message center reader</span></span> | <span data-ttu-id="44a64-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="44a64-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="44a64-310">Správce aplikací Office</span><span class="sxs-lookup"><span data-stu-id="44a64-310">Office apps administrator</span></span> | <span data-ttu-id="44a64-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="44a64-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="44a64-312">Správce hesel</span><span class="sxs-lookup"><span data-stu-id="44a64-312">Password administrator</span></span> | <span data-ttu-id="44a64-313">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="44a64-313">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="44a64-314">Správce Power BI</span><span class="sxs-lookup"><span data-stu-id="44a64-314">Power BI administrator</span></span> | <span data-ttu-id="44a64-315">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="44a64-315">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="44a64-316">Správce platforma Power</span><span class="sxs-lookup"><span data-stu-id="44a64-316">Power platform administrator</span></span> | <span data-ttu-id="44a64-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="44a64-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="44a64-318">Správce privilegovaného ověřování</span><span class="sxs-lookup"><span data-stu-id="44a64-318">Privileged authentication administrator</span></span> | <span data-ttu-id="44a64-319">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="44a64-319">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="44a64-320">Správce privilegovaných rolí</span><span class="sxs-lookup"><span data-stu-id="44a64-320">Privileged role administrator</span></span> | <span data-ttu-id="44a64-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="44a64-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="44a64-322">Čtečka sestav</span><span class="sxs-lookup"><span data-stu-id="44a64-322">Reports reader</span></span> | <span data-ttu-id="44a64-323">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="44a64-323">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="44a64-324">Správce hledání</span><span class="sxs-lookup"><span data-stu-id="44a64-324">Search administrator</span></span> | <span data-ttu-id="44a64-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="44a64-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="44a64-326">Editor hledání</span><span class="sxs-lookup"><span data-stu-id="44a64-326">Search editor</span></span> | <span data-ttu-id="44a64-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="44a64-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="44a64-328">Správce zabezpečení</span><span class="sxs-lookup"><span data-stu-id="44a64-328">Security administrator</span></span> | <span data-ttu-id="44a64-329">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="44a64-329">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="44a64-330">Operátor zabezpečení</span><span class="sxs-lookup"><span data-stu-id="44a64-330">Security operator</span></span> | <span data-ttu-id="44a64-331">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="44a64-331">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="44a64-332">Čtenář zabezpečení</span><span class="sxs-lookup"><span data-stu-id="44a64-332">Security reader</span></span> | <span data-ttu-id="44a64-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="44a64-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="44a64-334">Správce služeb</span><span class="sxs-lookup"><span data-stu-id="44a64-334">Service support administrator</span></span> | <span data-ttu-id="44a64-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="44a64-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="44a64-336">Správce SharePointu</span><span class="sxs-lookup"><span data-stu-id="44a64-336">SharePoint administrator</span></span> | <span data-ttu-id="44a64-337">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="44a64-337">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="44a64-338">Správce Skypu pro firmy</span><span class="sxs-lookup"><span data-stu-id="44a64-338">Skype for Business administrator</span></span> | <span data-ttu-id="44a64-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="44a64-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="44a64-340">Správce komunikace týmů</span><span class="sxs-lookup"><span data-stu-id="44a64-340">Teams Communications Administrator</span></span> | <span data-ttu-id="44a64-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="44a64-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="44a64-342">Týmy Communications support inženýr</span><span class="sxs-lookup"><span data-stu-id="44a64-342">Teams Communications Support Engineer</span></span> | <span data-ttu-id="44a64-343">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="44a64-343">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="44a64-344">Týmy – specialisté komunikace</span><span class="sxs-lookup"><span data-stu-id="44a64-344">Teams Communications Specialist</span></span> | <span data-ttu-id="44a64-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="44a64-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="44a64-346">Správce služby Teams</span><span class="sxs-lookup"><span data-stu-id="44a64-346">Teams Service Administrator</span></span> | <span data-ttu-id="44a64-347">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="44a64-347">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="44a64-348">Správce uživatelů</span><span class="sxs-lookup"><span data-stu-id="44a64-348">User administrator</span></span> | <span data-ttu-id="44a64-349">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="44a64-349">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="44a64-350">Další materiály</span><span class="sxs-lookup"><span data-stu-id="44a64-350">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
