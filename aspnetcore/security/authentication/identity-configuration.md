---
title: Konfigurace ASP.NET CoreIdentity
author: AdrienTorris
description: Pochopení ASP.NET Core Identity výchozích hodnot a Naučte se konfigurovat Identity vlastnosti pro použití vlastních hodnot.
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: 95c19b671602b45ba217dcb551110854cbbee359
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408965"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="c133f-103">Konfigurace ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="c133f-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="c133f-104">ASP.NET Core Identity používá výchozí hodnoty pro nastavení, jako jsou zásady hesla, uzamčení a konfigurace souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="c133f-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="c133f-105">Tato nastavení lze přepsat ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="c133f-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a>Identity<span data-ttu-id="c133f-106">nastavení</span><span class="sxs-lookup"><span data-stu-id="c133f-106"> options</span></span>

<span data-ttu-id="c133f-107">Třída [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) představuje možnosti, které lze použít ke konfiguraci Identity systému.</span><span class="sxs-lookup"><span data-stu-id="c133f-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="c133f-108">`IdentityOptions`musí být nastavena **po** volání `AddIdentity` nebo `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="c133f-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="c133f-109">PodporyIdentity</span><span class="sxs-lookup"><span data-stu-id="c133f-109">Claims Identity</span></span>

<span data-ttu-id="c133f-110">[IdentityOptions. hodnota ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) Určuje [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) s vlastnostmi, které jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="c133f-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="c133f-111">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="c133f-111">Property</span></span> | <span data-ttu-id="c133f-112">Popis</span><span class="sxs-lookup"><span data-stu-id="c133f-112">Description</span></span> | <span data-ttu-id="c133f-113">Výchozí</span><span class="sxs-lookup"><span data-stu-id="c133f-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="c133f-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="c133f-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="c133f-115">Získá nebo nastaví typ deklarace identity použitý pro deklaraci identity role.</span><span class="sxs-lookup"><span data-stu-id="c133f-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="c133f-116">ClaimTypes. role</span><span class="sxs-lookup"><span data-stu-id="c133f-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="c133f-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="c133f-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="c133f-118">Získá nebo nastaví typ deklarace identity použitý pro deklaraci identity razítka zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="c133f-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="c133f-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="c133f-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="c133f-120">Získá nebo nastaví typ deklarace identity použitý pro deklaraci identity uživatelského identifikátoru.</span><span class="sxs-lookup"><span data-stu-id="c133f-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="c133f-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="c133f-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="c133f-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="c133f-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="c133f-123">Získá nebo nastaví typ deklarace identity, který se používá pro deklaraci identity uživatelského jména.</span><span class="sxs-lookup"><span data-stu-id="c133f-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="c133f-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="c133f-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="c133f-125">Účtů</span><span class="sxs-lookup"><span data-stu-id="c133f-125">Lockout</span></span>

<span data-ttu-id="c133f-126">V metodě [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) je nastavené uzamčení:</span><span class="sxs-lookup"><span data-stu-id="c133f-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="c133f-127">Předchozí kód je založen na `Login` Identity šabloně.</span><span class="sxs-lookup"><span data-stu-id="c133f-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="c133f-128">Možnosti uzamčení se nastavují v `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c133f-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="c133f-129">Předchozí kód nastaví [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) s výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="c133f-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="c133f-130">Úspěšné ověření obnoví počet neúspěšných pokusů o přístup a resetuje hodiny.</span><span class="sxs-lookup"><span data-stu-id="c133f-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="c133f-131">[IdentityOptions. zamknutí](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) Určuje [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) s vlastnostmi zobrazenými v tabulce.</span><span class="sxs-lookup"><span data-stu-id="c133f-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="c133f-132">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="c133f-132">Property</span></span> | <span data-ttu-id="c133f-133">Popis</span><span class="sxs-lookup"><span data-stu-id="c133f-133">Description</span></span> | <span data-ttu-id="c133f-134">Výchozí</span><span class="sxs-lookup"><span data-stu-id="c133f-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="c133f-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="c133f-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="c133f-136">Určuje, zda může být nový uživatel uzamčen.</span><span class="sxs-lookup"><span data-stu-id="c133f-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="c133f-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="c133f-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="c133f-138">Doba, po kterou je uživatel uzamčený, když dojde k uzamčení.</span><span class="sxs-lookup"><span data-stu-id="c133f-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="c133f-139">5 minut</span><span class="sxs-lookup"><span data-stu-id="c133f-139">5 minutes</span></span> |
| [<span data-ttu-id="c133f-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="c133f-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="c133f-141">Počet neúspěšných pokusů o přístup, pokud je uživatel uzamčený, pokud je povolené uzamknutí.</span><span class="sxs-lookup"><span data-stu-id="c133f-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="c133f-142">5</span><span class="sxs-lookup"><span data-stu-id="c133f-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="c133f-143">Heslo</span><span class="sxs-lookup"><span data-stu-id="c133f-143">Password</span></span>

<span data-ttu-id="c133f-144">Ve výchozím nastavení Identity vyžaduje, aby hesla obsahovala velké písmeno, malý znak, číslici a nealfanumerický znak.</span><span class="sxs-lookup"><span data-stu-id="c133f-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="c133f-145">Heslo musí obsahovat alespoň šest znaků.</span><span class="sxs-lookup"><span data-stu-id="c133f-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="c133f-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) lze nastavit v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c133f-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="c133f-147">[IdentityOptions. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) Určuje [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) s vlastnostmi zobrazenými v tabulce.</span><span class="sxs-lookup"><span data-stu-id="c133f-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="c133f-148">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="c133f-148">Property</span></span> | <span data-ttu-id="c133f-149">Popis</span><span class="sxs-lookup"><span data-stu-id="c133f-149">Description</span></span> | <span data-ttu-id="c133f-150">Výchozí</span><span class="sxs-lookup"><span data-stu-id="c133f-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="c133f-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="c133f-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="c133f-152">Vyžaduje číslo od 0-9 do hesla.</span><span class="sxs-lookup"><span data-stu-id="c133f-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="c133f-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="c133f-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="c133f-154">Minimální délka hesla.</span><span class="sxs-lookup"><span data-stu-id="c133f-154">The minimum length of the password.</span></span> | <span data-ttu-id="c133f-155">6</span><span class="sxs-lookup"><span data-stu-id="c133f-155">6</span></span> |
| [<span data-ttu-id="c133f-156">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="c133f-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="c133f-157">Vyžaduje v hesle malý znak.</span><span class="sxs-lookup"><span data-stu-id="c133f-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="c133f-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="c133f-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="c133f-159">Vyžaduje v hesle jiný než alfanumerický znak.</span><span class="sxs-lookup"><span data-stu-id="c133f-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="c133f-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="c133f-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="c133f-161">Platí jenom pro ASP.NET Core 2,0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="c133f-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="c133f-162">Vyžaduje počet různých znaků v hesle.</span><span class="sxs-lookup"><span data-stu-id="c133f-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="c133f-163">1</span><span class="sxs-lookup"><span data-stu-id="c133f-163">1</span></span> |
| [<span data-ttu-id="c133f-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="c133f-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="c133f-165">Vyžaduje velké písmeno v hesle.</span><span class="sxs-lookup"><span data-stu-id="c133f-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="c133f-166">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="c133f-166">Property</span></span> | <span data-ttu-id="c133f-167">Popis</span><span class="sxs-lookup"><span data-stu-id="c133f-167">Description</span></span> | <span data-ttu-id="c133f-168">Výchozí</span><span class="sxs-lookup"><span data-stu-id="c133f-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="c133f-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="c133f-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="c133f-170">Vyžaduje číslo od 0-9 do hesla.</span><span class="sxs-lookup"><span data-stu-id="c133f-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="c133f-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="c133f-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="c133f-172">Minimální délka hesla.</span><span class="sxs-lookup"><span data-stu-id="c133f-172">The minimum length of the password.</span></span> | <span data-ttu-id="c133f-173">6</span><span class="sxs-lookup"><span data-stu-id="c133f-173">6</span></span> |
| [<span data-ttu-id="c133f-174">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="c133f-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="c133f-175">Vyžaduje v hesle malý znak.</span><span class="sxs-lookup"><span data-stu-id="c133f-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="c133f-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="c133f-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="c133f-177">Vyžaduje v hesle jiný než alfanumerický znak.</span><span class="sxs-lookup"><span data-stu-id="c133f-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="c133f-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="c133f-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="c133f-179">Vyžaduje velké písmeno v hesle.</span><span class="sxs-lookup"><span data-stu-id="c133f-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="c133f-180">Přihlášení</span><span class="sxs-lookup"><span data-stu-id="c133f-180">Sign-in</span></span>

<span data-ttu-id="c133f-181">Následující nastavení sady kódů `SignIn` (na výchozí hodnoty):</span><span class="sxs-lookup"><span data-stu-id="c133f-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="c133f-182">[IdentityOptions. prohlašování](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) Určuje [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) s vlastnostmi zobrazenými v tabulce.</span><span class="sxs-lookup"><span data-stu-id="c133f-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="c133f-183">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="c133f-183">Property</span></span> | <span data-ttu-id="c133f-184">Popis</span><span class="sxs-lookup"><span data-stu-id="c133f-184">Description</span></span> | <span data-ttu-id="c133f-185">Výchozí</span><span class="sxs-lookup"><span data-stu-id="c133f-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="c133f-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="c133f-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="c133f-187">Pro přihlášení vyžaduje potvrzující e-mail.</span><span class="sxs-lookup"><span data-stu-id="c133f-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="c133f-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="c133f-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="c133f-189">K přihlášení vyžaduje potvrzené telefonní číslo.</span><span class="sxs-lookup"><span data-stu-id="c133f-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="c133f-190">Tokeny</span><span class="sxs-lookup"><span data-stu-id="c133f-190">Tokens</span></span>

<span data-ttu-id="c133f-191">[IdentityOptions. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) Určuje [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) s vlastnostmi zobrazenými v tabulce.</span><span class="sxs-lookup"><span data-stu-id="c133f-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="c133f-192">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="c133f-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="c133f-193">Popis</span><span class="sxs-lookup"><span data-stu-id="c133f-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="c133f-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="c133f-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="c133f-195">Získá nebo nastaví `AuthenticatorTokenProvider` použitou pro ověření dvou přihlašování pomocí ověřovatele.</span><span class="sxs-lookup"><span data-stu-id="c133f-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="c133f-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="c133f-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="c133f-197">Získá nebo nastaví `ChangeEmailTokenProvider` pro vygenerování tokenů použitých v e-mailech s potvrzením změny e-mailu.</span><span class="sxs-lookup"><span data-stu-id="c133f-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="c133f-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="c133f-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="c133f-199">Získá nebo nastaví `ChangePhoneNumberTokenProvider` vygenerování tokenů používaných při změně telefonních čísel.</span><span class="sxs-lookup"><span data-stu-id="c133f-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="c133f-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="c133f-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="c133f-201">Získá nebo nastaví poskytovatele tokenu pro vygenerování tokenů používaných v e-mailech s potvrzením účtu.</span><span class="sxs-lookup"><span data-stu-id="c133f-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="c133f-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="c133f-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="c133f-203">Získá nebo nastaví [IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) sloužící k vygenerování tokenů používaných v e-mailech pro resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="c133f-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="c133f-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="c133f-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="c133f-205">Slouží k vytvoření [poskytovatele tokenu uživatele](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) s klíčem použitým jako název poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="c133f-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="c133f-206">Uživatel</span><span class="sxs-lookup"><span data-stu-id="c133f-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="c133f-207">[IdentityOptions. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) Určuje [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) s vlastnostmi zobrazenými v tabulce.</span><span class="sxs-lookup"><span data-stu-id="c133f-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="c133f-208">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="c133f-208">Property</span></span> | <span data-ttu-id="c133f-209">Popis</span><span class="sxs-lookup"><span data-stu-id="c133f-209">Description</span></span> | <span data-ttu-id="c133f-210">Výchozí</span><span class="sxs-lookup"><span data-stu-id="c133f-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="c133f-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="c133f-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="c133f-212">Povolené znaky v uživatelském jménu</span><span class="sxs-lookup"><span data-stu-id="c133f-212">Allowed characters in the username.</span></span> | <span data-ttu-id="c133f-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="c133f-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="c133f-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="c133f-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="c133f-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="c133f-215">0123456789</span></span><br><span data-ttu-id="c133f-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="c133f-216">-.\_@+</span></span> |
| [<span data-ttu-id="c133f-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="c133f-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="c133f-218">Vyžaduje, aby každý uživatel měl jedinečný e-mail.</span><span class="sxs-lookup"><span data-stu-id="c133f-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="c133f-219">Nastavení souborů cookie</span><span class="sxs-lookup"><span data-stu-id="c133f-219">Cookie settings</span></span>

<span data-ttu-id="c133f-220">Nakonfigurujte soubor cookie aplikace v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c133f-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c133f-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) se musí volat **po** volání `AddIdentity` nebo `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="c133f-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="c133f-222">Další informace najdete v tématu [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span><span class="sxs-lookup"><span data-stu-id="c133f-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="c133f-223">Možnosti hash hesla</span><span class="sxs-lookup"><span data-stu-id="c133f-223">Password Hasher options</span></span>

<span data-ttu-id="c133f-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Získá a nastaví možnosti pro hodnoty hash hesla.</span><span class="sxs-lookup"><span data-stu-id="c133f-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="c133f-225">Možnost</span><span class="sxs-lookup"><span data-stu-id="c133f-225">Option</span></span> | <span data-ttu-id="c133f-226">Description</span><span class="sxs-lookup"><span data-stu-id="c133f-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="c133f-227">Režim kompatibility, který se používá při vytváření hodnoty hash nových hesel</span><span class="sxs-lookup"><span data-stu-id="c133f-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="c133f-228">Výchozí hodnota je <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> .</span><span class="sxs-lookup"><span data-stu-id="c133f-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="c133f-229">První bajt hesla s algoritmem hash, který se nazývá *značka formátu*, určuje verzi algoritmu hash, která se používá k hashování hesla.</span><span class="sxs-lookup"><span data-stu-id="c133f-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="c133f-230">Při ověřování hesla proti hodnotě hash <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> vybere metoda správný algoritmus na základě prvního bajtu.</span><span class="sxs-lookup"><span data-stu-id="c133f-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="c133f-231">Klient se může ověřit bez ohledu na to, která verze algoritmu se použila k hashování hesla.</span><span class="sxs-lookup"><span data-stu-id="c133f-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="c133f-232">Nastavení režimu kompatibility má vliv na hashování *nových hesel*.</span><span class="sxs-lookup"><span data-stu-id="c133f-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="c133f-233">Počet iterací použitých při použití algoritmu hash pro hesla pomocí PBKDF2.</span><span class="sxs-lookup"><span data-stu-id="c133f-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="c133f-234">Tato hodnota se používá pouze v případě, že <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> je nastavena na <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> .</span><span class="sxs-lookup"><span data-stu-id="c133f-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="c133f-235">Hodnota musí být kladné celé číslo a výchozí hodnota je `10000` .</span><span class="sxs-lookup"><span data-stu-id="c133f-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="c133f-236">V následujícím příkladu <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> je nastavena na hodnotu `12000` v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c133f-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
