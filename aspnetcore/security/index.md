---
title: Přehled zabezpečení ASP.NET Core
author: rick-anderson
description: Seznamte se se základy ověřování, autorizace a zabezpečení v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/index
ms.openlocfilehash: b507832e34ac850d2bd4e80bab3066e73ea2ad95
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776510"
---
# <a name="overview-of-aspnet-core-security"></a>Přehled zabezpečení ASP.NET Core

ASP.NET Core umožňuje vývojářům snadno nakonfigurovat a spravovat zabezpečení svých aplikací. ASP.NET Core obsahuje funkce pro správu ověřování, autorizace, ochrany dat, vynucování protokolu HTTPS, tajných klíčů aplikací, ochrany proti padělání a správě CORS. Tyto funkce zabezpečení umožňují vytvářet robustní a ještě zabezpečené aplikace ASP.NET Core.

## <a name="aspnet-core-security-features"></a>Funkce zabezpečení ASP.NET Core

ASP.NET Core poskytuje mnoho nástrojů a knihoven pro zabezpečení vašich aplikací včetně integrovaných Identity poskytovatelů, ale můžete použít služby identity třetích stran, jako je Facebook, Twitter nebo LinkedIn. Pomocí ASP.NET Core můžete snadno spravovat tajné kódy aplikací, které představují způsob ukládání a používání důvěrných informací bez nutnosti jejich vystavení v kódu.

## <a name="authentication-vs-authorization"></a>Ověřování vs. autorizace

Ověřování je proces, ve kterém uživatel poskytne přihlašovací údaje, které jsou pak porovnány s uživateli uloženými v operačním systému, databázi, aplikaci nebo prostředku. Pokud se shodují, uživatelé se úspěšně ověřují a pak můžou v rámci autorizačního procesu provádět akce, ke kterým mají oprávnění. Autorizace se týká procesu, který určuje, co může uživatel dělat.

Dalším způsobem, jak si představit ověřování, je zvážit jako způsob, jak zadat mezeru, jako je například server, databáze, aplikace nebo prostředek, zatímco autorizace je to, které akce může uživatel provádět, pro které objekty uvnitř daného prostoru (Server, databáze nebo aplikace).

## <a name="common-vulnerabilities-in-software"></a>Běžné chyby zabezpečení v softwaru

ASP.NET Core a EF obsahují funkce, které vám pomůžou zabezpečit aplikace a zabránit porušení zabezpečení. Následující seznam odkazů vás přesměruje na dokumentaci s podrobnými postupy, aby se předešlo běžným chybám zabezpečení ve webových aplikacích:

* [Útoky skriptování mezi weby](xref:security/cross-site-scripting)
* [Útoky prostřednictvím injektáže SQL](/ef/core/querying/raw-sql)
* [Padělání žádostí mezi weby (CSRF)](xref:security/anti-request-forgery)
* [Otevření útoků přesměrování](xref:security/preventing-open-redirects)

Existuje více ohrožení zabezpečení, o kterých byste měli vědět. Další informace najdete v dalších článcích v části **zabezpečení a Identity ** oddíl obsahu.
