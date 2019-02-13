---
title: Konfigurace ASP.NET Core Identity
author: AdrienTorris
description: ASP.NET Core Identity výchozí hodnoty a zjistěte, jak nakonfigurovat vlastnosti Identity použít vlastní hodnoty.
ms.author: riande
ms.date: 02/11/2019
uid: security/authentication/identity-configuration
ms.openlocfilehash: 3213f669cbfccdcda7cc7c0142b8101e696678e6
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159510"
---
# <a name="configure-aspnet-core-identity"></a>Konfigurace ASP.NET Core Identity

ASP.NET Core Identity používá výchozí hodnoty pro nastavení jako zásady hesel, uzamčení a konfigurace souboru cookie. Tato nastavení mohou být přepsána nastaveními v `Startup` třídy.

## <a name="identity-options"></a>Možnosti identity

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) třída reprezentuje možnosti, které lze použít ke konfiguraci systému identit. `IdentityOptions` musí být nastavena **po** volání `AddIdentity` nebo `AddDefaultIdentity`.

### <a name="claims-identity"></a>Deklarace Identity

[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) Určuje, [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) s vlastnostmi, které jsou uvedeny v následující tabulce.

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Získá nebo nastaví typ deklarace použitý pro deklarace role. | [ClaimTypes.Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Získá nebo nastaví typ deklarace použitý pro deklarace identity razítko zabezpečení. | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Získá nebo nastaví typ deklarace použitý pro deklarace identity identifikátor uživatele. | [ClaimTypes.NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Získá nebo nastaví typ deklarace použitý pro deklarace identity uživatele název. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Uzamčení

Uzamčení se nastavuje v [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) metody:

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

Předchozí kód je založený na `Login` Identity šablony. 

Možnosti uzamčení se nastavují v `StartUp.ConfigureServices`:

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

Předchozí kód nastaví [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) s výchozími hodnotami.

Po provedení úspěšného ověření resetuje počet pokusů o neúspěšných přístupů a resetuje na hodiny.

[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) Určuje, [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) s vlastnostmi, které jsou uvedené v tabulce.

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Určuje, pokud nového uživatele lze uzamknout. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | Množství času uživatele je uzamčen když dojde k uzamčení. | 5 minut |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | Počet neúspěšných pokusů o přístup dokud uživatel je uzamčen, pokud je povoleno uzamčení. | 5 |

### <a name="password"></a>Heslo

Ve výchozím nastavení identita vyžaduje, aby hesla obsahovat velké písmeno, malé písmeno, číslici a jiný než alfanumerický znak. Hesla musí být aspoň šest znaků dlouhá. [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) je možné nastavit v `Startup.ConfigureServices`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) Určuje, [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) s vlastnostmi, které jsou uvedené v tabulce.

::: moniker range=">= aspnetcore-2.0"

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Vyžaduje číslo v rozmezí 0 – 9 v hesle. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Minimální délka hesla. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Vyžaduje malé písmeno v hesle. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Vyžaduje nealfanumerických znaků v hesle. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Platí jenom pro ASP.NET Core 2.0 nebo novější.<br><br> Vyžaduje počet jedinečných znaků v hesle. | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Vyžaduje velké písmeno heslo. | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Vyžaduje číslo v rozmezí 0 – 9 v hesle. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Minimální délka hesla. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Vyžaduje malé písmeno v hesle. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Vyžaduje nealfanumerických znaků v hesle. | `true` |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Vyžaduje velké písmeno heslo. | `true` |

::: moniker-end

### <a name="sign-in"></a>přihlášení

Následující kód nastaví `SignIn` nastavení (výchozí hodnoty):

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) Určuje, [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) s vlastnostmi, které jsou uvedené v tabulce.

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Vyžaduje potvrzeno e-mailu pro přihlášení. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Vyžaduje potvrzeno telefonní číslo pro přihlášení. | `false` |

### <a name="tokens"></a>Tokeny

[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) Určuje, [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) s vlastnostmi, které jsou uvedené v tabulce.


|                                                        Vlastnost                                                         |                                                                                      Popis                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       Získá nebo nastaví `AuthenticatorTokenProvider` použitý k ověření dvojúrovňového přihlášení pomocí ověřovací data.                                       |
|       [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     Získá nebo nastaví `ChangeEmailTokenProvider` sloužící ke generování tokenů, které používá e-mailu změnit potvrzení e-mailů.                                     |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      Získá nebo nastaví `ChangePhoneNumberTokenProvider` sloužící ke generování tokenů při změně telefonního čísla.                                      |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             Získá nebo nastaví poskytovatele tokenu, kterého používá ke generování tokenů použít účet potvrzení e-mailů.                                              |
|     [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | Získá nebo nastaví [IUserTwoFactorTokenProvider<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) sloužící ke generování tokenů, které používá e-mailů resetování hesla. |
|                    [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                Slouží k vytvoření [Poskytovatel tokenu uživatele](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) s klíč použít jako název zprostředkovatele.                 |

### <a name="user"></a>Uživatel

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) Určuje, [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) s vlastnostmi, které jsou uvedené v tabulce.

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Povolené znaky uživatelské jméno. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Vyžaduje každý uživatel měl jedinečnou e-mailovou. | `false` |

### <a name="cookie-settings"></a>Nastavení souborů cookie

Konfigurovat soubor cookie aplikace v `Startup.ConfigureServices`. [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) musí být volána **po** volání `AddIdentity` nebo `AddDefaultIdentity`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

Další informace najdete v tématu [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Hasher možnosti hesla

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> Získá a nastaví možnosti pro vytvoření hodnoty hash hesla.

| Možnost | Popis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | Režim kompatibility, který se používá při výpočtu hodnoty hash nová hesla. Výchozí hodnota je <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>. První bajt hodnoty hash hesla, volá se *formát značky*, určuje verzi modulu hashovací algoritmus používaný k vytvoření hodnoty hash hesla. Při ověřování proti hodnotu hash hesla <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> metoda vybere správný algoritmus založený na prvním bajtem. Klient je možné ověřit bez ohledu na to, z nichž byla použita verze algoritmu hodnoty hash hesla. Nastavení režimu kompatibility ovlivní hodnotami hash *nová hesla*. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | Počet použitých při výpočtu hodnoty hash hesla, pomocí PBKDF2 iterací. Tato hodnota je pouze použit, když <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> je nastavena na <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>. Hodnota musí být kladné celé číslo a výchozí hodnota je `10000`. |

V následujícím příkladu <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> je nastavena na `12000` v `Startup.ConfigureServices`:

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
