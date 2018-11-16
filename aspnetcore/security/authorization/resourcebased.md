---
title: Autorizace na základě prostředků v ASP.NET Core
author: scottaddie
description: Zjistěte, jak implementovat ověřování na základě prostředků v aplikaci ASP.NET Core atribut Authorize nestačí.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: 6a163caa26b277fbee6b9d61f8f1d16a60c75b03
ms.sourcegitcommit: d3392f688cfebc1f25616da7489664d69c6ee330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51818366"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>Autorizace na základě prostředků v ASP.NET Core

Strategii autorizace závisí na prostředek, ke kterému přistupujete. Vezměte v úvahu dokument, který se má vytvořit vlastnost. Pouze autor můžou aktualizovat dokument. V důsledku toho dokumentu musí načíst z úložiště dat, než dojde k vyhodnocení autorizace.

Před datové vazby a před spuštěním obslužná rutina stránky nebo akci, která načte dokument dojde k vyhodnocení atribut. Z těchto důvodů, deklarativní autorizace pomocí `[Authorize]` atribut nebude stačit. Místo toho můžete vyvolat vlastní autorizační metoda&mdash;styl říká *imperativní autorizace*.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([stažení](xref:index#how-to-download-a-sample)).

[Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací](xref:security/authorization/secure-data) obsahuje ukázkovou aplikaci, která používá ověřování založené na resource.

## <a name="use-imperative-authorization"></a>Použití imperativní autorizace

Autorizace je implementovaný jako [služby IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) služby a je registrován v kolekci služby v rámci `Startup` třídy. Služba je k dispozici prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection) obslužných rutin stránky nebo provádět akce.

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` má dva `AuthorizeAsync` přetížení metody: jedna přijímá prostředku a název zásady a druhý přijímá prostředku a seznam všech požadavků k vyhodnocení.

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

V následujícím příkladu je prostředek, který má být zabezpečeny načten do vlastní `Document` objektu. `AuthorizeAsync` Přetížení je vyvoláno pro zjištění, zda má aktuální uživatel může upravit zadaný dokument. Vlastní zásady autorizace "EditPolicy" je rozdělen do rozhodnutí. Zobrazit [vlastní autorizace na základě zásad](xref:security/authorization/policies) pro další informace o vytváření zásad autorizace.

> [!NOTE]
> Následující kód ukázky předpokládají bylo spuštěno ověřování a nastavení `User` vlastnost.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>Zápis obslužné rutiny založené na prostředcích

Zápis obslužné rutiny pro ověřování na základě prostředku se výrazně liší od [zápis obslužné rutiny jednoduché požadavky](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler). Vytvořit vlastní požadavek třídy a implementovat třídu obslužné rutiny požadavku. Další informace o vytvoření třídy požadavek, naleznete v tématu [požadavky](xref:security/authorization/policies#requirements).

Třída obslužné rutiny určuje požadavky a typ prostředku. Například obslužnou rutinu využitím `SameAuthorRequirement` a `Document` prostředků takto:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

V předchozím příkladu, představte si, že `SameAuthorRequirement` je zvláštní případ další obecného `SpecificAuthorRequirement` třídy. `SpecificAuthorRequirement` Obsahuje třídu (není vidět) `Name` vlastnost představující jméno autora. `Name` Vlastnost může být nastavena na aktuální uživatel.

Registrovat požadavku a obslužné rutiny v `Startup.ConfigureServices`:

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a>Provozní požadavky

Pokud jste už rozhodování na základě výsledků operace CRUD (vytváření, čtení, Update, Delete), použijte [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) pomocná třída. Tato třída umožňuje zapisovat jedna obslužná rutina namísto jednotlivých tříd pro každý typ operace. Pro použití je třeba zadejte názvy některé operace:

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

Obslužná rutina je implementován takto, pomocí `OperationAuthorizationRequirement` požadavek a `Document` prostředků:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

Předchozí obslužná rutina ověří pomocí prostředku, identitu uživatele a požadavek na operaci `Name` vlastnost.

Volání obslužné rutiny provozní prostředků, zadejte operaci při vyvolání `AuthorizeAsync` v obslužné rutině stránky nebo akce. Následující příklad určuje, zda chcete-li zobrazit poskytnutý dokument smí obsahovat ověřeného uživatele.

> [!NOTE]
> Následující kód ukázky předpokládají bylo spuštěno ověřování a nastavení `User` vlastnost.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

Pokud autorizace úspěšná, vrátí se stránka pro zobrazení dokumentu. Pokud autorizace selže, ale uživatel je ověřen, vrací `ForbidResult` informuje veškerý middleware ověřování, který autorizace se nezdařila. A `ChallengeResult` je vrácena při ověřování musí být provedeno. Pro klienty prohlížeče interaktivní může být vhodné přesměruje uživatele na přihlašovací stránku.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

Pokud autorizace úspěšná, vrátí se zobrazení pro dokument. Pokud se ověření nezdaří, vrátí `ChallengeResult` informuje veškerý middleware ověřování, autorizace se nezdařila a middleware může trvat odpovídající odpověď. Stavový kód 401 nebo 403 může vrací odpovídající odpověď. Pro klienty prohlížeče interaktivní může to znamenat přesměruje uživatele na přihlašovací stránku.

::: moniker-end
