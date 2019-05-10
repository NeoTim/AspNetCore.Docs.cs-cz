---
title: Úvod k ověřování v ASP.NET Core
author: rick-anderson
description: Přečtěte si základní informace o ověřování a jak funguje autorizaci v aplikacích ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: 5465eb7875ebecd77b628376ef886db0ddd05025
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902844"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Úvod k ověřování v ASP.NET Core

<a name="security-authorization-introduction"></a>

Autorizace určuje, k procesu, který určuje, co je možné provádět uživatel. Například uživatel s oprávněním správce smí vytvořit knihovnu dokumentů, přidat dokumenty, úpravy dokumentů a je odstranit. Uživatel bez oprávnění správce, práci s knihovnou je autorizovaná jenom tyto dokumenty číst.

Autorizace je ortogonální a nezávisle na ověřování. Však vyžaduje ověřování ověřovacího mechanismu. Ověřování je proces zjišťování, který je uživatel. Ověřování můžou vytvořit jednu nebo více identit pro aktuálního uživatele.

## <a name="authorization-types"></a>Typy autorizace

ASP.NET Core autorizaci poskytuje jednoduchý deklarativní [role](xref:security/authorization/roles) a bohaté [založené na zásadách](xref:security/authorization/policies) modelu. Autorizace je vyjádřena v požadavcích a obslužné rutiny vyhodnotit deklarace identity uživatele podle požadavků. Imperativní kontroly může být založen na jednoduché zásady nebo zásady, které vyhodnotí identitu uživatele a vlastnosti prostředků, které se uživatel pokouší o přístup.

## <a name="namespaces"></a>Jmenné prostory

Součásti ověření, včetně `AuthorizeAttribute` a `AllowAnonymousAttribute` atributy, se nacházejí v `Microsoft.AspNetCore.Authorization` oboru názvů.

Informace naleznete v dokumentaci na [jednoduchá autorizace](xref:security/authorization/simple).
