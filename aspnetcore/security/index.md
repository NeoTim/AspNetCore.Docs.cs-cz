---
title: Přehled zabezpečení ASP.NET Core
author: tdykstra
description: Další informace o ověřování, autorizaci a zabezpečení základy v ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: security/index
ms.openlocfilehash: 579e472e01efd08bbafe949e37a3b655a42a5b46
ms.sourcegitcommit: 04b55a5ce9d649ff2df926157ec28ae47afe79e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52156916"
---
# <a name="overview-of-aspnet-core-security"></a>Přehled zabezpečení ASP.NET Core

ASP.NET Core umožňuje vývojářům snadno konfigurovat a spravovat zabezpečení pro své aplikace. ASP.NET Core obsahuje funkce pro správu ověřování, autorizaci, ochranu dat, vynucování SSL, tajných kódů aplikace, ochrana proti padělání požadavků ochrany a správy CORS. Tyto funkce zabezpečení umožňují vytvářet robustní ještě zabezpečení aplikací ASP.NET Core.

## <a name="aspnet-core-security-features"></a>Funkce zabezpečení ASP.NET Core

ASP.NET Core nabízí mnoho nástrojů a knihoven k zabezpečení vašich aplikací, včetně předdefinovaných poskytovatelů identit ale můžete použít 3. stran identit služeb jako je Facebook, Twitter a LinkedIn. ASP.NET Core můžete snadno spravovat tajné kódy aplikace, které představují způsob, jak uložit a použít důvěrné informace, aniž byste museli ho zpřístupnit v kódu.

## <a name="authentication-vs-authorization"></a>Ověřování vs. Autorizace

Ověřování je proces, ve kterém uživatel zadá přihlašovací údaje, které jsou pak porovnána s uloženými v operačním systému, databáze, aplikace nebo prostředku. Pokud se shodují, úspěšně ověřit uživatele a pak můžete provádět akce, které mají autorizaci pro, během autorizace. Povolení se vztahuje k procesu, která určuje, co uživatel může dělat.

Dalším způsobem, jak si můžete představit ověřování je vzít v úvahu jako způsob, jak při autorizaci akce, které může uživatel provádět, do které objekty do tohoto místa (server, databáze nebo aplikace) zadejte mezeru, například server, databáze, aplikace nebo prostředku.

## <a name="common-vulnerabilities-in-software"></a>Běžné chyby v softwaru

ASP.NET Core a EF obsahují funkce, které vám pomůžou zabezpečit vaše aplikace a zabránit porušení zabezpečení. Následující seznam odkazů přejdete k dokumentaci s podrobnostmi o techniky, aby nejběžnějších ohrožení zabezpečení ve službě web apps:

* [Útoky skriptování napříč weby](xref:security/cross-site-scripting)
* [Útoky prostřednictvím injektáže SQL](/ef/core/querying/raw-sql)
* [Proti padělání požadavků mezi weby (CSRF)](xref:security/anti-request-forgery)
* [Útoky na otevřeném přesměrování](xref:security/preventing-open-redirects)

Existují další chyby zabezpečení, které byste měli vědět. Další informace najdete v tématu v dalších článcích v **zabezpečení a identita** část obsahu.
