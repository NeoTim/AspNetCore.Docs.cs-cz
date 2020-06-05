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
ms.openlocfilehash: 3ed06cca7e20da381b870e642a6c616b2578cd0a
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451872"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Skupiny Azure AD, role pro správu a uživatelsky definované role

Od [Luke Latham](https://github.com/guardrex) a [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) poskytuje několik autorizačních přístupů, které lze kombinovat s ASP.NET Core Identity :

* Uživatelem definované skupiny
  * Zabezpečení
  * O365
  * Distribuce
* Role
  * Předdefinované role pro správu
  * Uživatelsky definované role

Pokyny v tomto článku se týkají Blazor scénářů nasazení AAD WebAssembly popsaných v následujících tématech:

* [Samostatná aplikace využívající účty Microsoft](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [Samostatná aplikace využívající Azure Active Directory](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [Hostovaná aplikace využívající Azure Active Directory](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>Uživatelsky definované skupiny a předdefinované role pro správu

Postup konfigurace aplikace v Azure Portal k poskytnutí `groups` deklarace identity členství najdete v následujících článcích Azure. Přiřaďte uživatele k uživatelem definovaným skupinám AAD a integrovaným rolím pro správu.

* [Role používající skupiny zabezpečení Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [groupMembershipClaims – atribut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

V následujících příkladech se předpokládá, že je uživatel přiřazený k předdefinované roli *správce fakturace* AAD.

Jediná `groups` deklarace ODESÍLANÁ AAD prezentuje skupiny uživatelů a role jako ID objektů (GUID) v poli JSON. Aplikace musí převést pole JSON skupin a rolí na jednotlivé `group` deklarace identity, pro které může aplikace sestavovat [zásady](xref:security/authorization/policies) .

Rozšíříte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> tak, aby zahrnovalo vlastnosti pole pro skupiny a role.

*CustomUserAccount.cs*:

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

Vytvořte vlastní objekt pro vytváření uživatelů v samostatné aplikaci nebo klientské aplikaci hostovaného řešení. Následující objekt pro vytváření je také nakonfigurovaný pro zpracování `roles` polí deklarací identity, která jsou popsaná v části [uživatelsky definované role](#user-defined-roles) :

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

Není nutné zadávat kód pro odebrání původní `groups` deklarace identity, protože je automaticky odebrána rozhraním Framework.

Registrace továrny v `Program.Main` (*program.cs*) samostatné aplikace nebo klientské aplikace hostovaného řešení:

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

Vytvořte [zásadu](xref:security/authorization/policies) pro každou skupinu nebo roli v `Program.Main` . Následující příklad vytvoří zásadu pro předdefinovanou *fakturační roli správce* služby AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Úplný seznam ID objektů role AAD najdete v části [ID skupin rolí AAD](#aad-adminstrative-role-group-ids) .

V následujících příkladech používá aplikace k autorizaci uživatele předchozí zásady.

[Komponenta AuthorizeView](xref:security/blazor/index#authorizeview-component) spolupracuje s touto zásadou:

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

Přístup k celé komponentě může být založen na zásadách pomocí `[Authorize]` direktivy atributu []] (odkazy XREF: Security/blazor/index # autorizovat-Attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Pokud uživatel není přihlášený, bude přesměrován na přihlašovací stránku AAD a pak se po přihlášení znovu k součásti.

Kontrolu zásad lze také [provést v kódu s procedurální logikou](xref:security/blazor/index#procedural-logic):

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

### <a name="user-defined-roles"></a>Uživatelsky definované role

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

V `CustomUserFactory` části [uživatelsky definované skupiny a předdefinované role pro správu AAD](#user-defined-groups-and-built-in-administrative-roles) je nastavené tak, aby se jednalo o `roles` deklaraci identity s hodnotou pole JSON. Přidejte a zaregistrujte se `CustomUserFactory` do samostatné aplikace nebo klientské aplikace hostovaného řešení, jak je znázorněno v části [uživatelsky definované skupiny a předdefinované role pro správu AAD](#user-defined-groups-and-built-in-administrative-roles) . Není nutné zadávat kód pro odebrání původní `roles` deklarace identity, protože je automaticky odebrána rozhraním Framework.

V `Program.Main` samostatné aplikaci nebo klientské aplikaci hostovaného řešení zadejte deklaraci identity s názvem `role` jako deklaraci identity role:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

Přístupy k autorizaci komponent jsou v tuto chvíli funkční. Kterýkoli z autorizačních mechanismů v součástech může použít `admin` roli k autorizaci uživatele:

* [AuthorizeView – komponenta](xref:security/blazor/index#authorizeview-component) (příklad: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` ] Attribute – direktiva] (odkazy XREF: Security/blazor/index # autorizovat-Attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (Příklad: `@attribute [Authorize(Roles = "admin")]` )
* [Procedurální logika](xref:security/blazor/index#procedural-logic) (příklad: `if (user.IsInRole("admin")) { ... }` )

  Podporuje se vícenásobné testy rolí:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>ID skupiny rolí pro správu AAD

ID objektů prezentovaná v následující tabulce se používají k vytváření [zásad](xref:security/authorization/policies) pro `group` deklarace identity. Zásady umožňují aplikaci ověřovat uživatele pro různé aktivity v aplikaci. Další informace najdete v části [uživatelsky definované skupiny a předdefinované role pro správu AAD](#user-defined-groups-and-built-in-administrative-roles) .

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

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
