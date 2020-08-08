---
title: Konfigurace ASP.NET CoreIdentity
author: AdrienTorris
description: Pochopení ASP.NET Core Identity výchozích hodnot a Naučte se konfigurovat Identity vlastnosti pro použití vlastních hodnot.
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: b7f6eaba1a0e819a077e3d63b4f997e75b8cd317
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020597"
---
# <a name="configure-aspnet-core-no-locidentity"></a>Konfigurace ASP.NET CoreIdentity

ASP.NET Core Identity používá výchozí hodnoty pro nastavení, jako jsou zásady hesla, uzamčení a cookie konfigurace. Tato nastavení lze přepsat ve `Startup` třídě.

## <a name="no-locidentity-options"></a>Identitynastavení

Třída [ Identity Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) představuje možnosti, které lze použít ke konfiguraci Identity systému. `IdentityOptions`musí být nastavena **po** volání `AddIdentity` nebo `AddDefaultIdentity` .

### <a name="claims-no-locidentity"></a>PodporyIdentity

[ Identity Options. Claims Identity ](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) Určuje [ Identity Možnosti deklarací identity](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) s vlastnostmi, které jsou uvedeny v následující tabulce.

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Získá nebo nastaví typ deklarace identity použitý pro deklaraci identity role. | [ClaimTypes. role](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Získá nebo nastaví typ deklarace identity použitý pro deklaraci identity razítka zabezpečení. | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Získá nebo nastaví typ deklarace identity použitý pro deklaraci identity uživatelského identifikátoru. | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Získá nebo nastaví typ deklarace identity, který se používá pro deklaraci identity uživatelského jména. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Účtů

V metodě [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) je nastavené uzamčení:

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

Předchozí kód je založen na `Login` Identity šabloně. 

Možnosti uzamčení se nastavují v `StartUp.ConfigureServices` :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

Předchozí kód nastaví [ Identity Možnosti](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) s výchozími hodnotami.

Úspěšné ověření obnoví počet neúspěšných pokusů o přístup a resetuje hodiny.

[ Identity Options. zamknutí](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) Určuje [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) s vlastnostmi zobrazenými v tabulce.

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Určuje, zda může být nový uživatel uzamčen. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | Doba, po kterou je uživatel uzamčený, když dojde k uzamčení. | 5 minut |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | Počet neúspěšných pokusů o přístup, pokud je uživatel uzamčený, pokud je povolené uzamknutí. | 5 |

### <a name="password"></a>Heslo

Ve výchozím nastavení Identity vyžaduje, aby hesla obsahovala velké písmeno, malý znak, číslici a nealfanumerický znak. Heslo musí obsahovat alespoň šest znaků.

Hesla jsou nakonfigurovaná pomocí:

* <xref:Microsoft.AspNetCore.Identity.PasswordOptions>v `Startup.ConfigureServices` .
* [ `[StringLength]` atributy](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` vlastností, pokud Identity jsou [do aplikace vygenerované uživatelské rozhraní](xref:security/authentication/scaffold-identity). `InputModel``Password`vlastnosti se nacházejí v následujících souborech:
  * `Areas/Identity/Pages/Account/Register.cshtml.cs`
  * `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

[ Identity Options. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) Určuje [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) s vlastnostmi zobrazenými v tabulce.

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Vyžaduje číslo od 0-9 do hesla. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Minimální délka hesla. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Vyžaduje v hesle malý znak. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Vyžaduje v hesle jiný než alfanumerický znak. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Platí jenom pro ASP.NET Core 2,0 nebo novější.<br><br> Vyžaduje počet různých znaků v hesle. | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Vyžaduje velké písmeno v hesle. | `true` |

### <a name="sign-in"></a>Přihlášení

Následující nastavení sady kódů `SignIn` (na výchozí hodnoty):

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

[ Identity Options. prohlašování](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) Určuje [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) s vlastnostmi zobrazenými v tabulce.

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Pro přihlášení vyžaduje potvrzující e-mail. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | K přihlášení vyžaduje potvrzené telefonní číslo. | `false` |

### <a name="tokens"></a>Tokeny

[ Identity Options. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) Určuje [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) s vlastnostmi zobrazenými v tabulce.

| Vlastnost | Popis |
| -------- | ----------- |
| [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider) | Získá nebo nastaví `AuthenticatorTokenProvider` použitou pro ověření dvou přihlašování pomocí ověřovatele. |
| [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider) | Získá nebo nastaví `ChangeEmailTokenProvider` pro vygenerování tokenů použitých v e-mailech s potvrzením změny e-mailu. |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) | Získá nebo nastaví `ChangePhoneNumberTokenProvider` vygenerování tokenů používaných při změně telefonních čísel. |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) | Získá nebo nastaví poskytovatele tokenu pro vygenerování tokenů používaných v e-mailech s potvrzením účtu. |
| [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider) | Získá nebo nastaví [IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) sloužící k vygenerování tokenů používaných v e-mailech pro resetování hesla. |
| [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap) | Slouží k vytvoření [poskytovatele tokenu uživatele](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) s klíčem použitým jako název poskytovatele. |

### <a name="user"></a>Uživatel

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[ Identity Options. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) Určuje [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) s vlastnostmi zobrazenými v tabulce.

| Vlastnost | Popis | Výchozí |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Povolené znaky v uživatelském jménu | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Vyžaduje, aby každý uživatel měl jedinečný e-mail. | `false` |

### <a name="no-loccookie-settings"></a>NastaveníCookie

Nakonfigurujte aplikaci cookie v `Startup.ConfigureServices` . [ConfigureApplication Cookie ](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) musí být volána **po** volání `AddIdentity` nebo `AddDefaultIdentity` .

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

Další informace najdete v tématu [ Cookie AuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Možnosti hash hesla

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Získá a nastaví možnosti pro hodnoty hash hesla.

| Možnost | Popis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | Režim kompatibility, který se používá při vytváření hodnoty hash nových hesel Výchozí hodnota je <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . První bajt hesla s algoritmem hash, který se nazývá *značka formátu*, určuje verzi algoritmu hash, která se používá k hashování hesla. Při ověřování hesla proti hodnotě hash <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> vybere metoda správný algoritmus na základě prvního bajtu. Klient se může ověřit bez ohledu na to, která verze algoritmu se použila k hashování hesla. Nastavení režimu kompatibility má vliv na hashování *nových hesel*. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | Počet iterací použitých při použití algoritmu hash pro hesla pomocí PBKDF2. Tato hodnota se používá pouze v případě, že <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> je nastavena na <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . Hodnota musí být kladné celé číslo a výchozí hodnota je `10000` . |

V následujícím příkladu <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> je nastavena na hodnotu `12000` v `Startup.ConfigureServices` :

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Globálně vyžadovat ověření všech uživatelů

[!INCLUDE[](~/includes/requireAuth.md)]