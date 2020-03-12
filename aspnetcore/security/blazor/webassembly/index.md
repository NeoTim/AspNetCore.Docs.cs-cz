---
title: Zabezpečené ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se, jak zabezpečit aplikace Blazor WebAssemlby jako aplikace s jedním stránkou (jednostránkové).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: 48136d0717998df3311dd5177688e062d0009176
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083837"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>Zabezpečené ASP.NET Core Blazor WebAssembly

[Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor aplikace WebAssembly jsou zabezpečené stejným způsobem jako aplikace s jednou stránkou (jednostránkové). K ověřování uživatelů jednostránkové je k dispozici několik přístupů, ale nejběžnějším a komplexním přístupem je použití implementace založené na [protokolu oAuth 2,0](https://oauth.net/), jako je například [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Knihovna ověřování

Blazor WebAssembly podporuje ověřování a autorizaci aplikací pomocí OIDC prostřednictvím knihovny `Microsoft.AspNetCore.Components.WebAssembly.Authentication`. Knihovna poskytuje sadu primitivních hodnot pro bezproblémové ověřování proti ASP.NET Core back-endy. Knihovna se integruje ASP.NET Core identitou, která podporuje autorizaci rozhraní API, která je postavená na [serveru identit](https://identityserver.io/). Knihovna se může ověřit u libovolného zprostředkovatele identity (IP) třetí strany, který podporuje OIDC, kterým se říká poskytovatelé OpenID (OP).

Podpora ověřování ve službě Blazor WebAssembly je postavená na knihovně *oidc-Client. js* , která se používá ke zpracování podrobností základního ověřovacího protokolu.

K dispozici jsou další možnosti ověřování jednostránkové, jako je například použití souborů cookie SameSite. Technický návrh Blazor WebAssembly se ale na oAuth a OIDC vyrovnává jako nejlepší možnost pro ověřování v Blazor aplikacích pro WebAssembly. [Ověřování založené na tokenech](xref:security/anti-request-forgery#token-based-authentication) založené na [webových tokenech JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) bylo vybráno při [ověřování pomocí souborů cookie](xref:security/anti-request-forgery#cookie-based-authentication) pro funkční a bezpečnostní účely:

* Použití protokolu založeného na tokenu nabízí menší prostor pro útoky, protože tokeny se neodesílají v rámci všech požadavků.
* Koncové body serveru nevyžadují ochranu proti [padělání žádostí mezi lokalitami (CSRF)](xref:security/anti-request-forgery) , protože tokeny se odesílají explicitně. To umožňuje hostovat aplikace Blazor WebAssembly společně s aplikacemi MVC nebo stránek Razor.
* Tokeny mají užší oprávnění než soubory cookie. Například tokeny nelze použít ke správě uživatelského účtu nebo změně hesla uživatele, pokud nejsou tyto funkce explicitně implementovány.
* Tokeny mají krátkou životnost, jednu hodinu ve výchozím nastavení, což omezuje okno útoku. Tokeny je také možné kdykoli odvolat.
* Samostatná JWTs nabízí záruky pro klienta a server o procesu ověřování. Například klient má prostředky ke zjištění a ověření, že tokeny, které obdrží, jsou legitimní a byly vygenerovány jako součást daného procesu ověřování. Pokud se třetí strana pokusí o přepnutí tokenu uprostřed procesu ověřování, může klient detekovat přepínaný token a vyhnout se jeho použití.
* Tokeny s protokolem oAuth a OIDC nespoléhají na správné chování uživatelského agenta, aby bylo zajištěno, že je aplikace zabezpečená.
* Protokoly založené na tokenech, jako je oAuth a OIDC, umožňují ověřování a autorizaci hostovaných a samostatných aplikací se stejnou sadou bezpečnostních charakteristik.

## <a name="authentication-process-with-oidc"></a>Proces ověřování pomocí OIDC

Knihovna `Microsoft.AspNetCore.Components.WebAssembly.Authentication` nabízí několik primitivních primitiv pro implementaci ověřování a autorizaci pomocí OIDC. V rámci širokého hlediska funguje ověřování následujícím způsobem:

* Když anonymní uživatel vybere tlačítko pro přihlášení nebo požádá o stránku s použitým atributem [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) , uživatel se přesměruje na přihlašovací stránku aplikace (`/authentication/login`).
* Na přihlašovací stránce se knihovna ověřování připraví na přesměrování na koncový bod autorizace. Koncový bod autorizace je mimo Blazor aplikace WebAssembly a je možné ho hostovat v samostatném zdroji. Koncový bod zodpovídá za zjištění, jestli je uživatel ověřený a který vydává jednu nebo více tokenů v reakci. Knihovna ověřování poskytuje zpětné volání přihlašovacího jména pro příjem ověřovací odpovědi.
  * Pokud uživatel není ověřený, bude uživatel přesměrován do základního ověřovacího systému, který je obvykle ASP.NET Core identitou.
  * Pokud byl uživatel již ověřen, koncový bod autorizace vygeneruje příslušné tokeny a přesměruje prohlížeč zpět na koncový bod zpětného volání přihlášení (`/authentication/login-callback`).
* Když aplikace Blazor WebAssembly načte koncový bod zpětného volání přihlášení (`/authentication/login-callback`), je zpracována ověřovací odpověď.
  * Pokud se proces ověřování úspěšně dokončí, uživatel se ověří a případně se pošle zpátky na původní chráněnou adresu URL, kterou si uživatel vyžádal.
  * Pokud se proces ověřování z nějakého důvodu nepovede, uživatel se pošle na stránku neúspěšné přihlášení (`/authentication/login-failed`) a zobrazí se chyba.
