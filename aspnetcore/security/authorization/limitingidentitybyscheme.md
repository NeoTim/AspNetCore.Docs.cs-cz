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
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>Autorizovat pomocí konkrétního schématu v ASP.NET Core

V některých scénářích, jako jsou například jednostránkové aplikace (jednostránkové), je běžné použít více metod ověřování. Aplikace může například použít ověřování pomocí souborů cookie k přihlášení a ověření nosiče JWT pro požadavky JavaScriptu. V některých případech může aplikace mít několik instancí obslužné rutiny ověřování. Například dva obslužné rutiny souborů cookie, kde jedna obsahuje základní identitu a která je vytvořena, když byla aktivována aplikace Multi-Factor Authentication (MFA). Vícefaktorové ověřování může být aktivováno, protože uživatel požadoval operaci, která vyžaduje dodatečné zabezpečení. Další informace o vynucování MFA, když si uživatel vyžádá prostředek, který vyžaduje MFA, najdete v části věnované problému pro ochranu GitHubu [pomocí MFA](https://github.com/aspnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).

Schéma ověřování se jmenuje, když je ověřovací služba nakonfigurovaná během ověřování. Příklad:

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

V předchozím kódu byly přidány dvě obslužné rutiny ověřování: jeden pro soubory cookie a jeden pro nosič.

>[!NOTE]
>Zadáním výchozího schématu dojde k nastavení vlastnosti `HttpContext.User` na tuto identitu. Pokud toto chování nepřejete, zakažte ho tak, že vyvoláte neparametrovou podobu `AddAuthentication`.

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Výběr schématu pomocí atributu autorizovat

V bodě autorizace aplikace označuje obslužnou rutinu, která se má použít. Vyberte obslužnou rutinu, se kterou bude aplikace autorizována předáním seznamu schémat ověřování oddělených čárkami, které se mají `[Authorize]`. Atribut `[Authorize]` Určuje schéma ověřování nebo schémata, které se mají použít bez ohledu na to, jestli je nakonfigurovaná výchozí hodnota. Příklad:

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

V předchozím příkladu jsou oba obslužné rutiny cookie a nosiče a mají možnost vytvořit a připojit identitu pro aktuálního uživatele. Zadáním jediného schématu se spustí odpovídající obslužná rutina.

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

V předchozím kódu se spouští jenom obslužná rutina se schématem "nosič". Všechny identity založené na souborech cookie se ignorují.

## <a name="selecting-the-scheme-with-policies"></a>Výběr schématu pomocí zásad

Pokud upřednostňujete určení požadovaných schémat v [zásadách](xref:security/authorization/policies), můžete nastavit kolekci `AuthenticationSchemes` při přidávání zásady:

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

V předchozím příkladu se zásada "Over18" spouští pouze proti identitě vytvořené obslužnou rutinou "nosiče". Pomocí zásady nastavte vlastnost `Policy` atributu `[Authorize]`:

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>Použití několika schémat ověřování

Některé aplikace můžou potřebovat podporu více typů ověřování. Vaše aplikace může například ověřovat uživatele z Azure Active Directory a z databáze uživatelů. Dalším příkladem je aplikace, která ověřuje uživatele z Active Directory Federation Services (AD FS) i Azure Active Directory B2C. V takovém případě by měla aplikace přijmout nosný token JWT od několika vystavitelů.

Přidejte všechna schémata ověřování, která chcete přijmout. Například následující kód v `Startup.ConfigureServices` přidá dvě ověřovací schémata JWT Bearer s různými vystaviteli:

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
> U výchozího `JwtBearerDefaults.AuthenticationScheme`schématu ověřování je zaregistrováno pouze jedno ověření nosiče JWT. Další ověřování musí být registrováno pomocí jedinečného schématu ověřování.

Dalším krokem je aktualizace výchozích zásad autorizace pro přijímání obou ověřovacích schémat. Příklad:

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

Vzhledem k tomu, že výchozí zásada autorizace je přepsána, je možné použít atribut `[Authorize]` v řadičích. Kontroler pak přijme žádosti s tokenem JWT vydaným prvním nebo druhým vystavitelem.

::: moniker-end
