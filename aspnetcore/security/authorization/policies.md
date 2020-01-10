---
title: Ověřování na základě zásad v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet a používat obslužné rutiny zásad autorizace pro vynucování autorizačních požadavků v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: security/authorization/policies
ms.openlocfilehash: eeb5ddd63ef8177325b35e5a666aa5e9ab047057
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828955"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Ověřování na základě zásad v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady. Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu. Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.

Zásady autorizace se skládají z jednoho nebo více požadavků. Je zaregistrován jako součást konfigurace autorizační služby v metodě `Startup.ConfigureServices`:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

V předchozím příkladu se vytvoří zásada "AtLeast21". Má jeden požadavek&mdash;minimální stáří, které je zadáno jako parametr pro požadavek.

## <a name="iauthorizationservice"></a>Načetl služby IAuthorizationService 

Primární služba, která určuje, jestli je autorizace úspěšná, <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> je služba značek bez metod a mechanismus pro sledování, zda byla autorizace úspěšná.

Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> zodpovídá za kontrolu, jestli jsou splněny požadavky:
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

Následující kód zobrazuje zjednodušenou (a poznámkovou poznámku) výchozí implementaci autorizační služby:

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

Následující kód ukazuje typický `ConfigureServices`:

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

Pro autorizaci použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]`.

## <a name="applying-policies-to-mvc-controllers"></a>Použití zásad u řadičů MVC

Pokud používáte Razor Pages, přečtěte si téma [použití zásad pro Razor Pages](#applying-policies-to-razor-pages) v tomto dokumentu.

Zásady se aplikují na řadiče pomocí atributu `[Authorize]` s názvem zásady. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Použití zásad pro Razor Pages

Zásady se aplikují na Razor Pages pomocí atributu `[Authorize]` s názvem zásady. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Zásady je možné použít také pro Razor Pages pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Požadavky

Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele. V našich zásadách "AtLeast21" je požadavek jedním parametrem&mdash;minimální stáří. Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky. Parametr minimálního stáří by mohl být implementován následujícím způsobem:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné. Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .

> [!NOTE]
> Požadavek nemusí mít data ani vlastnosti.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Obslužné rutiny autorizace

Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku. Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.

Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers). Obslužná rutina může dědit [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je požadavek, který má být zpracován. Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.

### <a name="use-a-handler-for-one-requirement"></a>Použití obslužné rutiny pro jeden požadavek

<a name="security-authorization-handler-example"></a>

Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem. K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol. Pokud je k dispozici deklarace identity, počítá se věk uživatele. Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou. Po úspěšném ověření je `context.Succeed` vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.

### <a name="use-a-handler-for-multiple-requirements"></a>Použití obslužné rutiny pro více požadavků

Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Předchozí kód projde [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky, které nejsou označeny jako úspěšné. Pro `ReadPermission` požadavek musí být uživatel buď vlastníkem, nebo sponzorem pro přístup k požadovanému prostředku. V případě `EditPermission` nebo `DeletePermission` požadavku musí být vlastníkem pro přístup k požadovanému prostředku.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registrace obslužné rutiny

Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace. Příklad:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Předchozí kód registruje `MinimumAgeHandler` jako typ singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>Co by měl obslužná rutina vrátit?

Všimněte si, že metoda `Handle` v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu. Jak se uvádí stav buď úspěch, nebo neúspěch?

* Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)`a předáním požadavku, který byl úspěšně ověřen.

* Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.

* Chcete-li zaručit selhání i v případě úspěchu dalších obslužných rutin požadavků, zavolejte `context.Fail`.

Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail`, všechny ostatní obslužné rutiny jsou stále volány. To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek. Pokud je nastavena na `false`, vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy spuštění obslužných rutin při volání `context.Fail`. Výchozí hodnota `InvokeHandlersAfterFailure` `true`. v takovém případě jsou volány všechny obslužné rutiny.

> [!NOTE]
> Obslužné rutiny autorizace jsou volány i v případě, že ověřování selhává.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Proč bych chtěl více obslužných rutin pro požadavek?

V případech, kdy chcete, aby vyhodnocení bylo na **nebo** bázi, implementujte více obslužných rutin pro jeden požadavek. Například Microsoft má dveře, které se otevírají jenom s kartami Key. Pokud ponecháte kartu klíče na domovské stránce, recepční vytiskne dočasný štítek a otevře dvířka za vás. V tomto scénáři byste měli jeden požadavek, *BuildingEntry*, ale několik obslužných rutin, každý z nich prozkoumá jeden požadavek.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Pokud je obslužná rutina úspěšná, když zásada vyhodnotí `BuildingEntryRequirement`, vyhodnocení zásad je úspěšné.

## <a name="using-a-func-to-fulfill-a-policy"></a>Splnění zásad pomocí funkce

Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu. Při konfiguraci zásady pomocí nástroje `RequireAssertion` Builder zásad je možné uvést `Func<AuthorizationHandlerContext, bool>`.

Předchozí `BadgeEntryHandler` například můžete přepsat následujícím způsobem:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Přístup k kontextu požadavku MVC v obslužných rutinách

Metoda `HandleRequirementAsync`, kterou implementujete v obslužné rutině autorizace, má dva parametry: `AuthorizationHandlerContext` a `TRequirement`, které zpracováváte. Rozhraní, jako je MVC nebo Jabbr, jsou zdarma, pokud chcete do vlastnosti `Resource` v `AuthorizationHandlerContext` přidat libovolný objekt pro předání dalších informací.

MVC například projde instanci [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) ve vlastnosti `Resource`. Tato vlastnost poskytuje přístup k `HttpContext`, `RouteData`a všechno ostatní poskytované MVC a Razor Pages.

Použití vlastnosti `Resource` je specifické pro rozhraní. Použití informací z vlastnosti `Resource` omezí vaše zásady autorizace na konkrétní architektury. Vlastnost `Resource` byste měli přetypovat pomocí klíčového slova `is` a pak potvrdit, že přetypování bylo úspěšné, aby při spuštění v jiných rozhraních nedošlo k chybě kódu `InvalidCastException`.

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

V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady. Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu. Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.

Zásady autorizace se skládají z jednoho nebo více požadavků. Je zaregistrován jako součást konfigurace autorizační služby v metodě `Startup.ConfigureServices`:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

V předchozím příkladu se vytvoří zásada "AtLeast21". Má jeden požadavek&mdash;minimální stáří, které je zadáno jako parametr pro požadavek.

## <a name="iauthorizationservice"></a>Načetl služby IAuthorizationService 

Primární služba, která určuje, jestli je autorizace úspěšná, <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> je služba značek bez metod a mechanismus pro sledování, zda byla autorizace úspěšná.

Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> zodpovídá za kontrolu, jestli jsou splněny požadavky:
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

Následující kód zobrazuje zjednodušenou (a poznámkovou poznámku) výchozí implementaci autorizační služby:

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

Následující kód ukazuje typický `ConfigureServices`:

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

Pro autorizaci použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]`.

## <a name="applying-policies-to-mvc-controllers"></a>Použití zásad u řadičů MVC

Pokud používáte Razor Pages, přečtěte si téma [použití zásad pro Razor Pages](#applying-policies-to-razor-pages) v tomto dokumentu.

Zásady se aplikují na řadiče pomocí atributu `[Authorize]` s názvem zásady. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Použití zásad pro Razor Pages

Zásady se aplikují na Razor Pages pomocí atributu `[Authorize]` s názvem zásady. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Zásady je možné použít také pro Razor Pages pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Požadavky

Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele. V našich zásadách "AtLeast21" je požadavek jedním parametrem&mdash;minimální stáří. Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky. Parametr minimálního stáří by mohl být implementován následujícím způsobem:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné. Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .

> [!NOTE]
> Požadavek nemusí mít data ani vlastnosti.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Obslužné rutiny autorizace

Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku. Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.

Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers). Obslužná rutina může dědit [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je požadavek, který má být zpracován. Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.

### <a name="use-a-handler-for-one-requirement"></a>Použití obslužné rutiny pro jeden požadavek

<a name="security-authorization-handler-example"></a>

Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem. K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol. Pokud je k dispozici deklarace identity, počítá se věk uživatele. Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou. Po úspěšném ověření je `context.Succeed` vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.

### <a name="use-a-handler-for-multiple-requirements"></a>Použití obslužné rutiny pro více požadavků

Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Předchozí kód projde [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky, které nejsou označeny jako úspěšné. Pro `ReadPermission` požadavek musí být uživatel buď vlastníkem, nebo sponzorem pro přístup k požadovanému prostředku. V případě `EditPermission` nebo `DeletePermission` požadavku musí být vlastníkem pro přístup k požadovanému prostředku.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registrace obslužné rutiny

Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Předchozí kód registruje `MinimumAgeHandler` jako typ singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>Co by měl obslužná rutina vrátit?

Všimněte si, že metoda `Handle` v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu. Jak se uvádí stav buď úspěch, nebo neúspěch?

* Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)`a předáním požadavku, který byl úspěšně ověřen.

* Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.

* Chcete-li zaručit selhání i v případě úspěchu dalších obslužných rutin požadavků, zavolejte `context.Fail`.

Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail`, všechny ostatní obslužné rutiny jsou stále volány. To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek. Pokud je nastavena na `false`, vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy spuštění obslužných rutin při volání `context.Fail`. Výchozí hodnota `InvokeHandlersAfterFailure` `true`. v takovém případě jsou volány všechny obslužné rutiny.

> [!NOTE]
> Obslužné rutiny autorizace jsou volány i v případě, že ověřování selhává.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Proč bych chtěl více obslužných rutin pro požadavek?

V případech, kdy chcete, aby vyhodnocení bylo na **nebo** bázi, implementujte více obslužných rutin pro jeden požadavek. Například Microsoft má dveře, které se otevírají jenom s kartami Key. Pokud ponecháte kartu klíče na domovské stránce, recepční vytiskne dočasný štítek a otevře dvířka za vás. V tomto scénáři byste měli jeden požadavek, *BuildingEntry*, ale několik obslužných rutin, každý z nich prozkoumá jeden požadavek.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Pokud je obslužná rutina úspěšná, když zásada vyhodnotí `BuildingEntryRequirement`, vyhodnocení zásad je úspěšné.

## <a name="using-a-func-to-fulfill-a-policy"></a>Splnění zásad pomocí funkce

Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu. Při konfiguraci zásady pomocí nástroje `RequireAssertion` Builder zásad je možné uvést `Func<AuthorizationHandlerContext, bool>`.

Předchozí `BadgeEntryHandler` například můžete přepsat následujícím způsobem:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Přístup k kontextu požadavku MVC v obslužných rutinách

Metoda `HandleRequirementAsync`, kterou implementujete v obslužné rutině autorizace, má dva parametry: `AuthorizationHandlerContext` a `TRequirement`, které zpracováváte. Rozhraní, jako je MVC nebo Jabbr, jsou zdarma, pokud chcete do vlastnosti `Resource` v `AuthorizationHandlerContext` přidat libovolný objekt pro předání dalších informací.

MVC například projde instanci [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) ve vlastnosti `Resource`. Tato vlastnost poskytuje přístup k `HttpContext`, `RouteData`a všechno ostatní poskytované MVC a Razor Pages.

Použití vlastnosti `Resource` je specifické pro rozhraní. Použití informací z vlastnosti `Resource` omezí vaše zásady autorizace na konkrétní architektury. Vlastnost `Resource` byste měli přetypovat pomocí klíčového slova `is` a pak potvrdit, že přetypování bylo úspěšné, aby při spuštění v jiných rozhraních nedošlo k chybě kódu `InvalidCastException`.

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end