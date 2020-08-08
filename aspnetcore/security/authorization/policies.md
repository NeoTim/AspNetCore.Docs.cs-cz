---
title: Ověřování na základě zásad v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet a používat obslužné rutiny zásad autorizace pro vynucování autorizačních požadavků v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
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
uid: security/authorization/policies
ms.openlocfilehash: 03d6e7fdc4ab4b5e4925508952bfd6c835d90486
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021273"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Ověřování na základě zásad v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady. Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu. Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.

Zásady autorizace se skládají z jednoho nebo více požadavků. Je zaregistrován jako součást konfigurace autorizační služby v `Startup.ConfigureServices` metodě:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

V předchozím příkladu se vytvoří zásada "AtLeast21". Má jeden požadavek na &mdash; Minimální stáří, které je zadáno jako parametr požadavku.

## <a name="iauthorizationservice"></a>Načetl služby IAuthorizationService 

Primární služba, která určuje, jestli je autorizace úspěšná, je <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda autorizace je úspěšná.

Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> je zodpovědný za kontrolu, zda jsou splněny požadavky:
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

<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>Třída je to, co obslužná rutina používá k označení, zda byly splněny požadavky:

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

Následující kód ukazuje typický `ConfigureServices` :

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

Použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]` k autorizaci.

## <a name="apply-policies-to-mvc-controllers"></a>Použití zásad u řadičů MVC

Pokud používáte Razor stránky, přečtěte si téma [použití zásad na Razor stránkách](#apply-policies-to-razor-pages) v tomto dokumentu.

Zásady se aplikují na řadiče pomocí `[Authorize]` atributu s názvem zásady. Například:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>Použít zásady na Razor stránky

Zásady se aplikují na Razor stránky pomocí `[Authorize]` atributu s názvem zásady. Například:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Zásady nelze ***použít*** na Razor úrovni obslužné rutiny stránky, musejí být aplikovány na stránku.

Zásady lze použít na Razor stránky pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Požadavky

Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele. V našich zásadách "AtLeast21" je požadavek jedním parametrem &mdash; Minimální stáří. Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky. Parametr minimálního stáří by mohl být implementován následujícím způsobem:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné. Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .

> [!NOTE]
> Požadavek nemusí mít data ani vlastnosti.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Obslužné rutiny autorizace

Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku. Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.

Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers). Obslužná rutina může [Zdědit \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je požadavek, který má být zpracován. Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.

### <a name="use-a-handler-for-one-requirement"></a>Použití obslužné rutiny pro jeden požadavek

<a name="security-authorization-handler-example"></a>

Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem. K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol. Pokud je k dispozici deklarace identity, počítá se věk uživatele. Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou. Po úspěšném ověření `context.Succeed` je vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.

### <a name="use-a-handler-for-multiple-requirements"></a>Použití obslužné rutiny pro více požadavků

Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Předchozí průchod kódu [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; vlastnost obsahující požadavky, které nejsou označeny jako úspěšné. Pro `ReadPermission` přístup k požadovanému prostředku musí být uživatel buď vlastník, nebo sponzor. V případě `EditPermission` `DeletePermission` požadavku nebo musí být vlastníkem přístup k požadovanému prostředku.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registrace obslužné rutiny

Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace. Například:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Předchozí kód se registruje `MinimumAgeHandler` jako typ singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>Co by měl obslužná rutina vrátit?

Všimněte si, že `Handle` metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu. Jak se uvádí stav buď úspěch, nebo neúspěch?

* Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)` a předáním požadavku, který byl úspěšně ověřen.

* Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.

* Chcete-li zaručit selhání i v případě úspěchu dalších obslužných rutin požadavků, zavolejte `context.Fail` .

Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail` , všechny ostatní obslužné rutiny jsou stále volány. To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek. Když je nastavena na `false` , vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy, kdy je volána spuštění obslužných rutin `context.Fail` . `InvokeHandlersAfterFailure`Výchozí hodnota – v takovém `true` případě jsou volány všechny obslužné rutiny.

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

Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Pokud obslužná rutina uspěje, když zásada vyhodnotí `BuildingEntryRequirement` , vyhodnocení zásad je úspěšné.

## <a name="use-a-func-to-fulfill-a-policy"></a>Použití funkce ke splnění zásad

Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu. Je možné dodat a `Func<AuthorizationHandlerContext, bool>` nakonfigurovat zásadu pomocí `RequireAssertion` Tvůrce zásad.

Například předchozí `BadgeEntryHandler` může být přepsána následujícím způsobem:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a>Přístup k kontextu požadavku MVC v obslužných rutinách

`HandleRequirementAsync`Metoda, kterou implementujete v obslužné rutině autorizace, má dva parametry: `AuthorizationHandlerContext` a, `TRequirement` kterou zpracováváte. Rozhraní, jako je MVC, nebo SignalR jsou volná pro přidání libovolného objektu do `Resource` vlastnosti v `AuthorizationHandlerContext` pro předání dalších informací.

Při použití směrování koncových bodů se autorizaci obvykle zpracovává pomocí middleware autorizace. V tomto případě `Resource` je vlastnost instancí třídy <xref:Microsoft.AspNetCore.Http.Endpoint> . Koncový bod se dá použít k testování základního prostředku, ke kterému se právě směrujete. Například:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

Koncový bod neposkytuje přístup k aktuálnímu `HttpContext` . Při použití směrování koncových bodů použijte `IHttpContextAcessor` pro přístup k `HttpContext` obslužné rutině autorizace. Další informace naleznete v tématu [použití vlastnosti HttpContext z vlastních komponent](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).

V případě tradičního směrování nebo když k autorizaci dojde jako součást autorizačního filtru MVC, hodnota `Resource` je <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance. Tato vlastnost poskytuje přístup k `HttpContext` , `RouteData` a vše ostatní poskytované MVC a Razor stránkami.

Použití `Resource` vlastnosti je specifické pro rozhraní. Použití informací v této `Resource` vlastnosti omezuje vaše zásady autorizace na konkrétní architektury. Vlastnost byste měli přetypovat `Resource` pomocí `is` klíčového slova a pak potvrdit, že přetypování bylo úspěšné, aby `InvalidCastException` při spuštění v jiných rozhraních nedošlo k chybě kódu.

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Globálně vyžadovat ověření všech uživatelů

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady. Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu. Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.

Zásady autorizace se skládají z jednoho nebo více požadavků. Je zaregistrován jako součást konfigurace autorizační služby v `Startup.ConfigureServices` metodě:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

V předchozím příkladu se vytvoří zásada "AtLeast21". Má jeden požadavek na &mdash; Minimální stáří, které je zadáno jako parametr požadavku.

## <a name="iauthorizationservice"></a>Načetl služby IAuthorizationService 

Primární služba, která určuje, jestli je autorizace úspěšná, je <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda autorizace je úspěšná.

Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> je zodpovědný za kontrolu, zda jsou splněny požadavky:
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

<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>Třída je to, co obslužná rutina používá k označení, zda byly splněny požadavky:

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

Následující kód ukazuje typický `ConfigureServices` :

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

Použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]` k autorizaci.

## <a name="apply-policies-to-mvc-controllers"></a>Použití zásad u řadičů MVC

Pokud používáte Razor stránky, přečtěte si téma [použití zásad na Razor stránkách](#apply-policies-to-razor-pages) v tomto dokumentu.

Zásady se aplikují na řadiče pomocí `[Authorize]` atributu s názvem zásady. Například:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>Použít zásady na Razor stránky

Zásady se aplikují na Razor stránky pomocí `[Authorize]` atributu s názvem zásady. Například:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Zásady se dají na stránkách použít taky Razor pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Požadavky

Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele. V našich zásadách "AtLeast21" je požadavek jedním parametrem &mdash; Minimální stáří. Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky. Parametr minimálního stáří by mohl být implementován následujícím způsobem:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné. Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .

> [!NOTE]
> Požadavek nemusí mít data ani vlastnosti.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Obslužné rutiny autorizace

Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku. Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.

Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers). Obslužná rutina může [Zdědit \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je požadavek, který má být zpracován. Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.

### <a name="use-a-handler-for-one-requirement"></a>Použití obslužné rutiny pro jeden požadavek

<a name="security-authorization-handler-example"></a>

Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem. K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol. Pokud je k dispozici deklarace identity, počítá se věk uživatele. Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou. Po úspěšném ověření `context.Succeed` je vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.

### <a name="use-a-handler-for-multiple-requirements"></a>Použití obslužné rutiny pro více požadavků

Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Předchozí průchod kódu [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; vlastnost obsahující požadavky, které nejsou označeny jako úspěšné. Pro `ReadPermission` přístup k požadovanému prostředku musí být uživatel buď vlastník, nebo sponzor. V případě `EditPermission` `DeletePermission` požadavku nebo musí být vlastníkem přístup k požadovanému prostředku.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registrace obslužné rutiny

Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace. Například:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Předchozí kód se registruje `MinimumAgeHandler` jako typ singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>Co by měl obslužná rutina vrátit?

Všimněte si, že `Handle` metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu. Jak se uvádí stav buď úspěch, nebo neúspěch?

* Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)` a předáním požadavku, který byl úspěšně ověřen.

* Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.

* Chcete-li zaručit selhání i v případě úspěchu dalších obslužných rutin požadavků, zavolejte `context.Fail` .

Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail` , všechny ostatní obslužné rutiny jsou stále volány. To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek. Když je nastavena na `false` , vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy, kdy je volána spuštění obslužných rutin `context.Fail` . `InvokeHandlersAfterFailure`Výchozí hodnota – v takovém `true` případě jsou volány všechny obslužné rutiny.

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

Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Pokud obslužná rutina uspěje, když zásada vyhodnotí `BuildingEntryRequirement` , vyhodnocení zásad je úspěšné.

## <a name="use-a-func-to-fulfill-a-policy"></a>Použití funkce ke splnění zásad

Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu. Je možné dodat a `Func<AuthorizationHandlerContext, bool>` nakonfigurovat zásadu pomocí `RequireAssertion` Tvůrce zásad.

Například předchozí `BadgeEntryHandler` může být přepsána následujícím způsobem:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a>Přístup k kontextu požadavku MVC v obslužných rutinách

`HandleRequirementAsync`Metoda, kterou implementujete v obslužné rutině autorizace, má dva parametry: `AuthorizationHandlerContext` a, `TRequirement` kterou zpracováváte. Rozhraní, jako je MVC, nebo SignalR jsou volná pro přidání libovolného objektu do `Resource` vlastnosti v `AuthorizationHandlerContext` pro předání dalších informací.

MVC například projde instanci [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) ve `Resource` Vlastnosti. Tato vlastnost poskytuje přístup k `HttpContext` , `RouteData` a vše ostatní poskytované MVC a Razor stránkami.

Použití `Resource` vlastnosti je specifické pro rozhraní. Použití informací v této `Resource` vlastnosti omezuje vaše zásady autorizace na konkrétní architektury. Vlastnost byste měli přetypovat `Resource` pomocí `is` klíčového slova a pak potvrdit, že přetypování bylo úspěšné, aby `InvalidCastException` při spuštění v jiných rozhraních nedošlo k chybě kódu.

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
