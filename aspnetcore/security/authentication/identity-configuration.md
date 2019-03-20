---
title: Konfigurace ASP.NET Core Identity
author: AdrienTorris
description: ASP.NET Core Identity výchozí hodnoty a zjistěte, jak nakonfigurovat vlastnosti Identity použít vlastní hodnoty.
ms.author: riande
ms.date: 02/11/2019
uid: security/authentication/identity-configuration
ms.openlocfilehash: 823182bed2cb953e07f9374d135868aeb2be9c60
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210116"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="eace5-103">Konfigurace ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="eace5-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="eace5-104">ASP.NET Core Identity používá výchozí hodnoty pro nastavení jako zásady hesel, uzamčení a konfigurace souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="eace5-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="eace5-105">Tato nastavení mohou být přepsána nastaveními v `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="eace5-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a><span data-ttu-id="eace5-106">Možnosti identity</span><span class="sxs-lookup"><span data-stu-id="eace5-106">Identity options</span></span>

<span data-ttu-id="eace5-107">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) třída reprezentuje možnosti, které lze použít ke konfiguraci systému identit.</span><span class="sxs-lookup"><span data-stu-id="eace5-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="eace5-108">`IdentityOptions` musí být nastavena **po** volání `AddIdentity` nebo `AddDefaultIdentity`.</span><span class="sxs-lookup"><span data-stu-id="eace5-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="eace5-109">Deklarace Identity</span><span class="sxs-lookup"><span data-stu-id="eace5-109">Claims Identity</span></span>

<span data-ttu-id="eace5-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) Určuje, [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) s vlastnostmi, které jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="eace5-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="eace5-111">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="eace5-111">Property</span></span> | <span data-ttu-id="eace5-112">Popis</span><span class="sxs-lookup"><span data-stu-id="eace5-112">Description</span></span> | <span data-ttu-id="eace5-113">Výchozí</span><span class="sxs-lookup"><span data-stu-id="eace5-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="eace5-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="eace5-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="eace5-115">Získá nebo nastaví typ deklarace použitý pro deklarace role.</span><span class="sxs-lookup"><span data-stu-id="eace5-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="eace5-116">ClaimTypes.Role</span><span class="sxs-lookup"><span data-stu-id="eace5-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="eace5-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="eace5-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="eace5-118">Získá nebo nastaví typ deklarace použitý pro deklarace identity razítko zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="eace5-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="eace5-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="eace5-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="eace5-120">Získá nebo nastaví typ deklarace použitý pro deklarace identity identifikátor uživatele.</span><span class="sxs-lookup"><span data-stu-id="eace5-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="eace5-121">ClaimTypes.NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="eace5-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="eace5-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="eace5-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="eace5-123">Získá nebo nastaví typ deklarace použitý pro deklarace identity uživatele název.</span><span class="sxs-lookup"><span data-stu-id="eace5-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="eace5-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="eace5-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="eace5-125">Uzamčení</span><span class="sxs-lookup"><span data-stu-id="eace5-125">Lockout</span></span>

<span data-ttu-id="eace5-126">Uzamčení se nastavuje v [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) metody:</span><span class="sxs-lookup"><span data-stu-id="eace5-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="eace5-127">Předchozí kód je založený na `Login` Identity šablony.</span><span class="sxs-lookup"><span data-stu-id="eace5-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="eace5-128">Možnosti uzamčení se nastavují v `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eace5-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="eace5-129">Předchozí kód nastaví [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) s výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="eace5-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="eace5-130">Po provedení úspěšného ověření resetuje počet pokusů o neúspěšných přístupů a resetuje na hodiny.</span><span class="sxs-lookup"><span data-stu-id="eace5-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="eace5-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) Určuje, [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) s vlastnostmi, které jsou uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="eace5-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="eace5-132">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="eace5-132">Property</span></span> | <span data-ttu-id="eace5-133">Popis</span><span class="sxs-lookup"><span data-stu-id="eace5-133">Description</span></span> | <span data-ttu-id="eace5-134">Výchozí</span><span class="sxs-lookup"><span data-stu-id="eace5-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="eace5-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="eace5-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="eace5-136">Určuje, pokud nového uživatele lze uzamknout.</span><span class="sxs-lookup"><span data-stu-id="eace5-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="eace5-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="eace5-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="eace5-138">Množství času uživatele je uzamčen když dojde k uzamčení.</span><span class="sxs-lookup"><span data-stu-id="eace5-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="eace5-139">5 minut</span><span class="sxs-lookup"><span data-stu-id="eace5-139">5 minutes</span></span> |
| [<span data-ttu-id="eace5-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="eace5-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="eace5-141">Počet neúspěšných pokusů o přístup dokud uživatel je uzamčen, pokud je povoleno uzamčení.</span><span class="sxs-lookup"><span data-stu-id="eace5-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="eace5-142">5</span><span class="sxs-lookup"><span data-stu-id="eace5-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="eace5-143">Heslo</span><span class="sxs-lookup"><span data-stu-id="eace5-143">Password</span></span>

<span data-ttu-id="eace5-144">Ve výchozím nastavení identita vyžaduje, aby hesla obsahovat velké písmeno, malé písmeno, číslici a jiný než alfanumerický znak.</span><span class="sxs-lookup"><span data-stu-id="eace5-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="eace5-145">Hesla musí být aspoň šest znaků dlouhá.</span><span class="sxs-lookup"><span data-stu-id="eace5-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="eace5-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) je možné nastavit v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eace5-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="eace5-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) Určuje, [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) s vlastnostmi, které jsou uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="eace5-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="eace5-148">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="eace5-148">Property</span></span> | <span data-ttu-id="eace5-149">Popis</span><span class="sxs-lookup"><span data-stu-id="eace5-149">Description</span></span> | <span data-ttu-id="eace5-150">Výchozí</span><span class="sxs-lookup"><span data-stu-id="eace5-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="eace5-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="eace5-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="eace5-152">Vyžaduje číslo v rozmezí 0 – 9 v hesle.</span><span class="sxs-lookup"><span data-stu-id="eace5-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="eace5-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="eace5-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="eace5-154">Minimální délka hesla.</span><span class="sxs-lookup"><span data-stu-id="eace5-154">The minimum length of the password.</span></span> | <span data-ttu-id="eace5-155">6</span><span class="sxs-lookup"><span data-stu-id="eace5-155">6</span></span> |
| [<span data-ttu-id="eace5-156">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="eace5-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="eace5-157">Vyžaduje malé písmeno v hesle.</span><span class="sxs-lookup"><span data-stu-id="eace5-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="eace5-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="eace5-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="eace5-159">Vyžaduje nealfanumerických znaků v hesle.</span><span class="sxs-lookup"><span data-stu-id="eace5-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="eace5-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="eace5-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="eace5-161">Platí jenom pro ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="eace5-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="eace5-162">Vyžaduje počet jedinečných znaků v hesle.</span><span class="sxs-lookup"><span data-stu-id="eace5-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="eace5-163">1</span><span class="sxs-lookup"><span data-stu-id="eace5-163">1</span></span> |
| [<span data-ttu-id="eace5-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="eace5-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="eace5-165">Vyžaduje velké písmeno heslo.</span><span class="sxs-lookup"><span data-stu-id="eace5-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="eace5-166">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="eace5-166">Property</span></span> | <span data-ttu-id="eace5-167">Popis</span><span class="sxs-lookup"><span data-stu-id="eace5-167">Description</span></span> | <span data-ttu-id="eace5-168">Výchozí</span><span class="sxs-lookup"><span data-stu-id="eace5-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="eace5-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="eace5-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="eace5-170">Vyžaduje číslo v rozmezí 0 – 9 v hesle.</span><span class="sxs-lookup"><span data-stu-id="eace5-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="eace5-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="eace5-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="eace5-172">Minimální délka hesla.</span><span class="sxs-lookup"><span data-stu-id="eace5-172">The minimum length of the password.</span></span> | <span data-ttu-id="eace5-173">6</span><span class="sxs-lookup"><span data-stu-id="eace5-173">6</span></span> |
| [<span data-ttu-id="eace5-174">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="eace5-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="eace5-175">Vyžaduje malé písmeno v hesle.</span><span class="sxs-lookup"><span data-stu-id="eace5-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="eace5-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="eace5-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="eace5-177">Vyžaduje nealfanumerických znaků v hesle.</span><span class="sxs-lookup"><span data-stu-id="eace5-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="eace5-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="eace5-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="eace5-179">Vyžaduje velké písmeno heslo.</span><span class="sxs-lookup"><span data-stu-id="eace5-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="eace5-180">přihlášení</span><span class="sxs-lookup"><span data-stu-id="eace5-180">Sign-in</span></span>

<span data-ttu-id="eace5-181">Následující kód nastaví `SignIn` nastavení (výchozí hodnoty):</span><span class="sxs-lookup"><span data-stu-id="eace5-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="eace5-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) Určuje, [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) s vlastnostmi, které jsou uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="eace5-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="eace5-183">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="eace5-183">Property</span></span> | <span data-ttu-id="eace5-184">Popis</span><span class="sxs-lookup"><span data-stu-id="eace5-184">Description</span></span> | <span data-ttu-id="eace5-185">Výchozí</span><span class="sxs-lookup"><span data-stu-id="eace5-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="eace5-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="eace5-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="eace5-187">Vyžaduje potvrzeno e-mailu pro přihlášení.</span><span class="sxs-lookup"><span data-stu-id="eace5-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="eace5-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="eace5-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="eace5-189">Vyžaduje potvrzeno telefonní číslo pro přihlášení.</span><span class="sxs-lookup"><span data-stu-id="eace5-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="eace5-190">Tokeny</span><span class="sxs-lookup"><span data-stu-id="eace5-190">Tokens</span></span>

<span data-ttu-id="eace5-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) Určuje, [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) s vlastnostmi, které jsou uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="eace5-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="eace5-192">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="eace5-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="eace5-193">Popis</span><span class="sxs-lookup"><span data-stu-id="eace5-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="eace5-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="eace5-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="eace5-195">Získá nebo nastaví `AuthenticatorTokenProvider` použitý k ověření dvojúrovňového přihlášení pomocí ověřovací data.</span><span class="sxs-lookup"><span data-stu-id="eace5-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="eace5-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="eace5-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="eace5-197">Získá nebo nastaví `ChangeEmailTokenProvider` sloužící ke generování tokenů, které používá e-mailu změnit potvrzení e-mailů.</span><span class="sxs-lookup"><span data-stu-id="eace5-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="eace5-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="eace5-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="eace5-199">Získá nebo nastaví `ChangePhoneNumberTokenProvider` sloužící ke generování tokenů při změně telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="eace5-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="eace5-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="eace5-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="eace5-201">Získá nebo nastaví poskytovatele tokenu, kterého používá ke generování tokenů použít účet potvrzení e-mailů.</span><span class="sxs-lookup"><span data-stu-id="eace5-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="eace5-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="eace5-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="eace5-203">Získá nebo nastaví [IUserTwoFactorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) sloužící ke generování tokenů, které používá e-mailů resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="eace5-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="eace5-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="eace5-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="eace5-205">Slouží k vytvoření [Poskytovatel tokenu uživatele](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) s klíč použít jako název zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="eace5-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="eace5-206">Uživatel</span><span class="sxs-lookup"><span data-stu-id="eace5-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="eace5-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) Určuje, [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) s vlastnostmi, které jsou uvedené v tabulce.</span><span class="sxs-lookup"><span data-stu-id="eace5-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="eace5-208">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="eace5-208">Property</span></span> | <span data-ttu-id="eace5-209">Popis</span><span class="sxs-lookup"><span data-stu-id="eace5-209">Description</span></span> | <span data-ttu-id="eace5-210">Výchozí</span><span class="sxs-lookup"><span data-stu-id="eace5-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="eace5-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="eace5-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="eace5-212">Povolené znaky uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="eace5-212">Allowed characters in the username.</span></span> | <span data-ttu-id="eace5-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="eace5-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="eace5-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="eace5-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="eace5-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="eace5-215">0123456789</span></span><br><span data-ttu-id="eace5-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="eace5-216">-.\_@+</span></span> |
| [<span data-ttu-id="eace5-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="eace5-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="eace5-218">Vyžaduje každý uživatel měl jedinečnou e-mailovou.</span><span class="sxs-lookup"><span data-stu-id="eace5-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="eace5-219">Nastavení souborů cookie</span><span class="sxs-lookup"><span data-stu-id="eace5-219">Cookie settings</span></span>

<span data-ttu-id="eace5-220">Konfigurovat soubor cookie aplikace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eace5-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eace5-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) musí být volána **po** volání `AddIdentity` nebo `AddDefaultIdentity`.</span><span class="sxs-lookup"><span data-stu-id="eace5-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="eace5-222">Další informace najdete v tématu [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span><span class="sxs-lookup"><span data-stu-id="eace5-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="eace5-223">Hasher možnosti hesla</span><span class="sxs-lookup"><span data-stu-id="eace5-223">Password Hasher options</span></span>

<span data-ttu-id="eace5-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> Získá a nastaví možnosti pro vytvoření hodnoty hash hesla.</span><span class="sxs-lookup"><span data-stu-id="eace5-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="eace5-225">Možnost</span><span class="sxs-lookup"><span data-stu-id="eace5-225">Option</span></span> | <span data-ttu-id="eace5-226">Popis</span><span class="sxs-lookup"><span data-stu-id="eace5-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="eace5-227">Režim kompatibility, který se používá při výpočtu hodnoty hash nová hesla.</span><span class="sxs-lookup"><span data-stu-id="eace5-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="eace5-228">Výchozí hodnota je <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span><span class="sxs-lookup"><span data-stu-id="eace5-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="eace5-229">První bajt hodnoty hash hesla, volá se *formát značky*, určuje verzi modulu hashovací algoritmus používaný k vytvoření hodnoty hash hesla.</span><span class="sxs-lookup"><span data-stu-id="eace5-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="eace5-230">Při ověřování proti hodnotu hash hesla <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> metoda vybere správný algoritmus založený na prvním bajtem.</span><span class="sxs-lookup"><span data-stu-id="eace5-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="eace5-231">Klient je možné ověřit bez ohledu na to, z nichž byla použita verze algoritmu hodnoty hash hesla.</span><span class="sxs-lookup"><span data-stu-id="eace5-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="eace5-232">Nastavení režimu kompatibility ovlivní hodnotami hash *nová hesla*.</span><span class="sxs-lookup"><span data-stu-id="eace5-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="eace5-233">Počet použitých při výpočtu hodnoty hash hesla, pomocí PBKDF2 iterací.</span><span class="sxs-lookup"><span data-stu-id="eace5-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="eace5-234">Tato hodnota je pouze použit, když <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> je nastavena na <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span><span class="sxs-lookup"><span data-stu-id="eace5-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="eace5-235">Hodnota musí být kladné celé číslo a výchozí hodnota je `10000`.</span><span class="sxs-lookup"><span data-stu-id="eace5-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="eace5-236">V následujícím příkladu <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> je nastavena na `12000` v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eace5-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
