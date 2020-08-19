---
title: Úvod k autorizaci v ASP.NET Core
author: rick-anderson
description: Seznamte se se základy autorizace a principem ověřování v aplikacích ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/introduction
ms.openlocfilehash: 7d7570ead1365588fd582d9bea364685da29a576
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634433"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Úvod k autorizaci v ASP.NET Core

<a name="security-authorization-introduction"></a>

Autorizace se týká procesu, který určuje, co může uživatel dělat. Uživatel s právy pro správu může například vytvořit knihovnu dokumentů, přidat dokumenty, upravit dokumenty a odstranit je. Uživatel bez oprávnění správce pracující s knihovnou má oprávnění ke čtení dokumentů.

Autorizace je kolmá a nezávislá na ověřování. Autorizace ale vyžaduje mechanismus ověřování. Ověřování je proces, který zjišťuje uživatele. Ověřování může pro aktuálního uživatele vytvořit jednu nebo více identit.

Další informace o ověřování v ASP.NET Core najdete v tématu <xref:security/authentication/index> .

## <a name="authorization-types"></a>Typy autorizace

ASP.NET Core Authorization poskytuje jednoduchou, deklarativní [roli](xref:security/authorization/roles) a bohatý model [založený na zásadách](xref:security/authorization/policies) . Autorizace je vyjádřena v požadavcích a obslužné rutiny vyhodnocují deklarace identity uživatele proti požadavkům. Imperativní kontroly můžou být založené na jednoduchých zásadách nebo zásadách, které vyhodnocují identitu uživatele i vlastnosti prostředku, ke kterému se uživatel pokouší získat přístup.

## <a name="namespaces"></a>Obory názvů

Autorizační komponenty, včetně `AuthorizeAttribute` atributů a `AllowAnonymousAttribute` , se nacházejí v `Microsoft.AspNetCore.Authorization` oboru názvů.

Projděte si dokumentaci k [jednoduché autorizaci](xref:security/authorization/simple).
