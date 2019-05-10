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
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="e2aa6-103">Úvod k ověřování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e2aa6-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="e2aa6-104">Autorizace určuje, k procesu, který určuje, co je možné provádět uživatel.</span><span class="sxs-lookup"><span data-stu-id="e2aa6-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="e2aa6-105">Například uživatel s oprávněním správce smí vytvořit knihovnu dokumentů, přidat dokumenty, úpravy dokumentů a je odstranit.</span><span class="sxs-lookup"><span data-stu-id="e2aa6-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="e2aa6-106">Uživatel bez oprávnění správce, práci s knihovnou je autorizovaná jenom tyto dokumenty číst.</span><span class="sxs-lookup"><span data-stu-id="e2aa6-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="e2aa6-107">Autorizace je ortogonální a nezávisle na ověřování.</span><span class="sxs-lookup"><span data-stu-id="e2aa6-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="e2aa6-108">Však vyžaduje ověřování ověřovacího mechanismu.</span><span class="sxs-lookup"><span data-stu-id="e2aa6-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="e2aa6-109">Ověřování je proces zjišťování, který je uživatel.</span><span class="sxs-lookup"><span data-stu-id="e2aa6-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="e2aa6-110">Ověřování můžou vytvořit jednu nebo více identit pro aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="e2aa6-110">Authentication may create one or more identities for the current user.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="e2aa6-111">Typy autorizace</span><span class="sxs-lookup"><span data-stu-id="e2aa6-111">Authorization types</span></span>

<span data-ttu-id="e2aa6-112">ASP.NET Core autorizaci poskytuje jednoduchý deklarativní [role](xref:security/authorization/roles) a bohaté [založené na zásadách](xref:security/authorization/policies) modelu.</span><span class="sxs-lookup"><span data-stu-id="e2aa6-112">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="e2aa6-113">Autorizace je vyjádřena v požadavcích a obslužné rutiny vyhodnotit deklarace identity uživatele podle požadavků.</span><span class="sxs-lookup"><span data-stu-id="e2aa6-113">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="e2aa6-114">Imperativní kontroly může být založen na jednoduché zásady nebo zásady, které vyhodnotí identitu uživatele a vlastnosti prostředků, které se uživatel pokouší o přístup.</span><span class="sxs-lookup"><span data-stu-id="e2aa6-114">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="e2aa6-115">Jmenné prostory</span><span class="sxs-lookup"><span data-stu-id="e2aa6-115">Namespaces</span></span>

<span data-ttu-id="e2aa6-116">Součásti ověření, včetně `AuthorizeAttribute` a `AllowAnonymousAttribute` atributy, se nacházejí v `Microsoft.AspNetCore.Authorization` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="e2aa6-116">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="e2aa6-117">Informace naleznete v dokumentaci na [jednoduchá autorizace](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="e2aa6-117">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
