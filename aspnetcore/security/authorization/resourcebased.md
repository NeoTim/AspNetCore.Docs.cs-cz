---
title: Ověřování na základě prostředků v ASP.NET Core
author: scottaddie
description: Naučte se implementovat autorizaci na základě prostředků v ASP.NET Core aplikaci, když atribut autorizovat nestačí.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: 2be611c754583d996db7107f341b1be03cef73cf
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664799"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>Ověřování na základě prostředků v ASP.NET Core

Autorizační strategie závisí na prostředku, ke kterému se přistupoval. Vezměte v úvahu dokument, který má vlastnost Author. Dokument může aktualizovat pouze autor. V důsledku toho musí být dokument načten z úložiště dat ještě před tím, než může dojít k vyhodnocení autorizace.

K vyhodnocení atributu dojde před datovou vazbou a před spuštěním obslužné rutiny stránky nebo akce, která dokument načte. Z těchto důvodů nestačí deklarativní autorizace s atributem `[Authorize]`. Místo toho můžete vyvolat vlastní metodu autorizace&mdash;stylu, který se označuje jako *imperativní autorizace*.

::: moniker range=">= aspnetcore-3.0"
[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([Jak stáhnout](xref:index#how-to-download-a-sample)).
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([Jak stáhnout](xref:index#how-to-download-a-sample)).
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([Jak stáhnout](xref:index#how-to-download-a-sample)).
::: moniker-end

[Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací](xref:security/authorization/secure-data) obsahuje ukázkovou aplikaci, která používá autorizaci založenou na prostředku.

## <a name="use-imperative-authorization"></a>Použít imperativní autorizaci

Autorizace je implementována jako služba [načetl služby IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) a je registrována v kolekci služeb v rámci třídy `Startup`. Služba je zpřístupněna prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection) na obslužné rutiny stránky nebo akce.

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` má dvě přetížení metod `AuthorizeAsync`: jedno přijetí prostředku a název zásady a druhý přijetí prostředku a seznam požadavků, které se mají vyhodnotit.

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

V následujícím příkladu je prostředek, který má být zabezpečen, načten do vlastního objektu `Document`. Je vyvolána `AuthorizeAsync` přetížení, které určuje, zda má aktuální uživatel povolený úpravu zadaného dokumentu. Vlastní zásady autorizace "EditPolicy" se připravují na rozhodnutí. Další informace o vytváření zásad autorizace najdete v tématu věnovaném [vlastním autorizaci na základě zásad](xref:security/authorization/policies) .

> [!NOTE]
> Následující ukázky kódu předpokládají, že ověřování běželo a nastavilo vlastnost `User`.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>Zápis obslužné rutiny založené na prostředku

Zápis obslužné rutiny pro autorizaci založenou na prostředku není mnohem jiný než [zápis obslužné rutiny s jednoduchými požadavky](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler). Vytvořte vlastní třídu požadavků a implementujte třídu obslužné rutiny požadavků. Další informace o vytváření třídy požadavku najdete v tématu [požadavky](xref:security/authorization/policies#requirements).

Třída obslužné rutiny určuje jak požadavek, tak i typ prostředku. Například obslužná rutina, která využívá `SameAuthorRequirement` a prostředek `Document`, je následující:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

V předchozím příkladu představte, že `SameAuthorRequirement` je zvláštní případ obecnější `SpecificAuthorRequirement` třídy. Třída `SpecificAuthorRequirement` (není zobrazená) obsahuje vlastnost `Name` představující jméno autora. Vlastnost `Name` lze nastavit na aktuálního uživatele.

Zaregistrujte požadavek a obslužnou rutinu v `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a>Provozní požadavky

Pokud provádíte rozhodnutí na základě výsledků operací CRUD (vytváření, čtení, aktualizace, odstranění), použijte pomocnou třídu [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) . Tato třída umožňuje napsat jednu obslužnou rutinu místo jednotlivé třídy pro každý typ operace. Pokud ho chcete použít, zadejte několik názvů operací:

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

Obslužná rutina je implementována následujícím způsobem, a to pomocí `OperationAuthorizationRequirement` požadavku a prostředku `Document`:

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

Předchozí obslužná rutina ověří operaci pomocí prostředku, identity uživatele a vlastnosti `Name` požadavku.

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a>Vyvolávat výzvu a zakazují je pomocí obslužné rutiny provozního prostředku

V této části se dozvíte, jak jsou zpracovávány výsledky akce výzva a zakázat, a jak se liší výzvy a zakazující se.

Pro volání obslužné rutiny provozního prostředku určete operaci při vyvolání `AuthorizeAsync` v obslužné rutině nebo akci stránky. Následující příklad určuje, zda má ověřený uživatel oprávnění zobrazit zadaný dokument.

> [!NOTE]
> Následující ukázky kódu předpokládají, že ověřování běželo a nastavilo vlastnost `User`.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

Pokud se autorizace zdaří, vrátí se stránka pro zobrazení dokumentu. Pokud se autorizace nepovede, ale uživatel bude ověřený, vrátí `ForbidResult` informuje jakýkoliv middleware pro ověřování, který autorizace nezdařila. Pokud je nutné provést ověření, je vrácena `ChallengeResult`. Pro klienty interaktivního prohlížeče může být vhodné přesměrovat uživatele na přihlašovací stránku.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

Pokud je ověření úspěšné, vrátí se zobrazení dokumentu. Pokud se autorizace nezdařila, vrácení `ChallengeResult` informuje jakýkoliv middleware ověřování, že Autorizace selhala, a middleware může přijmout příslušnou odpověď. Vhodná odpověď může vrátit stavový kód 401 nebo 403. U interaktivních klientů prohlížeče může to znamenat přesměrování uživatele na přihlašovací stránku.

::: moniker-end
