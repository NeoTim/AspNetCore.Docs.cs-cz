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
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="22767-103">Úvod k autorizaci v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="22767-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="22767-104">Autorizace se týká procesu, který určuje, co může uživatel dělat.</span><span class="sxs-lookup"><span data-stu-id="22767-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="22767-105">Uživatel s právy pro správu může například vytvořit knihovnu dokumentů, přidat dokumenty, upravit dokumenty a odstranit je.</span><span class="sxs-lookup"><span data-stu-id="22767-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="22767-106">Uživatel bez oprávnění správce pracující s knihovnou má oprávnění ke čtení dokumentů.</span><span class="sxs-lookup"><span data-stu-id="22767-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="22767-107">Autorizace je kolmá a nezávislá na ověřování.</span><span class="sxs-lookup"><span data-stu-id="22767-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="22767-108">Autorizace ale vyžaduje mechanismus ověřování.</span><span class="sxs-lookup"><span data-stu-id="22767-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="22767-109">Ověřování je proces, který zjišťuje uživatele.</span><span class="sxs-lookup"><span data-stu-id="22767-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="22767-110">Ověřování může pro aktuálního uživatele vytvořit jednu nebo více identit.</span><span class="sxs-lookup"><span data-stu-id="22767-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="22767-111">Další informace o ověřování v ASP.NET Core najdete v tématu <xref:security/authentication/index> .</span><span class="sxs-lookup"><span data-stu-id="22767-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="22767-112">Typy autorizace</span><span class="sxs-lookup"><span data-stu-id="22767-112">Authorization types</span></span>

<span data-ttu-id="22767-113">ASP.NET Core Authorization poskytuje jednoduchou, deklarativní [roli](xref:security/authorization/roles) a bohatý model [založený na zásadách](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="22767-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="22767-114">Autorizace je vyjádřena v požadavcích a obslužné rutiny vyhodnocují deklarace identity uživatele proti požadavkům.</span><span class="sxs-lookup"><span data-stu-id="22767-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="22767-115">Imperativní kontroly můžou být založené na jednoduchých zásadách nebo zásadách, které vyhodnocují identitu uživatele i vlastnosti prostředku, ke kterému se uživatel pokouší získat přístup.</span><span class="sxs-lookup"><span data-stu-id="22767-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="22767-116">Obory názvů</span><span class="sxs-lookup"><span data-stu-id="22767-116">Namespaces</span></span>

<span data-ttu-id="22767-117">Autorizační komponenty, včetně `AuthorizeAttribute` atributů a `AllowAnonymousAttribute` , se nacházejí v `Microsoft.AspNetCore.Authorization` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="22767-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="22767-118">Projděte si dokumentaci k [jednoduché autorizaci](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="22767-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
