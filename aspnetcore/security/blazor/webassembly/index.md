---
title: Zabezpečené ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se, jak zabezpečit aplikace Blazor WebAssemlby jako aplikace s jedním stránkou (jednostránkové).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: 652d4c61110f786396d9d5af4f131b817c40e333
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219243"
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
* Samostatná JWTs nabízí záruky pro klienta a server o procesu ověřování. Například klient má prostředky ke zjištění a ověření, že tokeny, které obdrží, jsou legitimní a byly vygenerovány jako součást daného ověřovacího procesu. Pokud se třetí strana pokusí o přepnutí tokenu uprostřed procesu ověřování, může klient detekovat přepínaný token a vyhnout se jeho použití.
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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Možnosti pro hostované aplikace a poskytovatele přihlašovacích údajů třetích stran

Při ověřování a autorizaci hostované Blazor aplikace WebAssembly s poskytovatelem třetí strany je k dispozici několik možností pro ověření uživatele. Kterou zvolíte, závisí na vašem scénáři.

Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Ověřování uživatelů pro volání rozhraní API chráněných třetích stran

Ověřit uživatele pomocí toku oAuth na straně klienta proti poskytovateli rozhraní API třetí strany:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 V tomto scénáři:

* Server, který je hostitelem aplikace, nehraje roli.
* Rozhraní API na serveru nejde chránit.
* Aplikace může volat jenom chráněná rozhraní API třetích stran.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Ověřování uživatelů pomocí poskytovatele třetí strany a volání chráněných rozhraní API na hostitelském serveru a třetí straně

Nakonfigurujte identitu pomocí poskytovatele přihlášení třetí strany. Získejte tokeny vyžadované pro přístup k rozhraní API třetích stran a uložte je.

Když se uživatel přihlásí, identita shromažďuje přístup a aktualizuje tokeny v rámci procesu ověřování. V tomto okamžiku je k dispozici několik přístupů pro volání rozhraní API třetích stran.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Použití přístupového tokenu serveru k načtení přístupového tokenu třetí strany

K načtení přístupového tokenu třetí strany z koncového bodu rozhraní API serveru použijte přístupový token vygenerovaný na serveru. Odtud pomocí přístupového tokenu třetí strany můžete volat prostředky rozhraní API třetích stran přímo z identity na klientovi.

Tento postup nedoporučujeme. Tento přístup vyžaduje ošetření přístupového tokenu třetí strany, jako kdyby byl vygenerován pro veřejného klienta. V případech oAuth nemá veřejná aplikace tajný klíč klienta, protože nemůže být důvěryhodná pro bezpečné ukládání tajných kódů a přístupového tokenu se vytvoří pro důvěrného klienta. Důvěrný klient je klient, který má tajný klíč klienta a předpokládá, že bude moci bezpečně ukládat tajné klíče.

* Přístupovému tokenu třetí strany se můžou udělit další obory, které budou provádět citlivé operace na základě skutečnosti, že třetí strana vygenerovala token pro více důvěryhodných klientů.
* Podobně by se aktualizační tokeny neměly vystavit klientovi, který není důvěryhodný, protože by to tak měl klienta bez omezení přístupu, pokud se na ně neukládají jiná omezení.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Volání rozhraní API z klienta na rozhraní API serveru za účelem volání rozhraní API třetích stran

Naplňte volání rozhraní API z klienta na serverové rozhraní API. Ze serveru načtěte přístupový token pro prostředek rozhraní API třetí strany a vydejte jakékoli volání, které je potřeba.

I když tento přístup vyžaduje další síťové směrování prostřednictvím serveru za účelem volání rozhraní API třetí strany, má za následek bezpečnější prostředí:

* Server může ukládat aktualizační tokeny a zajistit, aby aplikace neztratila přístup k prostředkům třetích stran.
* Aplikace nemůže zajímat přístupové tokeny ze serveru, který může obsahovat citlivá oprávnění.
