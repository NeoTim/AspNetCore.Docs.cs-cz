---
title: Ověřování na základě zásad v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet a používat obslužné rutiny zásad autorizace pro vynucování autorizačních požadavků v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: security/authorization/policies
ms.openlocfilehash: 60625944d4ba31da6b98bdf947991088dc75ed87
ms.sourcegitcommit: 7001657c00358b082734ba4273693b9b3ed35d2a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68669974"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Ověřování na základě zásad v ASP.NET Core

V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené](xref:security/authorization/claims) na deklaracích se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady. Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu. Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.

Zásady autorizace se skládají z jednoho nebo více požadavků. Je zaregistrován jako součást konfigurace autorizační služby v `Startup.ConfigureServices` metodě:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

V předchozím příkladu se vytvoří zásada "AtLeast21". Má jeden požadavek&mdash;na minimální stáří, které je zadáno jako parametr požadavku.

## <a name="iauthorizationservice"></a>Načetl služby IAuthorizationService 

Primární služba, která určuje, jestli je autorizace úspěšná <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>, je:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda autorizace je úspěšná.

Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> je zodpovědný za kontrolu, zda jsou splněny požadavky:
<!--The following code is a copy/paste from 
https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

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

<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Třída je to, co obslužná rutina používá k označení, zda byly splněny požadavky:

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

Použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo`[Authorize(Policy = "Something")]` k autorizaci.

## <a name="applying-policies-to-mvc-controllers"></a>Použití zásad u řadičů MVC

Pokud používáte Razor Pages, přečtěte si téma [použití zásad pro Razor Pages](#applying-policies-to-razor-pages) v tomto dokumentu.

Zásady se aplikují na řadiče pomocí `[Authorize]` atributu s názvem zásady. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Použití zásad pro Razor Pages

Zásady se aplikují na Razor Pages pomocí `[Authorize]` atributu s názvem zásady. Příklad:

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

Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers). Obslužná rutina může [dědit\<AuthorizationHandler TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), `TRequirement` kde je požadavek, který má být zpracován. Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.

### <a name="use-a-handler-for-one-requirement"></a>Použití obslužné rutiny pro jeden požadavek

<a name="security-authorization-handler-example"></a>

Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem. K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol. Pokud je k dispozici deklarace identity, počítá se věk uživatele. Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou. Po úspěšném `context.Succeed` ověření je vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.

### <a name="use-a-handler-for-multiple-requirements"></a>Použití obslužné rutiny pro více požadavků

Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Předchozí průchod kódu [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky, které nejsou označeny jako úspěšné. `ReadPermission` Pro přístup k požadovanému prostředku musí být uživatel buď vlastník, nebo sponzor. V případě `EditPermission` požadavku nebo `DeletePermission` musí být vlastníkem přístup k požadovanému prostředku.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registrace obslužné rutiny

Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace. Příklad:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Předchozí kód se registruje `MinimumAgeHandler` jako typ singleton vyvoláním. `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>Co by měl obslužná rutina vrátit?

Všimněte si, `Handle` že metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu. Jak se uvádí stav buď úspěch, nebo neúspěch?

* Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)`a předáním požadavku, který byl úspěšně ověřen.

* Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.

* Chcete-li zaručit selhání i v případě úspěchu dalších obslužných `context.Fail`rutin požadavků, zavolejte.

Pokud obslužná rutina `context.Succeed` volá `context.Fail`nebo, všechny ostatní obslužné rutiny jsou stále volány. To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek. Když je nastavena `false`na, vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy, kdy `context.Fail` je volána spuštění obslužných rutin. `InvokeHandlersAfterFailure``true`výchozí hodnota – v takovém případě jsou volány všechny obslužné rutiny.

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

Zajistěte, aby [](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)byly oba obslužné rutiny registrovány. Pokud obslužná rutina uspěje `BuildingEntryRequirement`, když zásada vyhodnotí, vyhodnocení zásad je úspěšné.

## <a name="using-a-func-to-fulfill-a-policy"></a>Splnění zásad pomocí funkce

Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu. Je možné dodat a `Func<AuthorizationHandlerContext, bool>` nakonfigurovat zásadu `RequireAssertion` pomocí Tvůrce zásad.

Například předchozí `BadgeEntryHandler` může být přepsána následujícím způsobem:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Přístup k kontextu požadavku MVC v obslužných rutinách

Metoda, kterou implementujete v obslužné rutině autorizace, má dva `AuthorizationHandlerContext` parametry: `TRequirement` a, kterou zpracováváte. `HandleRequirementAsync` Architektury, jako je MVC nebo Jabbr, jsou volné pro přidání libovolného objektu do `Resource` vlastnosti v, `AuthorizationHandlerContext` aby bylo možné předávat Další informace.

MVC například projde instanci [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) ve `Resource` vlastnosti. Tato vlastnost poskytuje přístup k `HttpContext`, `RouteData`a vše ostatní poskytované MVC a Razor Pages.

Použití `Resource` vlastnosti je specifické pro rozhraní. Použití informací v této `Resource` vlastnosti omezuje vaše zásady autorizace na konkrétní architektury. `Resource` Vlastnost byste měli přetypovat `is` pomocí klíčového slova a pak potvrdit, že přetypování bylo úspěšné, aby při spuštění v jiných rozhraních nedošlo `InvalidCastException` k chybě kódu.

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
