---
title: Autorizace založená na zásadách v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit a používat obslužné rutiny zásad autorizace pro vynucení požadavků na autorizaci v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488758"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Autorizace založená na zásadách v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Pod kryty, [autorizace založená na rolích](xref:security/authorization/roles) a [autorizace založená na deklaracích](xref:security/authorization/claims) používá požadavek, obslužnou rutinu požadavků a předem nakonfigurovanou zásadu. Tyto stavební bloky podporují vyjádření hodnocení autorizace v kódu. Výsledkem je bohatší, opakovaně použitelná, testovatelná autorizační struktura.

Zásady autorizace se skládají z jednoho nebo více požadavků. Je registrován jako součást konfigurace autorizační služby v metodě: `Startup.ConfigureServices`

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

V předchozím příkladu je vytvořena zásada "AtLeast21". Má jediný požadavek&mdash;na minimální věk, který je dodáván jako parametr požadavku.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Primární služba, která určuje, zda <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>je autorizace úspěšná, je :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Předchozí kód zvýrazní dvě metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda je autorizace úspěšná.

Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> z nich je zodpovědný za kontrolu, zda jsou splněny požadavky:
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

Třída <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> je to, co obslužná rutina používá k označení, zda byly splněny požadavky:

```csharp
 context.Succeed(requirement)
```

Následující kód zobrazuje zjednodušenou (a notoponos s poznámkami) výchozí implementaci autorizační služby:

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

Následující kód ukazuje `ConfigureServices`typické :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

Použití <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` nebo pro autorizaci.

## <a name="applying-policies-to-mvc-controllers"></a>Použití zásad na řadiče MVC

Pokud používáte Razor Pages, [přečtěte si téma Použití zásad pro stránky Razor](#applying-policies-to-razor-pages) v tomto dokumentu.

Zásady jsou použity pro `[Authorize]` řadiče pomocí atributu s názvem zásady. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Použití zásad na stránky Razor

Zásady jsou použity na `[Authorize]` Razor Pages pomocí atributu s názvem zásady. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Zásady lze také použít na Stránky Razor pomocí [konvence autorizace](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Požadavky

Požadavek na autorizaci je kolekce parametrů dat, které zásady mohou použít k vyhodnocení aktuálního uživatelského objektu. V naší politice "AtLeast21" je požadavkem jediný parametr&mdash;minimální věk. Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky. Parametrizovaný požadavek na minimální věk by mohl být implementován takto:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Pokud zásada autorizace obsahuje více požadavků na autorizaci, musí všechny požadavky projít, aby bylo vyhodnocení zásad úspěšné. Jinými slovy, více požadavků na autorizaci přidaných do jedné zásady autorizace jsou zpracovány na základě **AND.**

> [!NOTE]
> Požadavek nemusí mít data nebo vlastnosti.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Obslužné rutiny autorizace

Obslužná rutina autorizace je zodpovědná za vyhodnocení vlastností požadavku. Obslužná rutina autorizace vyhodnotí požadavky proti poskytnuté [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, zda je povolen přístup.

Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers). Obslužná rutina může `TRequirement` dědit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde je požadavek, který má být zpracován. Alternativně obslužná rutina může implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jeden typ požadavku.

### <a name="use-a-handler-for-one-requirement"></a>Použití obslužné rutiny pro jeden požadavek

<a name="security-authorization-handler-example"></a>

Následuje příklad vztahu 1:1, ve kterém obslužná rutina minimálního věku využívá jeden požadavek:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Předchozí kód určuje, zda aktuální zaregistrovaný objekt uživatele má datum narození deklarace, která byla vydána známé a důvěryhodné vystavitele. K autorizaci nemůže dojít, pokud deklarace chybí, v takovém případě je vrácena dokončená úloha. Pokud je k dispozici deklarace, vypočítá se věk uživatele. Pokud uživatel splňuje minimální věk definovaný požadavkem, autorizace se považuje za úspěšnou. Pokud je autorizace úspěšná, `context.Succeed` je vyvolána s splněným požadavkem jako jeho jediným parametrem.

### <a name="use-a-handler-for-multiple-requirements"></a>Použití obslužné rutiny pro více požadavků

Následuje příklad relace 1:N, ve které obslužná rutina oprávnění může zpracovat tři různé typy požadavků:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Předchozí kód prochází [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky, které nejsou označeny jako úspěšné. Pro `ReadPermission` požadavek musí být uživatel vlastníkem nebo sponzorem, aby měl přístup k požadovanému prostředku. V případě `EditPermission` nebo `DeletePermission` požadavku musí být vlastníkem, aby měl přístup k požadovanému prostředku.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registrace obslužné rutiny

Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace. Příklad:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Předchozí kód registruje `MinimumAgeHandler` jako singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Obslužné rutiny lze zaregistrovat pomocí některé [hod-do provozní doby](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>Co by měl obslužná rutina vrátit?

Všimněte `Handle` si, že metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) vrátí žádnou hodnotu. Jak je uveden stav úspěchu nebo neúspěchu?

* Obslužná `context.Succeed(IAuthorizationRequirement requirement)`rutina označuje úspěch voláním , předáním požadavku, který byl úspěšně ověřen.

* Obslužná rutina nemusí obecně zpracovávat chyby, protože ostatní obslužné rutiny pro stejný požadavek mohou být úspěšné.

* Chcete-li zaručit selhání, i v `context.Fail`případě, že ostatní obslužné rutiny požadavku úspěšné, volání .

Pokud volání `context.Succeed` obslužné rutiny nebo `context.Fail`, všechny ostatní obslužné rutiny jsou stále volány. To umožňuje požadavky na vedlejší účinky, jako je například protokolování, ke kterému dochází i v případě, že jiná obslužná rutina úspěšně ověřena nebo se nezdařilpožadavek. Pokud je `false`nastavena na , [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) vlastnost (k dispozici v ASP.NET Core 1.1 a novější) zkratuje provádění obslužné rutiny při `context.Fail` volání. `InvokeHandlersAfterFailure`výchozí hodnota `true`je na , v takovém případě jsou volány všechny obslužné rutiny.

> [!NOTE]
> Obslužné rutiny autorizace se nazývají i v případě, že se ověření nezdaří.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Proč bych měl chtít více obslužných rutin pro požadavek?

V případech, kdy chcete, aby hodnocení bylo na základě **or,** implementovat více obslužné rutiny pro jeden požadavek. Například Microsoft má dveře, které se otevírají pouze s klíčovými kartami. Pokud si necháte svou kartu doma, recepční vytiskne dočasnou nálepku a otevře vám dveře. V tomto scénáři byste měli jeden požadavek *BuildingEntry*, ale více obslužné rutiny, každý z nich zkoumá jeden požadavek.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Ujistěte se, že jsou [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)obě obslužné rutiny . Pokud buď obslužná rutina úspěšné při vyhodnotí zásady `BuildingEntryRequirement`, vyhodnocení zásady úspěšné.

## <a name="using-a-func-to-fulfill-a-policy"></a>Použití func ke splnění politiky

Mohou nastat situace, ve kterých je plnění zásady jednoduché vyjádřit v kódu. Je možné zadat při `Func<AuthorizationHandlerContext, bool>` konfiguraci zásad s `RequireAssertion` tvůrcem zásad.

Předchozí `BadgeEntryHandler` může být například přepsána takto:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Přístup k kontextu požadavku MVC v obslužných rutinách

Metoda, `HandleRequirementAsync` kterou implementujete v obslužné rutině autorizace, má dva parametry: a `AuthorizationHandlerContext` `TRequirement` zpracováváte. Architektury jako MVC nebo Jabbr mohou přidat libovolný `Resource` objekt `AuthorizationHandlerContext` do vlastnosti předat další informace.

Například MVC předá instanci [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) ve vlastnosti. `Resource` Tato vlastnost poskytuje `HttpContext` `RouteData`přístup k , a vše ostatní poskytované MVC a Razor stránky.

Použití vlastnosti `Resource` je specifické pro architekturu. Použití informací `Resource` ve vlastnosti omezuje zásady autorizace na konkrétní architektury. Měli byste `Resource` přetypovat `is` vlastnost pomocí klíčového slova a pak potvrďte, že `InvalidCastException` obsazení se podařilo zajistit, že váš kód není selhání s při spuštění na jiných rozhraních:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

Pod kryty, [autorizace založená na rolích](xref:security/authorization/roles) a [autorizace založená na deklaracích](xref:security/authorization/claims) používá požadavek, obslužnou rutinu požadavků a předem nakonfigurovanou zásadu. Tyto stavební bloky podporují vyjádření hodnocení autorizace v kódu. Výsledkem je bohatší, opakovaně použitelná, testovatelná autorizační struktura.

Zásady autorizace se skládají z jednoho nebo více požadavků. Je registrován jako součást konfigurace autorizační služby v metodě: `Startup.ConfigureServices`

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

V předchozím příkladu je vytvořena zásada "AtLeast21". Má jediný požadavek&mdash;na minimální věk, který je dodáván jako parametr požadavku.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Primární služba, která určuje, zda <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>je autorizace úspěšná, je :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Předchozí kód zvýrazní dvě metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda je autorizace úspěšná.

Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> z nich je zodpovědný za kontrolu, zda jsou splněny požadavky:
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

Třída <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> je to, co obslužná rutina používá k označení, zda byly splněny požadavky:

```csharp
 context.Succeed(requirement)
```

Následující kód zobrazuje zjednodušenou (a notoponos s poznámkami) výchozí implementaci autorizační služby:

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

Následující kód ukazuje `ConfigureServices`typické :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

Použití <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` nebo pro autorizaci.

## <a name="applying-policies-to-mvc-controllers"></a>Použití zásad na řadiče MVC

Pokud používáte Razor Pages, [přečtěte si téma Použití zásad pro stránky Razor](#applying-policies-to-razor-pages) v tomto dokumentu.

Zásady jsou použity pro `[Authorize]` řadiče pomocí atributu s názvem zásady. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Použití zásad na stránky Razor

Zásady jsou použity na `[Authorize]` Razor Pages pomocí atributu s názvem zásady. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Zásady lze také použít na Stránky Razor pomocí [konvence autorizace](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Požadavky

Požadavek na autorizaci je kolekce parametrů dat, které zásady mohou použít k vyhodnocení aktuálního uživatelského objektu. V naší politice "AtLeast21" je požadavkem jediný parametr&mdash;minimální věk. Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky. Parametrizovaný požadavek na minimální věk by mohl být implementován takto:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Pokud zásada autorizace obsahuje více požadavků na autorizaci, musí všechny požadavky projít, aby bylo vyhodnocení zásad úspěšné. Jinými slovy, více požadavků na autorizaci přidaných do jedné zásady autorizace jsou zpracovány na základě **AND.**

> [!NOTE]
> Požadavek nemusí mít data nebo vlastnosti.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Obslužné rutiny autorizace

Obslužná rutina autorizace je zodpovědná za vyhodnocení vlastností požadavku. Obslužná rutina autorizace vyhodnotí požadavky proti poskytnuté [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, zda je povolen přístup.

Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers). Obslužná rutina může `TRequirement` dědit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde je požadavek, který má být zpracován. Alternativně obslužná rutina může implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jeden typ požadavku.

### <a name="use-a-handler-for-one-requirement"></a>Použití obslužné rutiny pro jeden požadavek

<a name="security-authorization-handler-example"></a>

Následuje příklad vztahu 1:1, ve kterém obslužná rutina minimálního věku využívá jeden požadavek:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Předchozí kód určuje, zda aktuální zaregistrovaný objekt uživatele má datum narození deklarace, která byla vydána známé a důvěryhodné vystavitele. K autorizaci nemůže dojít, pokud deklarace chybí, v takovém případě je vrácena dokončená úloha. Pokud je k dispozici deklarace, vypočítá se věk uživatele. Pokud uživatel splňuje minimální věk definovaný požadavkem, autorizace se považuje za úspěšnou. Pokud je autorizace úspěšná, `context.Succeed` je vyvolána s splněným požadavkem jako jeho jediným parametrem.

### <a name="use-a-handler-for-multiple-requirements"></a>Použití obslužné rutiny pro více požadavků

Následuje příklad relace 1:N, ve které obslužná rutina oprávnění může zpracovat tři různé typy požadavků:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Předchozí kód prochází [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky, které nejsou označeny jako úspěšné. Pro `ReadPermission` požadavek musí být uživatel vlastníkem nebo sponzorem, aby měl přístup k požadovanému prostředku. V případě `EditPermission` nebo `DeletePermission` požadavku musí být vlastníkem, aby měl přístup k požadovanému prostředku.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registrace obslužné rutiny

Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Předchozí kód registruje `MinimumAgeHandler` jako singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Obslužné rutiny lze zaregistrovat pomocí některé [hod-do provozní doby](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>Co by měl obslužná rutina vrátit?

Všimněte `Handle` si, že metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) vrátí žádnou hodnotu. Jak je uveden stav úspěchu nebo neúspěchu?

* Obslužná `context.Succeed(IAuthorizationRequirement requirement)`rutina označuje úspěch voláním , předáním požadavku, který byl úspěšně ověřen.

* Obslužná rutina nemusí obecně zpracovávat chyby, protože ostatní obslužné rutiny pro stejný požadavek mohou být úspěšné.

* Chcete-li zaručit selhání, i v `context.Fail`případě, že ostatní obslužné rutiny požadavku úspěšné, volání .

Pokud volání `context.Succeed` obslužné rutiny nebo `context.Fail`, všechny ostatní obslužné rutiny jsou stále volány. To umožňuje požadavky na vedlejší účinky, jako je například protokolování, ke kterému dochází i v případě, že jiná obslužná rutina úspěšně ověřena nebo se nezdařilpožadavek. Pokud je `false`nastavena na , [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) vlastnost (k dispozici v ASP.NET Core 1.1 a novější) zkratuje provádění obslužné rutiny při `context.Fail` volání. `InvokeHandlersAfterFailure`výchozí hodnota `true`je na , v takovém případě jsou volány všechny obslužné rutiny.

> [!NOTE]
> Obslužné rutiny autorizace se nazývají i v případě, že se ověření nezdaří.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Proč bych měl chtít více obslužných rutin pro požadavek?

V případech, kdy chcete, aby hodnocení bylo na základě **or,** implementovat více obslužné rutiny pro jeden požadavek. Například Microsoft má dveře, které se otevírají pouze s klíčovými kartami. Pokud si necháte svou kartu doma, recepční vytiskne dočasnou nálepku a otevře vám dveře. V tomto scénáři byste měli jeden požadavek *BuildingEntry*, ale více obslužné rutiny, každý z nich zkoumá jeden požadavek.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Ujistěte se, že jsou [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)obě obslužné rutiny . Pokud buď obslužná rutina úspěšné při vyhodnotí zásady `BuildingEntryRequirement`, vyhodnocení zásady úspěšné.

## <a name="using-a-func-to-fulfill-a-policy"></a>Použití func ke splnění politiky

Mohou nastat situace, ve kterých je plnění zásady jednoduché vyjádřit v kódu. Je možné zadat při `Func<AuthorizationHandlerContext, bool>` konfiguraci zásad s `RequireAssertion` tvůrcem zásad.

Předchozí `BadgeEntryHandler` může být například přepsána takto:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Přístup k kontextu požadavku MVC v obslužných rutinách

Metoda, `HandleRequirementAsync` kterou implementujete v obslužné rutině autorizace, má dva parametry: a `AuthorizationHandlerContext` `TRequirement` zpracováváte. Architektury jako MVC nebo SignalR jsou zdarma `Resource` přidat libovolný `AuthorizationHandlerContext` objekt do vlastnosti na předat další informace.

Při použití směrování koncových bodů je autorizace obvykle zpracována middlewarem autorizace. V tomto případě `Resource` je vlastnost <xref:Microsoft.AspNetCore.Http.Endpoint>instancí třídy . Koncový bod lze použít k prominování základní prostředek, do kterého směrování. Příklad:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

S tradiční směrování, nebo když autorizace se stane jako součást `Resource` filtru <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> autorizace MVC, hodnota je instance. Tato vlastnost poskytuje `HttpContext` `RouteData`přístup k , a vše ostatní poskytované MVC a Razor stránky.

Použití vlastnosti `Resource` je specifické pro architekturu. Použití informací `Resource` ve vlastnosti omezuje zásady autorizace na konkrétní architektury. Měli byste `Resource` přetypovat `is` vlastnost pomocí klíčového slova a pak potvrďte, že `InvalidCastException` obsazení se podařilo zajistit, že váš kód není selhání s při spuštění na jiných rozhraních:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
