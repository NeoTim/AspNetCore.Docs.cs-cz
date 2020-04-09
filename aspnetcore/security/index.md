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
# <a name="overview-of-aspnet-core-security"></a>Přehled základních ASP.NET zabezpečení

ASP.NET Core umožňuje vývojářům snadno konfigurovat a spravovat zabezpečení svých aplikací. ASP.NET Core obsahuje funkce pro správu ověřování, autorizace, ochrany dat, vynucení https, tajných kódů aplikací, ochrany proti padělání a správy CORS. Tyto funkce zabezpečení umožňují vytvářet robustní a přesto bezpečné ASP.NET základní aplikace.

## <a name="aspnet-core-security-features"></a>ASP.NET Základní funkce zabezpečení

ASP.NET Core poskytuje mnoho nástrojů a knihoven pro zabezpečení vašich aplikací, včetně integrovaných poskytovatelů identit, ale můžete použít služby identity třetích stran, jako je Facebook, Twitter nebo LinkedIn. Pomocí ASP.NET Core můžete snadno spravovat tajné kódy aplikací, což je způsob ukládání a používání důvěrných informací, aniž byste je museli vystavit v kódu.

## <a name="authentication-vs-authorization"></a>Ověřování vs. Autorizace

Ověřování je proces, ve kterém uživatel poskytuje pověření, která jsou pak porovnány s těmi, které jsou uloženy v operačním systému, databázi, aplikaci nebo prostředku. Pokud se shodují, uživatelé se úspěšně ověří a pak mohou během procesu autorizace provádět akce, pro které jsou oprávněni. Autorizace odkazuje na proces, který určuje, co má uživatel povoleno.

Dalším způsobem, jak si ověření myslet, je považovat ho za způsob, jak zadat mezeru, například server, databázi, aplikaci nebo prostředek, zatímco autorizace je, které akce může uživatel provádět, které objekty uvnitř tohoto prostoru (server, databáze nebo aplikace).

## <a name="common-vulnerabilities-in-software"></a>Běžné chyby zabezpečení softwaru

ASP.NET Core a EF obsahují funkce, které vám pomohou zabezpečit vaše aplikace a zabránit narušení zabezpečení. Následující seznam odkazů vás přenese na postupy dokumentace s podrobnostmi, abyste se vyhnuli nejčastějším chybám zabezpečení ve webových aplikacích:

* [Útoky skriptování mezi weby](xref:security/cross-site-scripting)
* [Útoky prostřednictvím injektáže SQL](/ef/core/querying/raw-sql)
* [Padělání žádostí napříč místy (CSRF)](xref:security/anti-request-forgery)
* [Otevřené útoky přesměrování](xref:security/preventing-open-redirects)

Existuje více chyb zabezpečení, které byste měli vědět. Další informace naleznete v dalších článcích v části **Zabezpečení a identita** v obsahu.
