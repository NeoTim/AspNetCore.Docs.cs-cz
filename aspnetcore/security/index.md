---
title: Přehled zabezpečení ASP.NET Core
author: tdykstra
description: Další informace o ověřování, autorizaci a zabezpečení základy v ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: security/index
ms.openlocfilehash: 933501411169d89c4b24edda743c47591aa7a87a
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098958"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="afbfc-103">Přehled zabezpečení ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="afbfc-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="afbfc-104">ASP.NET Core umožňuje vývojářům snadno konfigurovat a spravovat zabezpečení pro své aplikace.</span><span class="sxs-lookup"><span data-stu-id="afbfc-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="afbfc-105">ASP.NET Core obsahuje funkce pro správu ověřování, autorizaci, ochranu dat, vynucení protokolu HTTPS, tajných kódů aplikace, ochrana proti padělání požadavků ochrany a správy CORS.</span><span class="sxs-lookup"><span data-stu-id="afbfc-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="afbfc-106">Tyto funkce zabezpečení umožňují vytvářet robustní ještě zabezpečení aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="afbfc-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="afbfc-107">Funkce zabezpečení ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="afbfc-107">ASP.NET Core security features</span></span>

<span data-ttu-id="afbfc-108">ASP.NET Core nabízí mnoho nástrojů a knihoven k zabezpečení vašich aplikací, včetně předdefinovaných poskytovatelů identit ale můžete použít 3. stran identit služeb jako je Facebook, Twitter a LinkedIn.</span><span class="sxs-lookup"><span data-stu-id="afbfc-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in Identity providers but you can use 3rd party identity services such as Facebook, Twitter, or LinkedIn.</span></span> <span data-ttu-id="afbfc-109">ASP.NET Core můžete snadno spravovat tajné kódy aplikace, které představují způsob, jak uložit a použít důvěrné informace, aniž byste museli ho zpřístupnit v kódu.</span><span class="sxs-lookup"><span data-stu-id="afbfc-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="afbfc-110">Ověřování vs. Autorizace</span><span class="sxs-lookup"><span data-stu-id="afbfc-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="afbfc-111">Ověřování je proces, ve kterém uživatel zadá přihlašovací údaje, které jsou pak porovnána s uloženými v operačním systému, databáze, aplikace nebo prostředku.</span><span class="sxs-lookup"><span data-stu-id="afbfc-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="afbfc-112">Pokud se shodují, úspěšně ověřit uživatele a pak můžete provádět akce, které mají autorizaci pro, během autorizace.</span><span class="sxs-lookup"><span data-stu-id="afbfc-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="afbfc-113">Povolení se vztahuje k procesu, která určuje, co uživatel může dělat.</span><span class="sxs-lookup"><span data-stu-id="afbfc-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="afbfc-114">Dalším způsobem, jak si můžete představit ověřování je vzít v úvahu jako způsob, jak při autorizaci akce, které může uživatel provádět, do které objekty do tohoto místa (server, databáze nebo aplikace) zadejte mezeru, například server, databáze, aplikace nebo prostředku.</span><span class="sxs-lookup"><span data-stu-id="afbfc-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="afbfc-115">Běžné chyby v softwaru</span><span class="sxs-lookup"><span data-stu-id="afbfc-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="afbfc-116">ASP.NET Core a EF obsahují funkce, které vám pomůžou zabezpečit vaše aplikace a zabránit porušení zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="afbfc-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="afbfc-117">Následující seznam odkazů přejdete k dokumentaci s podrobnostmi o techniky, aby nejběžnějších ohrožení zabezpečení ve službě web apps:</span><span class="sxs-lookup"><span data-stu-id="afbfc-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="afbfc-118">Útoky skriptování napříč weby</span><span class="sxs-lookup"><span data-stu-id="afbfc-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="afbfc-119">Útoky prostřednictvím injektáže SQL</span><span class="sxs-lookup"><span data-stu-id="afbfc-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="afbfc-120">Proti padělání požadavků mezi weby (CSRF)</span><span class="sxs-lookup"><span data-stu-id="afbfc-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="afbfc-121">Útoky na otevřeném přesměrování</span><span class="sxs-lookup"><span data-stu-id="afbfc-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="afbfc-122">Existují další chyby zabezpečení, které byste měli vědět.</span><span class="sxs-lookup"><span data-stu-id="afbfc-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="afbfc-123">Další informace najdete v tématu v dalších článcích v **zabezpečení a identita** část obsahu.</span><span class="sxs-lookup"><span data-stu-id="afbfc-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
