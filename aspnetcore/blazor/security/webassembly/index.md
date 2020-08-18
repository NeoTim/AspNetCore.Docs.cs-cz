---
title: ASP.NET Core zabezpečení Blazor WebAssembly
author: guardrex
description: Naučte se zabezpečit Blazor aplikace WebAssemlby jako aplikace s jedním stránkou (jednostránkové).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/index
ms.openlocfilehash: fe9bd270ce509e8389dd3d4f3f6f3a6dda03d425
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504538"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core zabezpečení Blazor WebAssembly

[Javier Calvarro Nelson](https://github.com/javiercn)

Blazor WebAssembly aplikace jsou zabezpečené stejným způsobem jako aplikace s jedním stránkou (jednostránkové). K ověřování uživatelů jednostránkové je k dispozici několik přístupů, ale nejběžnějším a komplexním přístupem je použití implementace založené na [protokolu OAuth 2,0](https://oauth.net/), jako je například [OPENID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Knihovna ověřování

Blazor WebAssembly podporuje ověřování a autorizaci aplikací pomocí OIDC prostřednictvím [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) knihovny. Knihovna poskytuje sadu primitivních hodnot pro bezproblémové ověřování proti ASP.NET Core back-endy. Knihovna se integruje ASP.NET Core Identity s podporou autorizace rozhraní API postavenou na [ Identity serveru](https://identityserver.io/). Knihovna se může ověřit u libovolného Identity poskytovatele (IP) třetí strany, který podporuje OIDC, kterým se říká poskytovatelé OpenID (OP).

Podpora ověřování v nástroji Blazor WebAssembly je postavená na `oidc-client.js` knihovně, která se používá ke zpracování podrobností o podkladovém protokolu ověřování.

Další možnosti pro ověřování jednostránkové existují, jako je například použití SameSite cookie s. Technický návrh pro se ale v Blazor WebAssembly aplikacích OAuth a OIDC vyplatí jako nejlepší možnost pro ověřování v Blazor WebAssembly aplikacích. [Ověřování založené na tokenech](xref:security/anti-request-forgery#token-based-authentication) na základě [webových tokenů JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) bylo vybráno pro účely funkčnosti a zabezpečení [ cookie na základě ověření](xref:security/anti-request-forgery#cookie-based-authentication) :

* Použití protokolu založeného na tokenu nabízí menší prostor pro útoky, protože tokeny se neodesílají v rámci všech požadavků.
* Koncové body serveru nevyžadují ochranu proti [padělání žádostí mezi lokalitami (CSRF)](xref:security/anti-request-forgery) , protože tokeny se odesílají explicitně. To vám umožní hostovat Blazor WebAssembly aplikace souběžně s aplikacemi MVC nebo Razor stránky.
* Tokeny mají užší oprávnění než cookie s. Například tokeny nelze použít ke správě uživatelského účtu nebo změně hesla uživatele, pokud nejsou tyto funkce explicitně implementovány.
* Tokeny mají krátkou životnost, jednu hodinu ve výchozím nastavení, což omezuje okno útoku. Tokeny je také možné kdykoli odvolat.
* Samostatná JWTs nabízí záruky pro klienta a server o procesu ověřování. Například klient má prostředky ke zjištění a ověření, že tokeny, které obdrží, jsou legitimní a byly vygenerovány jako součást daného ověřovacího procesu. Pokud se třetí strana pokusí o přepnutí tokenu uprostřed procesu ověřování, může klient detekovat přepínaný token a vyhnout se jeho použití.
* Tokeny s protokolem OAuth a OIDC nespoléhají na správné chování uživatelského agenta, aby bylo zajištěno, že je aplikace zabezpečená.
* Protokoly založené na tokenech, jako je OAuth a OIDC, umožňují ověřování a autorizaci hostovaných a samostatných aplikací se stejnou sadou bezpečnostních charakteristik.

## <a name="authentication-process-with-oidc"></a>Proces ověřování pomocí OIDC

[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)Knihovna nabízí několik primitivních primitiv pro implementaci ověřování a autorizaci pomocí OIDC. V rámci širokého hlediska funguje ověřování následujícím způsobem:

* Když anonymní uživatel vybere tlačítko pro přihlášení nebo požádá o stránku s [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) použitým atributem, uživatel se přesměruje na přihlašovací stránku aplikace ( `/authentication/login` ).
* Na přihlašovací stránce se knihovna ověřování připraví na přesměrování na koncový bod autorizace. Koncový bod autorizace je mimo Blazor WebAssembly aplikaci a je možné ho hostovat v samostatném zdroji. Koncový bod zodpovídá za zjištění, jestli je uživatel ověřený a který vydává jednu nebo více tokenů v reakci. Knihovna ověřování poskytuje zpětné volání přihlašovacího jména pro příjem ověřovací odpovědi.
  * Pokud uživatel není ověřený, bude uživatel přesměrován do základního ověřovacího systému, který je obvykle ASP.NET Core Identity .
  * Pokud byl uživatel již ověřen, koncový bod autorizace vygeneruje odpovídající tokeny a přesměruje prohlížeč zpět na koncový bod zpětného volání přihlašovacího jména ( `/authentication/login-callback` ).
* Když Blazor WebAssembly aplikace načte koncový bod zpětného volání přihlášení ( `/authentication/login-callback` ), je zpracována ověřovací odpověď.
  * Pokud se proces ověřování úspěšně dokončí, uživatel se ověří a případně se pošle zpátky na původní chráněnou adresu URL, kterou si uživatel vyžádal.
  * Pokud se proces ověřování z nějakého důvodu nepovede, uživatel se pošle na stránku neúspěšného přihlášení ( `/authentication/login-failed` ) a zobrazí se chyba.

## <a name="authentication-component"></a>`Authentication` (komponenta)

`Authentication`Součást ( `Pages/Authentication.razor` ) zpracovává operace vzdáleného ověřování a umožňuje aplikaci:

* Nakonfigurujte trasy aplikace pro stavy ověřování.
* Nastavte obsah uživatelského rozhraní pro stavy ověřování.
* Spravovat stav ověřování.

Akce ověřování, jako je registrace nebo přihlašování uživatele, jsou předány do Blazor <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> komponenty rozhraní, která zůstává v provozu a řídí stav napříč operacemi ověřování.

Další informace a příklady naleznete v tématu <xref:blazor/security/webassembly/additional-scenarios> .

## <a name="authorization"></a>Autorizace

V Blazor WebAssembly aplikacích lze kontroly autorizace obejít, protože všechny kódy na straně klienta mohou být změněny uživateli. Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.

**Na serveru vždy provádějte kontroly autorizace v libovolném koncovém bodu rozhraní API, ke kterému přistupovala aplikace na straně klienta.**

## <a name="require-authorization-for-the-entire-app"></a>Vyžadovat autorizaci pro celou aplikaci

Použijte pro každou komponentu aplikace [ `[Authorize]` atribut](xref:blazor/security/index#authorize-attribute) ([dokumentaci k rozhraní API](xref:System.Web.Mvc.AuthorizeAttribute)) Razor pomocí jednoho z následujících přístupů:

* Použijte [`@attribute`](xref:mvc/views/razor#attribute) v souboru direktivu `_Imports.razor` :

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* Přidejte atribut do každé Razor součásti ve `Pages` složce.

> [!NOTE]
> Nastavení <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> na zásadu se nepodporuje <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> . **not**

## <a name="refresh-tokens"></a>Aktualizovat tokeny

Aktualizace tokenů nemůže být zabezpečená na straně klienta v Blazor WebAssembly aplikacích. Proto se tokeny aktualizace neodesílají do aplikace pro přímé použití.

Aktualizační tokeny může spravovat a používat aplikace na straně serveru v hostovaném Blazor WebAssembly řešení pro přístup k rozhraním API třetích stran. Další informace naleznete v tématu <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.

## <a name="implementation-guidance"></a>Pokyny k implementaci

Články v tomto *přehledu* obsahují informace o ověřování uživatelů v Blazor WebAssembly aplikacích pro konkrétní poskytovatele.

Samostatné Blazor WebAssembly aplikace:

* [Obecné pokyny pro poskytovatele OIDC a knihovnu ověřování pro WebAssembly](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Účty Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

Hostované Blazor WebAssembly aplikace:

* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [Identity WebServer](xref:blazor/security/webassembly/hosted-with-identity-server)

Další pokyny týkající se konfigurace najdete v tématu <xref:blazor/security/webassembly/additional-scenarios> .
