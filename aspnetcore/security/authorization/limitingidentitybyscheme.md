---
title: Autorizovat s konkrétní schéma v ASP.NET Core
author: rick-anderson
description: Tento článek vysvětluje, jak omezit identity na konkrétní schéma při práci s několika metod ověřování.
ms.author: riande
ms.date: 10/22/2018
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 778bb61f472ab2e76f85da5999d3c79238188f19
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64903003"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="1f453-103">Autorizovat s konkrétní schéma v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f453-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="1f453-104">V některých případech, například jednostránkové aplikace (SPA) je běžné použití více metod ověřování.</span><span class="sxs-lookup"><span data-stu-id="1f453-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="1f453-105">Aplikace může například používat ověřování na základě souborů cookie pro přihlášení a ověřování nosného tokenu JWT pro požadavky jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1f453-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="1f453-106">V některých případech se aplikace může mít více instancí obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="1f453-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="1f453-107">Například dvě obslužné rutiny souborů cookie kde jedna obsahuje základní identitu a jedno se vytvoří při aktivaci služby Multi-Factor authentication (MFA).</span><span class="sxs-lookup"><span data-stu-id="1f453-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="1f453-108">Vícefaktorové ověřování můžou být vyvolány, protože uživatel si vyžádal operace, která vyžaduje dodatečné zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="1f453-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1f453-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1f453-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1f453-110">Schéma ověřování má název, pokud je nakonfigurovaná služba ověřování během ověřování.</span><span class="sxs-lookup"><span data-stu-id="1f453-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="1f453-111">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1f453-111">For example:</span></span>

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

<span data-ttu-id="1f453-112">V předchozím kódu byly přidány dva ověřování obslužných rutin: jednu pro soubory cookie a jednu pro nosiče.</span><span class="sxs-lookup"><span data-stu-id="1f453-112">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="1f453-113">Určení výchozí schéma vede `HttpContext.User` nastavenou na tuto identitu.</span><span class="sxs-lookup"><span data-stu-id="1f453-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="1f453-114">Pokud toto chování není žádoucí, zakažte vyvoláním konstruktor bez parametrů formu `AddAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="1f453-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1f453-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1f453-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1f453-116">Schémata ověřování jsou pojmenovány při ověřování middlewares se nastavily při ověřování.</span><span class="sxs-lookup"><span data-stu-id="1f453-116">Authentication schemes are named when authentication middlewares are configured during authentication.</span></span> <span data-ttu-id="1f453-117">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1f453-117">For example:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    // Code omitted for brevity

    app.UseCookieAuthentication(new CookieAuthenticationOptions()
    {
        AuthenticationScheme = "Cookie",
        LoginPath = "/Account/Unauthorized/",
        AccessDeniedPath = "/Account/Forbidden/",
        AutomaticAuthenticate = false
    });
    
    app.UseJwtBearerAuthentication(new JwtBearerOptions()
    {
        AuthenticationScheme = "Bearer",
        AutomaticAuthenticate = false,
        Audience = "http://localhost:5001/",
        Authority = "http://localhost:5000/",
        RequireHttpsMetadata = false
    });
```

<span data-ttu-id="1f453-118">V předchozím kódu byly přidány dva middlewares ověřování: jednu pro soubory cookie a jednu pro nosiče.</span><span class="sxs-lookup"><span data-stu-id="1f453-118">In the preceding code, two authentication middlewares have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="1f453-119">Určení výchozí schéma vede `HttpContext.User` nastavenou na tuto identitu.</span><span class="sxs-lookup"><span data-stu-id="1f453-119">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="1f453-120">Pokud toto chování není žádoucí, zakázat nastavením `AuthenticationOptions.AutomaticAuthenticate` vlastnost `false`.</span><span class="sxs-lookup"><span data-stu-id="1f453-120">If that behavior isn't desired, disable it by setting the `AuthenticationOptions.AutomaticAuthenticate` property to `false`.</span></span>

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="1f453-121">Výběr schématu s atributem Authorize</span><span class="sxs-lookup"><span data-stu-id="1f453-121">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="1f453-122">Přejme během autorizace aplikace udává, že obslužná rutina, který se má použít.</span><span class="sxs-lookup"><span data-stu-id="1f453-122">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="1f453-123">Vyberte obslužnou rutinu, pomocí kterého aplikace předáním čárkami oddělený seznam režimů ověřování k ověření `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="1f453-123">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="1f453-124">`[Authorize]` Atribut určuje schéma ověřování nebo použití bez ohledu na to, jestli je nakonfigurovaná výchozí schémata.</span><span class="sxs-lookup"><span data-stu-id="1f453-124">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="1f453-125">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1f453-125">For example:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1f453-126">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1f453-126">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1f453-127">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1f453-127">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

---

<span data-ttu-id="1f453-128">V předchozím příkladu se souborem cookie nebo nosiče obslužné rutiny spustit a mít možnost vytvořit a připojit identitu pro aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="1f453-128">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="1f453-129">Zadáním jednoho schéma pouze odpovídající obslužná rutina se spustí.</span><span class="sxs-lookup"><span data-stu-id="1f453-129">By specifying a single scheme only, the corresponding handler runs.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1f453-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1f453-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1f453-131">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1f453-131">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

---

<span data-ttu-id="1f453-132">V předchozím kódu pouze obslužná rutina se schématem "Nosiče" se spustí.</span><span class="sxs-lookup"><span data-stu-id="1f453-132">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="1f453-133">Jsou ignorovány všechny identity na základě souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="1f453-133">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="1f453-134">Výběr schématu se zásadami</span><span class="sxs-lookup"><span data-stu-id="1f453-134">Selecting the scheme with policies</span></span>

<span data-ttu-id="1f453-135">Pokud chcete zadat požadované schémata v [zásady](xref:security/authorization/policies), můžete nastavit `AuthenticationSchemes` kolekce při přidání zásady:</span><span class="sxs-lookup"><span data-stu-id="1f453-135">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

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

<span data-ttu-id="1f453-136">V předchozím příkladu spuštěno "Over18" zásady pouze u identity vytvoří "Nosiče" obslužnou rutinou.</span><span class="sxs-lookup"><span data-stu-id="1f453-136">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="1f453-137">Použijte zásady tak, že nastavíte `[Authorize]` atributu `Policy` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="1f453-137">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="1f453-138">Použití více schémat ověřování</span><span class="sxs-lookup"><span data-stu-id="1f453-138">Use multiple authentication schemes</span></span>

<span data-ttu-id="1f453-139">Některé aplikace může potřebovat pro podporu více typů ověřování.</span><span class="sxs-lookup"><span data-stu-id="1f453-139">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="1f453-140">Vaše aplikace může například ověřovat uživatele ze služby Azure Active Directory a z databáze uživatelů.</span><span class="sxs-lookup"><span data-stu-id="1f453-140">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="1f453-141">Dalším příkladem je aplikace, která ověřuje uživatele z Active Directory Federation Services a Azure Active Directory B2C.</span><span class="sxs-lookup"><span data-stu-id="1f453-141">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="1f453-142">V tomto případě aplikace by měla přijímat nosný token JWT z několika vystavitelů.</span><span class="sxs-lookup"><span data-stu-id="1f453-142">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="1f453-143">Přidáte všechna schémata ověřování, které chcete přijmout.</span><span class="sxs-lookup"><span data-stu-id="1f453-143">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="1f453-144">Například následující kód na `Startup.ConfigureServices` přidá dva režimy ověřování nosiče JWT s jinou vydavatelů:</span><span class="sxs-lookup"><span data-stu-id="1f453-144">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

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
> <span data-ttu-id="1f453-145">Pouze jeden ověřování nosného tokenu JWT je registrovaný pomocí výchozího schématu ověřování `JwtBearerDefaults.AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="1f453-145">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="1f453-146">Další ověření musí být registrována pomocí jedinečného ověřování schématu.</span><span class="sxs-lookup"><span data-stu-id="1f453-146">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="1f453-147">Dalším krokem je aktualizace výchozích zásad autorizace tak, aby přijímal obou režimů ověřování.</span><span class="sxs-lookup"><span data-stu-id="1f453-147">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="1f453-148">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1f453-148">For example:</span></span>

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

<span data-ttu-id="1f453-149">Jako výchozí zásady autorizace je přepsána, je možné použít `[Authorize]` atribut v zařízení.</span><span class="sxs-lookup"><span data-stu-id="1f453-149">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="1f453-150">Kontrolér přijímá požadavky pak pomocí tokenů JWT vydaného vystavitele první nebo druhé.</span><span class="sxs-lookup"><span data-stu-id="1f453-150">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end
