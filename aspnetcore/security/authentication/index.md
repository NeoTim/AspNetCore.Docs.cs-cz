---
title: Přehled ověřování ASP.NET jádra
author: mjrousos
description: Další informace o ověřování v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
uid: security/authentication/index
ms.openlocfilehash: 404904ecfa30d1fe7e47f0daaa423ddd6f1b06e8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434327"
---
# <a name="overview-of-aspnet-core-authentication"></a>Přehled ověřování ASP.NET jádra

Podle [Mike Rousos](https://github.com/mjrousos)

Ověřování je proces určení identity uživatele. [Autorizace](xref:security/authorization/introduction) je proces určení, zda má uživatel přístup k prostředku. V ASP.NET Core, ověřování `IAuthenticationService`je zpracována , který se používá ověřování [middleware](xref:fundamentals/middleware/index). Ověřovací služba používá registrované obslužné rutiny ověřování k dokončení akcí souvisejících s ověřováním. Příklady akcí souvisejících s ověřováním:

* Ověřování uživatele.
* Odpovídá, když se neověřený uživatel pokusí o přístup k omezenému prostředku.

Registrované obslužné rutiny ověřování a jejich možnosti konfigurace se nazývají "schémata".

Schémata ověřování jsou určena registrací `Startup.ConfigureServices`ověřovacích služeb v :

* Voláním metody rozšíření specifické pro schéma `services.AddAuthentication` po `AddJwtBearer` volání `AddCookie`(například nebo , například). Tyto metody rozšíření používají [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) k registraci schémat s příslušným nastavením.
* Méně často voláním [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) přímo.

Například následující kód registruje ověřovací služby a obslužné rutiny pro schémata ověřování cookie a JWT nosiče:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

Parametr `AddAuthentication` `JwtBearerDefaults.AuthenticationScheme` je název schématu, které se má použít ve výchozím nastavení, pokud není požadováno určité schéma.

Pokud se používá více schémat, mohou zásady autorizace (nebo atributy autorizace) [určit schéma ověřování (nebo schémata),](xref:security/authorization/limitingidentitybyscheme) na kterých závisí při ověření uživatele. Ve výše uvedeném příkladu může být schéma ověřování`CookieAuthenticationDefaults.AuthenticationScheme` souborů cookie použito zadáním jeho názvu `AddCookie`(ve výchozím nastavení, i když při volání může být poskytnut jiný název).

V některých případech `AddAuthentication` je volání automaticky provedeno jinými metodami rozšíření. Například při použití ASP.NET základní `AddAuthentication` [identity](xref:security/authentication/identity), se nazývá interně.

Middleware ověřování je `Startup.Configure` přidán voláním metody <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> rozšíření v `IApplicationBuilder`aplikaci . Volání `UseAuthentication` registruje middleware, který používá dříve registrovaná schémata ověřování. Volejte `UseAuthentication` před jakýmkoli middlewarem, který závisí na ověřených uživatelích. Při použití směrování koncového `UseAuthentication` bodu musí volání přejít:

* Po `UseRouting`, tak, aby informace o trase je k dispozici pro rozhodnutí o ověření.
* Před `UseEndpoints`, tak, aby uživatelé jsou ověřeny před přístupem ke koncovým bodům.

## <a name="authentication-concepts"></a>Koncepty ověřování

### <a name="authentication-scheme"></a>Schéma ověřování

Schéma ověřování je název, který odpovídá:

* Obslužná rutina ověřování.
* Možnosti konfigurace konkrétní instance obslužné rutiny.

Schémata jsou užitečné jako mechanismus pro odkazování na ověřování, výzvu a zakázat chování přidružené obslužné rutiny. Zásady autorizace mohou například použít názvy schémat k určení, které schéma ověřování (nebo schémata) by měly být použity k ověření uživatele. Při konfiguraci ověřování je běžné určit výchozí schéma ověřování. Výchozí schéma se používá, pokud zdroj nepožaduje určité schéma. Je také možné:

* Zadejte různá výchozí schémata, která mají být používána pro akce ověření, výzvy a zakázat.
* Zkombinujte více schémat do jednoho pomocí [schémat zásad](xref:security/authentication/policyschemes).

### <a name="authentication-handler"></a>Obslužná rutina ověřování

Obslužná rutina ověřování:

* Je typ, který implementuje chování schématu.
* Je odvozen <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> od <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>nebo .
* Má primární odpovědnost za ověření uživatelů.

Na základě konfigurace schématu ověřování a kontextu příchozího požadavku jsou obslužné rutiny ověřování:

* Vytvořte <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objekty představující identitu uživatele, pokud je ověření úspěšné.
* Vrátí 'žádný výsledek' nebo 'selhání', pokud je neúspěšné ověřování.
* Mít metody pro výzvu a zakázat akce, když se uživatelé pokusí o přístup k prostředkům:
  * Jsou neoprávněné přístup (zakázat).
  * Pokud jsou neověřené (výzva).

### <a name="authenticate"></a>Ověření

Akce ověření schématu ověřování je zodpovědná za vytvoření identity uživatele na základě kontextu požadavku. Vrátí označující, <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> zda bylo ověření úspěšné, a pokud ano, identitu uživatele v lístku ověřování. Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Příklady ověření zahrnují:

* Schéma ověřování souborů cookie, které vytváří identitu uživatele z souborů cookie.
* JWT nosič schéma rekonstruovat a ověřování JWT nosič token k vytvoření identity uživatele.

### <a name="challenge"></a>Úloha

Výzva ověřování je vyvolána autorizací, když neověřený uživatel požaduje koncový bod, který vyžaduje ověření. Výzva ověřování je vydána například v případě, že anonymní uživatel požaduje omezený prostředek nebo klikne na odkaz pro přihlášení. Autorizace vyvolá výzvu pomocí zadaného schémata ověřování nebo výchozí, pokud není zadánžádný. Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Příklady výzev ověřování zahrnují:

* Schéma ověřování souborů cookie přesměrovává uživatele na přihlašovací stránku.
* JWT nosič schéma vrací výsledek 401 `www-authenticate: bearer` s hlavičkou.

Akce výzvy by měla dát uživateli vědět, jaký mechanismus ověřování použít pro přístup k požadovanému prostředku.

### <a name="forbid"></a>Zakázat

Akce zakázat schéma ověřování je volána autorizací, když se ověřený uživatel pokusí o přístup k prostředku, ke kterému nemá přístup. Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. Mezi příklady, které zakazují ověřování, patří:
* Schéma ověřování souborů cookie přesměrovávající uživatele na stránku označující přístup bylo zakázáno.
* JWT nosič erace systém vrací 403 výsledek.
* Vlastní schéma ověřování přesměrovávající na stránku, na které může uživatel požádat o přístup k prostředku.

Zakázaná akce může dát uživateli vědět:

* Jsou ověřeny.
* Není jim povolen přístup k požadovanému prostředku.

Rozdíly mezi výzvou a příkazem k příkazu naleznete v následujících odkazech:

* [Zavažte a zakažte provozní obslužnou rutinu zdrojů](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).
* [Rozdíly mezi výzvou a zakázanou](xref:security/authorization/secure-data#challenge).

## <a name="authentication-providers-per-tenant"></a>Zprostředkovatelé ověřování na klienta

ASP.NET core framework nemá integrované řešení pro ověřování více klientů.
I když je jistě možné, aby zákazníci napsali jeden, pomocí vestavěných funkcí, doporučujeme zákazníkům, aby se pro tento účel podívali do [Orchard Core.](https://www.orchardcore.net/)

Orchard Core je:

* Modulární a víceklientská architektura aplikací s otevřeným zdrojovým kódem vytvořená s ASP.NET Core.
* Systém pro správu obsahu (CMS) postavený na tomto rozhraní aplikace.

Příklad poskytovatelů ověřování na klienta najdete ve zdroji [Orchard Core.](https://github.com/OrchardCMS/OrchardCore)

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
