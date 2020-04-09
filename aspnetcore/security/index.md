---
title: Přehled základních ASP.NET zabezpečení
author: rick-anderson
description: Informace o základech ověřování, autorizace a zabezpečení najdete v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/index
ms.openlocfilehash: 0f8e96fb7d5246e746b95f8907745f849de60e24
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656035"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="ffea6-103">Přehled základních ASP.NET zabezpečení</span><span class="sxs-lookup"><span data-stu-id="ffea6-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="ffea6-104">ASP.NET Core umožňuje vývojářům snadno konfigurovat a spravovat zabezpečení svých aplikací.</span><span class="sxs-lookup"><span data-stu-id="ffea6-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="ffea6-105">ASP.NET Core obsahuje funkce pro správu ověřování, autorizace, ochrany dat, vynucení https, tajných kódů aplikací, ochrany proti padělání a správy CORS.</span><span class="sxs-lookup"><span data-stu-id="ffea6-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="ffea6-106">Tyto funkce zabezpečení umožňují vytvářet robustní a přesto bezpečné ASP.NET základní aplikace.</span><span class="sxs-lookup"><span data-stu-id="ffea6-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="ffea6-107">ASP.NET Základní funkce zabezpečení</span><span class="sxs-lookup"><span data-stu-id="ffea6-107">ASP.NET Core security features</span></span>

<span data-ttu-id="ffea6-108">ASP.NET Core poskytuje mnoho nástrojů a knihoven pro zabezpečení vašich aplikací, včetně integrovaných poskytovatelů identit, ale můžete použít služby identity třetích stran, jako je Facebook, Twitter nebo LinkedIn.</span><span class="sxs-lookup"><span data-stu-id="ffea6-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in Identity providers but you can use 3rd party identity services such as Facebook, Twitter, or LinkedIn.</span></span> <span data-ttu-id="ffea6-109">Pomocí ASP.NET Core můžete snadno spravovat tajné kódy aplikací, což je způsob ukládání a používání důvěrných informací, aniž byste je museli vystavit v kódu.</span><span class="sxs-lookup"><span data-stu-id="ffea6-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="ffea6-110">Ověřování vs. Autorizace</span><span class="sxs-lookup"><span data-stu-id="ffea6-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="ffea6-111">Ověřování je proces, ve kterém uživatel poskytuje pověření, která jsou pak porovnány s těmi, které jsou uloženy v operačním systému, databázi, aplikaci nebo prostředku.</span><span class="sxs-lookup"><span data-stu-id="ffea6-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="ffea6-112">Pokud se shodují, uživatelé se úspěšně ověří a pak mohou během procesu autorizace provádět akce, pro které jsou oprávněni.</span><span class="sxs-lookup"><span data-stu-id="ffea6-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="ffea6-113">Autorizace odkazuje na proces, který určuje, co má uživatel povoleno.</span><span class="sxs-lookup"><span data-stu-id="ffea6-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="ffea6-114">Dalším způsobem, jak si ověření myslet, je považovat ho za způsob, jak zadat mezeru, například server, databázi, aplikaci nebo prostředek, zatímco autorizace je, které akce může uživatel provádět, které objekty uvnitř tohoto prostoru (server, databáze nebo aplikace).</span><span class="sxs-lookup"><span data-stu-id="ffea6-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="ffea6-115">Běžné chyby zabezpečení softwaru</span><span class="sxs-lookup"><span data-stu-id="ffea6-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="ffea6-116">ASP.NET Core a EF obsahují funkce, které vám pomohou zabezpečit vaše aplikace a zabránit narušení zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="ffea6-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="ffea6-117">Následující seznam odkazů vás přenese na postupy dokumentace s podrobnostmi, abyste se vyhnuli nejčastějším chybám zabezpečení ve webových aplikacích:</span><span class="sxs-lookup"><span data-stu-id="ffea6-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="ffea6-118">Útoky skriptování mezi weby</span><span class="sxs-lookup"><span data-stu-id="ffea6-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="ffea6-119">Útoky prostřednictvím injektáže SQL</span><span class="sxs-lookup"><span data-stu-id="ffea6-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="ffea6-120">Padělání žádostí napříč místy (CSRF)</span><span class="sxs-lookup"><span data-stu-id="ffea6-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="ffea6-121">Otevřené útoky přesměrování</span><span class="sxs-lookup"><span data-stu-id="ffea6-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="ffea6-122">Existuje více chyb zabezpečení, které byste měli vědět.</span><span class="sxs-lookup"><span data-stu-id="ffea6-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="ffea6-123">Další informace naleznete v dalších článcích v části **Zabezpečení a identita** v obsahu.</span><span class="sxs-lookup"><span data-stu-id="ffea6-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
