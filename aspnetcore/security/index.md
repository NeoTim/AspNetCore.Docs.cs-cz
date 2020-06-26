---
title: Přehled zabezpečení ASP.NET Core
author: rick-anderson
description: Seznamte se se základy ověřování, autorizace a zabezpečení v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/index
ms.openlocfilehash: 2da3585df16cbec4b87db32cee1bb9138e6aeb16
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406678"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="76240-103">Přehled zabezpečení ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="76240-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="76240-104">ASP.NET Core umožňuje vývojářům snadno nakonfigurovat a spravovat zabezpečení svých aplikací.</span><span class="sxs-lookup"><span data-stu-id="76240-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="76240-105">ASP.NET Core obsahuje funkce pro správu ověřování, autorizace, ochrany dat, vynucování protokolu HTTPS, tajných klíčů aplikací, ochrany proti padělání a správě CORS.</span><span class="sxs-lookup"><span data-stu-id="76240-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="76240-106">Tyto funkce zabezpečení umožňují vytvářet robustní a ještě zabezpečené aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="76240-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="76240-107">Funkce zabezpečení ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="76240-107">ASP.NET Core security features</span></span>

<span data-ttu-id="76240-108">ASP.NET Core poskytuje mnoho nástrojů a knihoven pro zabezpečení vašich aplikací včetně integrovaných zprostředkovatelů identity, ale můžete použít služby identity třetích stran, jako je Facebook, Twitter a LinkedIn.</span><span class="sxs-lookup"><span data-stu-id="76240-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="76240-109">Pomocí ASP.NET Core můžete snadno spravovat tajné kódy aplikací, které představují způsob ukládání a používání důvěrných informací bez nutnosti jejich vystavení v kódu.</span><span class="sxs-lookup"><span data-stu-id="76240-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="76240-110">Ověřování vs. autorizace</span><span class="sxs-lookup"><span data-stu-id="76240-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="76240-111">Ověřování je proces, ve kterém uživatel poskytne přihlašovací údaje, které jsou pak porovnány s uživateli uloženými v operačním systému, databázi, aplikaci nebo prostředku.</span><span class="sxs-lookup"><span data-stu-id="76240-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="76240-112">Pokud se shodují, uživatelé se úspěšně ověřují a pak můžou v rámci autorizačního procesu provádět akce, ke kterým mají oprávnění.</span><span class="sxs-lookup"><span data-stu-id="76240-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="76240-113">Autorizace se týká procesu, který určuje, co může uživatel dělat.</span><span class="sxs-lookup"><span data-stu-id="76240-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="76240-114">Dalším způsobem, jak si představit ověřování, je zvážit jako způsob, jak zadat mezeru, jako je například server, databáze, aplikace nebo prostředek, zatímco autorizace je to, které akce může uživatel provádět, pro které objekty uvnitř daného prostoru (Server, databáze nebo aplikace).</span><span class="sxs-lookup"><span data-stu-id="76240-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="76240-115">Běžné chyby zabezpečení v softwaru</span><span class="sxs-lookup"><span data-stu-id="76240-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="76240-116">ASP.NET Core a EF obsahují funkce, které vám pomůžou zabezpečit aplikace a zabránit porušení zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="76240-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="76240-117">Následující seznam odkazů vás přesměruje na dokumentaci s podrobnými postupy, aby se předešlo běžným chybám zabezpečení ve webových aplikacích:</span><span class="sxs-lookup"><span data-stu-id="76240-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="76240-118">Útoky skriptování mezi weby</span><span class="sxs-lookup"><span data-stu-id="76240-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="76240-119">Útoky prostřednictvím injektáže SQL</span><span class="sxs-lookup"><span data-stu-id="76240-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="76240-120">Padělání žádostí mezi weby (CSRF)</span><span class="sxs-lookup"><span data-stu-id="76240-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="76240-121">Otevření útoků přesměrování</span><span class="sxs-lookup"><span data-stu-id="76240-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="76240-122">Existuje více ohrožení zabezpečení, o kterých byste měli vědět.</span><span class="sxs-lookup"><span data-stu-id="76240-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="76240-123">Další informace najdete v dalších článcích v části \*\*zabezpečení a Identity \*\* oddíl obsahu.</span><span class="sxs-lookup"><span data-stu-id="76240-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
