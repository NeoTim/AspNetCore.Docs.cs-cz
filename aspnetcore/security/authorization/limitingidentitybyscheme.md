---
title: Autorizovat pomocí konkrétního schématu v ASP.NET Core
author: rick-anderson
description: Tento článek vysvětluje, jak omezit identitu na konkrétní schéma při práci s více metodami ověřování.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 9c173a4589279b03bc12b4b7dea594fae88cf471
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928387"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="95884-103">Autorizovat pomocí konkrétního schématu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95884-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="95884-104">V některých scénářích, jako jsou například jednostránkové aplikace (jednostránkové), je běžné použít více metod ověřování.</span><span class="sxs-lookup"><span data-stu-id="95884-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="95884-105">Aplikace může například použít ověřování pomocí souborů cookie k přihlášení a ověření nosiče JWT pro požadavky JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="95884-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="95884-106">V některých případech může aplikace mít několik instancí obslužné rutiny ověřování.</span><span class="sxs-lookup"><span data-stu-id="95884-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="95884-107">Například dva obslužné rutiny souborů cookie, kde jedna obsahuje základní identitu a která je vytvořena, když byla aktivována aplikace Multi-Factor Authentication (MFA).</span><span class="sxs-lookup"><span data-stu-id="95884-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="95884-108">Vícefaktorové ověřování může být aktivováno, protože uživatel požadoval operaci, která vyžaduje dodatečné zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="95884-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="95884-109">Další informace o vynucování MFA, když si uživatel vyžádá prostředek, který vyžaduje MFA, najdete v části věnované problému pro ochranu GitHubu [pomocí MFA](https://github.com/aspnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span><span class="sxs-lookup"><span data-stu-id="95884-109">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/aspnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="95884-110">Schéma ověřování se jmenuje, když je ověřovací služba nakonfigurovaná během ověřování.</span><span class="sxs-lookup"><span data-stu-id="95884-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="95884-111">Příklad:</span><span class="sxs-lookup"><span data-stu-id="95884-111">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="95884-112">V předchozím kódu byly přidány dvě obslužné rutiny ověřování: jeden pro soubory cookie a jeden pro nosič.</span><span class="sxs-lookup"><span data-stu-id="95884-112">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="95884-113">Zadáním výchozího schématu dojde k nastavení vlastnosti `HttpContext.User` na tuto identitu.</span><span class="sxs-lookup"><span data-stu-id="95884-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="95884-114">Pokud toto chování nepřejete, zakažte ho tak, že vyvoláte neparametrovou podobu `AddAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="95884-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="95884-115">Výběr schématu pomocí atributu autorizovat</span><span class="sxs-lookup"><span data-stu-id="95884-115">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="95884-116">V bodě autorizace aplikace označuje obslužnou rutinu, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="95884-116">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="95884-117">Vyberte obslužnou rutinu, se kterou bude aplikace autorizována předáním seznamu schémat ověřování oddělených čárkami, které se mají `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="95884-117">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="95884-118">Atribut `[Authorize]` Určuje schéma ověřování nebo schémata, které se mají použít bez ohledu na to, jestli je nakonfigurovaná výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="95884-118">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="95884-119">Příklad:</span><span class="sxs-lookup"><span data-stu-id="95884-119">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="95884-120">V předchozím příkladu jsou oba obslužné rutiny cookie a nosiče a mají možnost vytvořit a připojit identitu pro aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="95884-120">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="95884-121">Zadáním jediného schématu se spustí odpovídající obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="95884-121">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="95884-122">V předchozím kódu se spouští jenom obslužná rutina se schématem "nosič".</span><span class="sxs-lookup"><span data-stu-id="95884-122">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="95884-123">Všechny identity založené na souborech cookie se ignorují.</span><span class="sxs-lookup"><span data-stu-id="95884-123">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="95884-124">Výběr schématu pomocí zásad</span><span class="sxs-lookup"><span data-stu-id="95884-124">Selecting the scheme with policies</span></span>

<span data-ttu-id="95884-125">Pokud upřednostňujete určení požadovaných schémat v [zásadách](xref:security/authorization/policies), můžete nastavit kolekci `AuthenticationSchemes` při přidávání zásady:</span><span class="sxs-lookup"><span data-stu-id="95884-125">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="95884-126">V předchozím příkladu se zásada "Over18" spouští pouze proti identitě vytvořené obslužnou rutinou "nosiče".</span><span class="sxs-lookup"><span data-stu-id="95884-126">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="95884-127">Pomocí zásady nastavte vlastnost `Policy` atributu `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="95884-127">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="95884-128">Použití několika schémat ověřování</span><span class="sxs-lookup"><span data-stu-id="95884-128">Use multiple authentication schemes</span></span>

<span data-ttu-id="95884-129">Některé aplikace můžou potřebovat podporu více typů ověřování.</span><span class="sxs-lookup"><span data-stu-id="95884-129">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="95884-130">Vaše aplikace může například ověřovat uživatele z Azure Active Directory a z databáze uživatelů.</span><span class="sxs-lookup"><span data-stu-id="95884-130">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="95884-131">Dalším příkladem je aplikace, která ověřuje uživatele z Active Directory Federation Services (AD FS) i Azure Active Directory B2C.</span><span class="sxs-lookup"><span data-stu-id="95884-131">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="95884-132">V takovém případě by měla aplikace přijmout nosný token JWT od několika vystavitelů.</span><span class="sxs-lookup"><span data-stu-id="95884-132">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="95884-133">Přidejte všechna schémata ověřování, která chcete přijmout.</span><span class="sxs-lookup"><span data-stu-id="95884-133">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="95884-134">Například následující kód v `Startup.ConfigureServices` přidá dvě ověřovací schémata JWT Bearer s různými vystaviteli:</span><span class="sxs-lookup"><span data-stu-id="95884-134">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="95884-135">U výchozího `JwtBearerDefaults.AuthenticationScheme`schématu ověřování je zaregistrováno pouze jedno ověření nosiče JWT.</span><span class="sxs-lookup"><span data-stu-id="95884-135">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="95884-136">Další ověřování musí být registrováno pomocí jedinečného schématu ověřování.</span><span class="sxs-lookup"><span data-stu-id="95884-136">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="95884-137">Dalším krokem je aktualizace výchozích zásad autorizace pro přijímání obou ověřovacích schémat.</span><span class="sxs-lookup"><span data-stu-id="95884-137">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="95884-138">Příklad:</span><span class="sxs-lookup"><span data-stu-id="95884-138">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="95884-139">Vzhledem k tomu, že výchozí zásada autorizace je přepsána, je možné použít atribut `[Authorize]` v řadičích.</span><span class="sxs-lookup"><span data-stu-id="95884-139">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="95884-140">Kontroler pak přijme žádosti s tokenem JWT vydaným prvním nebo druhým vystavitelem.</span><span class="sxs-lookup"><span data-stu-id="95884-140">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end
