---
title: Autorizovat pomocí konkrétního schématu v ASP.NET Core
author: rick-anderson
description: Tento článek vysvětluje, jak omezit identitu na konkrétní schéma při práci s více metodami ověřování.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: f52f6ec9c557add2c66105397eb2733a0dcb9e87
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635187"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="65731-103">Autorizovat pomocí konkrétního schématu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65731-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="65731-104">V některých scénářích, jako jsou například jednostránkové aplikace (jednostránkové), je běžné použít více metod ověřování.</span><span class="sxs-lookup"><span data-stu-id="65731-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="65731-105">Aplikace může například použít cookie ověřování k přihlášení a ověření nosiče JWT pro požadavky JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="65731-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="65731-106">V některých případech může aplikace mít několik instancí obslužné rutiny ověřování.</span><span class="sxs-lookup"><span data-stu-id="65731-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="65731-107">Například dvě cookie obslužné rutiny, kde jedna obsahuje základní identitu a která je vytvořena, když byla aktivována aplikace Multi-Factor Authentication (MFA).</span><span class="sxs-lookup"><span data-stu-id="65731-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="65731-108">Vícefaktorové ověřování může být aktivováno, protože uživatel požadoval operaci, která vyžaduje dodatečné zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="65731-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="65731-109">Další informace o vynucování MFA, když si uživatel vyžádá prostředek, který vyžaduje MFA, najdete v části věnované problému pro ochranu GitHubu [pomocí MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span><span class="sxs-lookup"><span data-stu-id="65731-109">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="65731-110">Schéma ověřování se jmenuje, když je ověřovací služba nakonfigurovaná během ověřování.</span><span class="sxs-lookup"><span data-stu-id="65731-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="65731-111">Příklad:</span><span class="sxs-lookup"><span data-stu-id="65731-111">For example:</span></span>

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

<span data-ttu-id="65731-112">V předchozím kódu byly přidány dvě obslužné rutiny ověřování: jeden pro cookie s a jeden pro nosič.</span><span class="sxs-lookup"><span data-stu-id="65731-112">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="65731-113">Zadáním výchozího schématu dojde `HttpContext.User` k nastavení vlastnosti na tuto identitu.</span><span class="sxs-lookup"><span data-stu-id="65731-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="65731-114">Pokud toto chování nepřejete, zakažte ho voláním formuláře bez parametrů `AddAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="65731-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="65731-115">Výběr schématu pomocí atributu autorizovat</span><span class="sxs-lookup"><span data-stu-id="65731-115">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="65731-116">V bodě autorizace aplikace označuje obslužnou rutinu, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="65731-116">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="65731-117">Vyberte obslužnou rutinu, se kterou bude aplikace autorizována předáním seznamu schémat ověřování, které jsou odděleny čárkami `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="65731-117">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="65731-118">`[Authorize]`Atribut určuje schéma ověřování nebo schémata, které se mají použít bez ohledu na to, jestli je nakonfigurované výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="65731-118">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="65731-119">Příklad:</span><span class="sxs-lookup"><span data-stu-id="65731-119">For example:</span></span>

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

<span data-ttu-id="65731-120">V předchozím příkladu cookie obslužné rutiny i nosiče jsou spuštěny a mají možnost vytvořit a připojit identitu pro aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="65731-120">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="65731-121">Zadáním jediného schématu se spustí odpovídající obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="65731-121">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="65731-122">V předchozím kódu se spouští jenom obslužná rutina se schématem "nosič".</span><span class="sxs-lookup"><span data-stu-id="65731-122">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="65731-123">Všechny cookie identity založené na základě jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="65731-123">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="65731-124">Výběr schématu pomocí zásad</span><span class="sxs-lookup"><span data-stu-id="65731-124">Selecting the scheme with policies</span></span>

<span data-ttu-id="65731-125">Pokud upřednostňujete určení požadovaných schémat v [zásadách](xref:security/authorization/policies), můžete `AuthenticationSchemes` kolekci nastavit při přidávání zásady:</span><span class="sxs-lookup"><span data-stu-id="65731-125">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

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

<span data-ttu-id="65731-126">V předchozím příkladu se zásada "Over18" spouští pouze proti identitě vytvořené obslužnou rutinou "nosiče".</span><span class="sxs-lookup"><span data-stu-id="65731-126">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="65731-127">Zásadu použijte nastavením `[Authorize]` `Policy` vlastnosti atributu:</span><span class="sxs-lookup"><span data-stu-id="65731-127">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="65731-128">Použití několika schémat ověřování</span><span class="sxs-lookup"><span data-stu-id="65731-128">Use multiple authentication schemes</span></span>

<span data-ttu-id="65731-129">Některé aplikace můžou potřebovat podporu více typů ověřování.</span><span class="sxs-lookup"><span data-stu-id="65731-129">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="65731-130">Vaše aplikace může například ověřovat uživatele z Azure Active Directory a z databáze uživatelů.</span><span class="sxs-lookup"><span data-stu-id="65731-130">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="65731-131">Dalším příkladem je aplikace, která ověřuje uživatele z Active Directory Federation Services (AD FS) i Azure Active Directory B2C.</span><span class="sxs-lookup"><span data-stu-id="65731-131">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="65731-132">V takovém případě by měla aplikace přijmout nosný token JWT od několika vystavitelů.</span><span class="sxs-lookup"><span data-stu-id="65731-132">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="65731-133">Přidejte všechna schémata ověřování, která chcete přijmout.</span><span class="sxs-lookup"><span data-stu-id="65731-133">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="65731-134">Například následující kód v `Startup.ConfigureServices` nástroji přidává dvě ověřovací schémata JWT Bearer s různými vystaviteli:</span><span class="sxs-lookup"><span data-stu-id="65731-134">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

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
> <span data-ttu-id="65731-135">U výchozího schématu ověřování je zaregistrováno pouze jedno ověření nosiče JWT `JwtBearerDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="65731-135">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="65731-136">Další ověřování musí být registrováno pomocí jedinečného schématu ověřování.</span><span class="sxs-lookup"><span data-stu-id="65731-136">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="65731-137">Dalším krokem je aktualizace výchozích zásad autorizace pro přijímání obou ověřovacích schémat.</span><span class="sxs-lookup"><span data-stu-id="65731-137">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="65731-138">Příklad:</span><span class="sxs-lookup"><span data-stu-id="65731-138">For example:</span></span>

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

<span data-ttu-id="65731-139">Vzhledem k tomu, že výchozí zásada autorizace je přepsána, je možné použít `[Authorize]` atribut v řadičích.</span><span class="sxs-lookup"><span data-stu-id="65731-139">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="65731-140">Kontroler pak přijme žádosti s tokenem JWT vydaným prvním nebo druhým vystavitelem.</span><span class="sxs-lookup"><span data-stu-id="65731-140">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end
