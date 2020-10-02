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
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a>Skupiny Azure Active Directory (AAD), role správců a uživatelsky definované role

Od [Luke Latham](https://github.com/guardrex) a [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) poskytuje několik autorizačních přístupů, které je možné kombinovat ASP.NET Core Identity :

* Uživatelem definované skupiny
  * Zabezpečení
  * Microsoft 365
  * Distribuce
* Role
  * Role správce AAD
  * Uživatelsky definované role

Pokyny v tomto článku se týkají Blazor WebAssembly scénářů nasazení AAD popsaných v následujících tématech:

* [Samostatná aplikace využívající účty Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Samostatná aplikace využívající Azure Active Directory](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Hostovaná aplikace využívající Azure Active Directory](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a>Oprávnění rozhraní API pro Microsoft Graph

Pro každého uživatele aplikace s více než pěti rolemi správce AAD a členstvím ve skupině zabezpečení je vyžadováno volání [rozhraní API Microsoft Graph](/graph/use-the-api) .

Pokud chcete povolit Graph API volání, poskytněte samostatnou nebo klientskou aplikaci hostovaného Blazor řešení kterékoli z následujících [Graph API oprávnění](/graph/permissions-reference) v Azure Portal:

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` je oprávnění s minimálním oprávněním a je použito pro příklad popsaný v tomto článku.

## <a name="user-defined-groups-and-administrator-roles"></a>Uživatelsky definované skupiny a role správců

Postup konfigurace aplikace v Azure Portal k poskytnutí `groups` deklarace identity členství najdete v následujících článcích Azure. Přiřaďte uživatele k rolím uživatelů (AAD) definovaných uživatelem a správcům AAD.

* [Role používající skupiny zabezpečení Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims` přidělen](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

V následujících příkladech se předpokládá, že je uživatel přiřazený k roli *správce fakturace* AAD.

Jediná `groups` deklarace ODESÍLANÁ AAD prezentuje skupiny uživatelů a role jako ID objektů (GUID) v poli JSON. Aplikace musí převést pole JSON skupin a rolí na jednotlivé `group` deklarace identity, pro které může aplikace sestavovat [zásady](xref:security/authorization/policies) .

Když počet přiřazených rolí správce AAD a uživatelem definovaných skupin přesáhne pět, AAD pošle `hasgroups` deklaraci identity s `true` hodnotou namísto odeslání `groups` deklarace identity. Každá aplikace, která může mít více než pět rolí a skupin přiřazených uživatelům, musí vytvořit samostatné Graph API volání, aby získala role a skupiny uživatelů. Ukázková implementace uvedená v tomto článku se zabývá tímto scénářem. Další informace najdete v `groups` `hasgroups` článku o deklaracích identity v tématu [tokeny přístupu k platformě Microsoft Identity Platform: deklarace datové části](/azure/active-directory/develop/access-tokens#payload-claims) .

Rozšíříte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> tak, aby zahrnovalo vlastnosti pole pro skupiny a role. Přiřaďte k jednotlivým vlastnostem prázdné pole, aby kontrola `null` není nutná, pokud jsou tyto vlastnosti použity ve `foreach` smyčcech později.

`CustomUserAccount.cs`:

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

V samostatné aplikaci nebo klientské aplikaci hostovaného Blazor řešení vytvořte vlastní <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> třídu. Použijte správný obor (oprávnění) pro Graph API volání, která získávají informace o rolích a skupinách.

`GraphAPIAuthorizationMessageHandler.cs`:

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

V `Program.Main` ( `Program.cs` ) přidejte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementační službu a přidejte s názvem <xref:System.Net.Http.HttpClient> pro vytváření Graph APIch požadavků. Následující příklad pojmenuje klienta `GraphAPI` :

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Vytvořte třídy adresářových objektů AAD pro příjem rolí a skupin Open Data Protocol (OData) z Graph API volání. OData přijde ve formátu JSON a volání <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> naplní instanci `DirectoryObjects` třídy.

`DirectoryObjects.cs`:

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

Vytvořte vlastní objekt pro vytváření uživatelů, který bude zpracovávat deklarace rolí a skupin. Následující příklad implementace také zpracovává `roles` pole deklarací, které je popsáno v části [uživatelsky definované role](#user-defined-roles) . Pokud `hasgroups` je tato deklarace identity přítomná, <xref:System.Net.Http.HttpClient> použije se k tomu autorizovaný požadavek na Graph API, aby se získaly role a skupiny uživatelů. Tato implementace používá Identity koncový bod platformy Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([dokumentace k rozhraní API](/graph/api/user-list-memberof)). Pokyny v tomto tématu budou aktualizovány pro Identity verzi v 2.0, pokud jsou balíčky MSAL upgradovány pro verze 2.0.

`CustomAccountFactory.cs`:

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

Není nutné zadávat kód pro odebrání původní `groups` deklarace identity, pokud je k dispozici, protože je automaticky odebrána rozhraním.

> [!NOTE]
> Přístup v tomto příkladu:
>
> * Přidá vlastní <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> třídu pro připojení přístupových tokenů k odchozím žádostem.
> * Přidá s názvem <xref:System.Net.Http.HttpClient> , aby vyžádal webové rozhraní API na zabezpečený externí koncový bod webového rozhraní API.
> * <xref:System.Net.Http.HttpClient>K provádění autorizovaných požadavků používá pojmenovaný.
>
> Obecné pokrytí tohoto přístupu najdete v <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> článku.

Zaregistrujte továrnu v `Program.Main` ( `Program.cs` ) samostatné aplikace nebo klientské aplikace hostovaného Blazor řešení. Souhlas s `Directory.Read.All` oborem oprávnění jako další obor pro aplikaci:

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

Vytvořte [zásadu](xref:security/authorization/policies) pro každou skupinu nebo roli v `Program.Main` . Následující příklad vytvoří zásadu pro roli *správce fakturace* AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Úplný seznam ID objektů role AAD najdete v části [ID objektů role správce AAD](#aad-administrator-role-object-ids) .

V následujících příkladech používá aplikace k autorizaci uživatele předchozí zásady.

[ `AuthorizeView` Komponenta](xref:blazor/security/index#authorizeview-component) spolupracuje se zásadami:

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

Přístup k celé komponentě může být založen na zásadách pomocí [ `[Authorize]` direktivy atributu](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Pokud uživatel není přihlášený, bude přesměrován na přihlašovací stránku AAD a pak se po přihlášení znovu k součásti.

Kontrolu zásad lze také [provést v kódu s procedurální logikou](xref:blazor/security/index#procedural-logic):

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a>Autorizace přístupu k rozhraní API serveru pro uživatelem definované skupiny a role správců

Kromě autorizace uživatelů v aplikaci WebAssembly na straně klienta pro přístup k stránkám a prostředkům může rozhraní API serveru autorizovat přístup uživatelů k zabezpečeným koncovým bodům rozhraní API. Po ověření přístupového tokenu uživatele *serverem* aplikace:

* Aplikace používá pro získání přístupového tokenu pro Graph API neproměnlivou [deklaraci identifikátoru objektu uživatele ( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) od tokenu JWT ( `id_token` ).
* Graph API volání získá členství uživatele v uživatelsky definované skupině zabezpečení Azure a správců role správce.
* Členství slouží k navázání `group` deklarací.
* [Zásady autorizace](xref:security/authorization/policies) se dají použít k omezení přístupu uživatelů k koncovým bodům rozhraní API serveru.

> [!NOTE]
> Tyto pokyny aktuálně nezahrnují autorizaci uživatelů na základě [uživatelem definovaných rolí AAD](#user-defined-roles).

### <a name="packages"></a>Balíčky

Přidejte odkazy na balíčky do aplikace *serveru* pro následující balíčky:

* [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Společnost Microsoft. Identity Model. clients. Active Directory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)

### <a name="azure-configuration"></a>Konfigurace Azure

* Potvrďte, že registrace aplikace *serveru* má přístup rozhraní API k oprávnění Graph API pro `Directory.Read.All` , což je úroveň přístupu s nejnižšími oprávněními pro skupiny zabezpečení. Po provedení přiřazení oprávnění potvrďte, že se na oprávnění vztahují souhlas správce.
* Přiřaďte *serverové* aplikaci nový tajný klíč klienta. Poznamenejte si tajný kód pro konfiguraci aplikace v části [nastavení aplikace](#app-settings) .

### <a name="app-settings"></a>Nastavení aplikace

V souboru nastavení aplikace ( `appsettings.json` nebo `appsettings.Production.json` ) vytvořte `ClientSecret` záznam s tajným klíčem klienta *serverové* aplikace z Azure Portal:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

Například:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a>Zásady autorizace

Vytvořte [zásady autorizace](xref:security/authorization/policies) pro skupiny zabezpečení AAD a role správce AAD v *serverové* aplikaci `Startup.ConfigureServices` ( `Startup.cs` ) založené na ID objektu skupiny a [ID objektu role správce AAD](#aad-administrator-role-object-ids).

Například zásada role správce fakturace Azure má následující konfiguraci:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Další informace naleznete v tématu <xref:security/authorization/policies>.

### <a name="controller-access"></a>Přístup k řadiči

Vyžadovat zásady na řadičích *serverové* aplikace.

Následující příklad omezuje přístup k fakturačním datům z nástroje `BillingDataController` do Azure fakturace správců s názvem zásady `BillingAdmin` , jak je nakonfigurováno v části [zásady autorizace](#authorization-policies) :

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a>Konfigurace služeb

V metodě *serverové* aplikace `Startup.ConfigureServices` přidejte logiku, aby Graph API volání a navázala `group` deklarace identity uživatelů pro skupiny zabezpečení a role uživatele.

> [!NOTE]
> Vzorový kód v této části používá Active Directory Authentication Library (ADAL), která je založená na Identity platformě Microsoft Platform v 1.0. Toto téma bude aktualizováno pro Identity verzi v 2.0 pro .NET 5. Sledujte průběh této práce monitorováním [[RC1] Microsoft Identity Platform 2,0 pro Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).

Pro kód ve `Startup` třídě *serverové* aplikace jsou vyžadovány další obory názvů. Následující sada `using` příkazů obsahuje požadované obory názvů pro kód, který následuje v této části:

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

Při konfiguraci <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :

* Volitelně můžete zahrnout zpracování pro <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> . Aplikace může například protokolovat neúspěšné ověření.
* V nástroji <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> proveďte Graph API volání pro získání skupin a rolí uživatele.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> poskytuje identifikovatelné osobní údaje (PII) v protokolování zpráv. Aktivujte jenom PII pro ladění pomocí testovacích uživatelských účtů.

V `Startup.ConfigureServices`:

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

V předchozím příkladu:

* Byl proveden pokus o získání tichého tokenu ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> ), protože přístupový token již mohl být uložen v mezipaměti tokenů ADAL. Je rychlejší získat token z mezipaměti, než požádat o nový token.
* Pokud přístupový token není získaný z mezipaměti ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> nebo <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> je vyvolaný), provede se uživatelský kontrolní výraz ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) s přihlašovacími údaji klienta ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ), aby získal token jménem uživatele ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ). V dalším kroku `Microsoft.Graph.GraphServiceClient` může pokračovat v použití tokenu k provedení volání Graph API. Token se umístí do mezipaměti tokenů ADAL. U budoucích Graph API volání stejného uživatele se token získává z mezipaměti v tichém režimu pomocí <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .

Kód v <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nezískává přenosné členství. Chcete-li změnit kód pro získání přímého a přenosného členství:

* Pro řádek kódu:

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  Nahraďte předchozí řádek za:

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* Pro řádek kódu:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  Nahraďte předchozí řádek za:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

Kód v nástroji <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nerozlišuje mezi skupinami zabezpečení AAD a rolemi správce AAD při vytváření deklarací identity. Aby aplikace rozlišila mezi skupinami a rolemi, podívejte se `entry.ODataType` při iteraci mezi skupinami a rolemi. Chcete-li vytvořit samostatnou skupinu zabezpečení a deklarace identity role, použijte kód podobný následujícímu:

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

## <a name="user-defined-roles"></a>Uživatelsky definované role

Aplikace zaregistrovaná v AAD se taky dá nakonfigurovat tak, aby používala uživatelsky definované role.

Pokud chcete aplikaci v Azure Portal nakonfigurovat tak, aby poskytovala `roles` deklaraci identity členství, přečtěte si téma [Postup: Přidání rolí aplikace do aplikace a jejich přijetí v tokenu](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) v dokumentaci k Azure.

Následující příklad předpokládá, že je aplikace nakonfigurovaná se dvěma rolemi:

* `admin`
* `developer`

> [!NOTE]
> I když nemůžete přiřadit role ke skupinám zabezpečení bez Azure AD Premium účtu, můžete přiřadit uživatele k rolím a získat `roles` deklaraci identity pro uživatele se standardním účtem Azure. Pokyny v této části nevyžadují účet Azure AD Premium.
>
> V Azure Portal je přiřazeno více rolí tím, že **_znovu přidáte uživatele_** pro každé přiřazení další role.

Jediná deklarace, kterou `roles` odesílá AAD, prezentuje uživatelsky definované role jako `appRoles` `value` s v poli JSON. Aplikace musí převést pole rolí JSON na jednotlivé `role` deklarace identity.

V `CustomUserFactory` části [uživatelsky definované skupiny a role správce AAD](#user-defined-groups-and-administrator-roles) je nastavené tak, aby se jednalo o `roles` deklaraci identity s hodnotou pole JSON. Přidejte a zaregistrujte se `CustomUserFactory` do samostatné aplikace nebo klientské aplikace hostovaného Blazor řešení, jak je znázorněno v části [uživatelsky definované skupiny a role správců AAD](#user-defined-groups-and-administrator-roles) . Není nutné zadávat kód pro odebrání původní `roles` deklarace identity, protože je automaticky odebrána rozhraním Framework.

V `Program.Main` samostatné aplikaci nebo klientské aplikaci hostovaného Blazor řešení zadejte deklaraci identity s názvem `role` jako deklaraci identity role:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

Přístupy k autorizaci komponent jsou v tuto chvíli funkční. Kterýkoli z autorizačních mechanismů v součástech může použít `admin` roli k autorizaci uživatele:

* [ `AuthorizeView` součást](xref:blazor/security/index#authorizeview-component) (příklad: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` direktiva atributu](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (příklad: `@attribute [Authorize(Roles = "admin")]` )
* [Procedurální logika](xref:blazor/security/index#procedural-logic) (příklad: `if (user.IsInRole("admin")) { ... }` )

  Podporuje se vícenásobné testy rolí:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a>ID objektů role správce AAD

ID objektů prezentovaná v následující tabulce se používají k vytváření [zásad](xref:security/authorization/policies) pro `group` deklarace identity. Zásady umožňují aplikaci ověřovat uživatele pro různé aktivity v aplikaci. Další informace najdete v části [uživatelsky definované skupiny a role správců AAD](#user-defined-groups-and-administrator-roles) .

Role správce AAD | ID objektu
--- | ---
Správce aplikace | fa11557b-4f15-4ddd-85d5-313c7cd74047
Vývojář aplikace | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Správce ověřování | 02d110a1-96b1-419e-af87-746461b60ed7
Správce Azure DevOps | a5311ace-ca41-44cd-b833-8d22caa0b34f
Správce Azure Information Protection | 18632dce-f9b5-4f01-abb5-37051f06860e
B2C IEF, správce sady klíčů | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Správce zásad IEF B2C | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Správce toku B2C uživatele | baa531b7-8cf0-44ad-8f98-eded88dae827
Správce atributů toku uživatele B2C | dd0baca0-a535-48c1-b871-8431abe16452
Správce fakturace | 69ff516a-b57d-4697-a429-9de4af7b5609
Správce cloudové aplikace | 250b5fe3-b553-458d-9a53-b782c13c34bf
Správce cloudového zařízení | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Správce dodržování předpisů | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Správce dat dodržování předpisů | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Správce podmíněného přístupu | 8f71a611-137d-49af-87ad-e97f1fd5da76
Schvalovatel přístupu k bezpečnostnímu modulu zákazníka | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Správce Desktop Analytics | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Čtečky adresářů | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Správce Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Správce Exchange | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
IdentitySprávce externího poskytovatele | febfaeb4-e478-407a-b4b3-f4d9716618a2
Globální správce | a45ba61b-44db-462c-924b-3b2719152588
Globální čtenář | f6903b21-6aba-4124-b44c-76671796b9d5
Správce skupin | 158b3e5a-d89d-460b-92b5-3b34985f0197
Pozvánka hosta | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Správce helpdesku | 108678c8-6628-44e1-8d01-caf598a6a5f5
Správce Intune | 79950741-23fa-4189-b2cb-46640601c497
Správce Kaizala | d6322af2-48e7-42e0-8c68-0bbe31af3412
Správce licencí | 3355458a-e423-44bf-8b98-4ac5e572cea5
Čtenář ochrany osobních údajů centra zpráv | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Čtenář centra zpráv | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Správce aplikací Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Správce hesel | 466e48b7-5d66-4ae5-8911-1a118de74941
Správce Power BI | 984e83b8-8337-4255-91a1-acb663175ab4
Správce platforma Power | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Správce privilegovaného ověřování | 0829f731-b46d-419f-9742-aeb122367d11
Správce privilegovaných rolí | f20a725a-d1c8-4107-83ea-1171c97d00c7
Čtečka sestav | 54635450-e8ed-4f2d-9632-07db2517b4de
Správce hledání | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Editor hledání | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Správce zabezpečení | 20fa50e3-6531-44d8-bd39-b251420568ad
Operátor zabezpečení | 43aae017-8e51-4188-91ab-e6debd572800
Čtenář zabezpečení | 45035cd3-fd97-4250-8197-3a53d3562d9b
Správce služeb | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
Správce SharePointu | e1c32229-875e-461d-ae24-3cb99116e86c
Správce Skypu pro firmy | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Správce komunikace týmů | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Týmy Communications support inženýr | 802dd94e-d717-46f6-af98-b9167071e9fc
Týmy – specialisté komunikace | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Správce služby Teams | 8846a0be-197b-443a-b13c-11192691fa24
Správce uživatelů | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Další materiály

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
