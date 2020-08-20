---
title: Přehled zabezpečení ASP.NET Core
author: rick-anderson
description: Seznamte se se základy ověřování, autorizace a zabezpečení v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/index
ms.openlocfilehash: 0378fd06b5cae5b8911e8a2f41937b28d5444538
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632860"
---
# <a name="overview-of-aspnet-core-security"></a>Přehled zabezpečení ASP.NET Core

ASP.NET Core umožňuje vývojářům snadno nakonfigurovat a spravovat zabezpečení svých aplikací. ASP.NET Core obsahuje funkce pro správu ověřování, autorizace, ochrany dat, vynucování protokolu HTTPS, tajných klíčů aplikací, prevence/CSRF prevence a správy CORS. Tyto funkce zabezpečení umožňují vytvářet robustní a ještě zabezpečené aplikace ASP.NET Core.

## <a name="aspnet-core-security-features"></a>Funkce zabezpečení ASP.NET Core

ASP.NET Core poskytuje mnoho nástrojů a knihoven pro zabezpečení vašich aplikací včetně integrovaných zprostředkovatelů identity, ale můžete použít služby identity třetích stran, jako je Facebook, Twitter a LinkedIn. Pomocí ASP.NET Core můžete snadno spravovat tajné kódy aplikací, které představují způsob ukládání a používání důvěrných informací bez nutnosti jejich vystavení v kódu.

## <a name="authentication-vs-authorization"></a>Ověřování vs. autorizace

Ověřování je proces, ve kterém uživatel poskytne přihlašovací údaje, které jsou pak porovnány s uživateli uloženými v operačním systému, databázi, aplikaci nebo prostředku. Pokud se shodují, uživatelé se úspěšně ověřují a pak můžou v rámci autorizačního procesu provádět akce, ke kterým mají oprávnění. Autorizace se týká procesu, který určuje, co může uživatel dělat.

Dalším způsobem, jak si představit ověřování, je zvážit jako způsob, jak zadat mezeru, jako je například server, databáze, aplikace nebo prostředek, zatímco autorizace je to, které akce může uživatel provádět, pro které objekty uvnitř daného prostoru (Server, databáze nebo aplikace).

## <a name="common-vulnerabilities-in-software"></a>Běžné chyby zabezpečení v softwaru

ASP.NET Core a EF obsahují funkce, které vám pomůžou zabezpečit aplikace a zabránit porušení zabezpečení. Následující seznam odkazů vás přesměruje na dokumentaci s podrobnými postupy, aby se předešlo běžným chybám zabezpečení ve webových aplikacích:

* [Útoky skriptování XSS (mezi weby)](xref:security/cross-site-scripting)
* [Útoky prostřednictvím injektáže SQL](/ef/core/querying/raw-sql)
* [Útoky na více než více lokalitách pro falšování požadavků (XSRF/CSRF)](xref:security/anti-request-forgery)
* [Otevření útoků přesměrování](xref:security/preventing-open-redirects)

Existuje více ohrožení zabezpečení, o kterých byste měli vědět. Další informace najdete v dalších článcích v části **zabezpečení a Identity ** oddíl obsahu.
