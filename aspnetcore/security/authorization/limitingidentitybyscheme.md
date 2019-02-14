---
title: Autorizovat s konkrétní schéma v ASP.NET Core
author: rick-anderson
description: Tento článek vysvětluje, jak omezit identity na konkrétní schéma při práci s několika metod ověřování.
ms.author: riande
ms.date: 10/22/2018
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 778bb61f472ab2e76f85da5999d3c79238188f19
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248196"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>Autorizovat s konkrétní schéma v ASP.NET Core

V některých případech, například jednostránkové aplikace (SPA) je běžné použití více metod ověřování. Aplikace může například používat ověřování na základě souborů cookie pro přihlášení a ověřování nosného tokenu JWT pro požadavky jazyka JavaScript. V některých případech se aplikace může mít více instancí obslužnou rutinu ověřování. Například dvě obslužné rutiny souborů cookie kde jedna obsahuje základní identitu a jedno se vytvoří při aktivaci služby Multi-Factor authentication (MFA). Vícefaktorové ověřování můžou být vyvolány, protože uživatel si vyžádal operace, která vyžaduje dodatečné zabezpečení.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Schéma ověřování má název, pokud je nakonfigurovaná služba ověřování během ověřování. Příklad:

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

V předchozím kódu byly přidány dva ověřování obslužných rutin: jednu pro soubory cookie a jednu pro nosiče.

>[!NOTE]
>Určení výchozí schéma vede `HttpContext.User` nastavenou na tuto identitu. Pokud toto chování není žádoucí, zakažte vyvoláním konstruktor bez parametrů formu `AddAuthentication`.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Schémata ověřování jsou pojmenovány při ověřování middlewares se nastavily při ověřování. Příklad:

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

V předchozím kódu byly přidány dva middlewares ověřování: jednu pro soubory cookie a jednu pro nosiče.

>[!NOTE]
>Určení výchozí schéma vede `HttpContext.User` nastavenou na tuto identitu. Pokud toto chování není žádoucí, zakázat nastavením `AuthenticationOptions.AutomaticAuthenticate` vlastnost `false`.

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Výběr schématu s atributem Authorize

Přejme během autorizace aplikace udává, že obslužná rutina, který se má použít. Vyberte obslužnou rutinu, pomocí kterého aplikace předáním čárkami oddělený seznam režimů ověřování k ověření `[Authorize]`. `[Authorize]` Atribut určuje schéma ověřování nebo použití bez ohledu na to, jestli je nakonfigurovaná výchozí schémata. Příklad:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

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

V předchozím příkladu se souborem cookie nebo nosiče obslužné rutiny spustit a mít možnost vytvořit a připojit identitu pro aktuálního uživatele. Zadáním jednoho schéma pouze odpovídající obslužná rutina se spustí.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

---

V předchozím kódu pouze obslužná rutina se schématem "Nosiče" se spustí. Jsou ignorovány všechny identity na základě souboru cookie.

## <a name="selecting-the-scheme-with-policies"></a>Výběr schématu se zásadami

Pokud chcete zadat požadované schémata v [zásady](xref:security/authorization/policies), můžete nastavit `AuthenticationSchemes` kolekce při přidání zásady:

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

V předchozím příkladu spuštěno "Over18" zásady pouze u identity vytvoří "Nosiče" obslužnou rutinou. Použijte zásady tak, že nastavíte `[Authorize]` atributu `Policy` vlastnost:

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>Použití více schémat ověřování

Některé aplikace může potřebovat pro podporu více typů ověřování. Vaše aplikace může například ověřovat uživatele ze služby Azure Active Directory a z databáze uživatelů. Dalším příkladem je aplikace, která ověřuje uživatele z Active Directory Federation Services a Azure Active Directory B2C. V tomto případě aplikace by měla přijímat nosný token JWT z několika vystavitelů.

Přidáte všechna schémata ověřování, které chcete přijmout. Například následující kód na `Startup.ConfigureServices` přidá dva režimy ověřování nosiče JWT s jinou vydavatelů:

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
> Pouze jeden ověřování nosného tokenu JWT je registrovaný pomocí výchozího schématu ověřování `JwtBearerDefaults.AuthenticationScheme`. Další ověření musí být registrována pomocí jedinečného ověřování schématu.

Dalším krokem je aktualizace výchozích zásad autorizace tak, aby přijímal obou režimů ověřování. Příklad:

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

Jako výchozí zásady autorizace je přepsána, je možné použít `[Authorize]` atribut v zařízení. Kontrolér přijímá požadavky pak pomocí tokenů JWT vydaného vystavitele první nebo druhé.

::: moniker-end
