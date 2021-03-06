---
title: Přehled ověřování ASP.NET Core
author: mjrousos
description: Přečtěte si o ověřování v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
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
uid: security/authentication/index
ms.openlocfilehash: 2511d68dd049ca47dea002d8960e7d2fd0207383
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632639"
---
# <a name="overview-of-aspnet-core-authentication"></a>Přehled ověřování ASP.NET Core

O [Jan Rousos](https://github.com/mjrousos)

Ověřování je proces určování identity uživatele. [Autorizace](xref:security/authorization/introduction) je proces, který určuje, jestli má uživatel přístup k prostředku. V ASP.NET Core se ověřování zpracovává pomocí `IAuthenticationService` , který používá [middleware](xref:fundamentals/middleware/index)ověřování. Ověřovací služba používá zaregistrované obslužné rutiny ověřování k dokončení akcí souvisejících s ověřováním. Mezi příklady akcí souvisejících s ověřováním patří:

* Ověřování uživatele.
* Odpověď, když se neověřený uživatel pokusí o přístup k prostředku s omezeným přístupem.

Registrované obslužné rutiny ověřování a jejich možnosti konfigurace se nazývají "schémata".

Schémata ověřování jsou určena registrací služby ověřování v nástroji `Startup.ConfigureServices` :

* Voláním metody rozšíření specifické pro schéma po volání `services.AddAuthentication` (například `AddJwtBearer` nebo například `AddCookie` ). Tyto metody rozšíření používají [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) k registraci schémat s odpovídajícím nastavením.
* Méně často zavoláním [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) přímo.

Například následující kód zaregistruje ověřovací služby a obslužné rutiny pro cookie ověřovací schémata JWT Bearer:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

`AddAuthentication`Parametr `JwtBearerDefaults.AuthenticationScheme` je název schématu, který se použije ve výchozím nastavení, když konkrétní schéma není požadováno.

Pokud se používá víc schémat, zásady autorizace (nebo autorizační atributy) můžou [určit schéma ověřování (nebo schémata)](xref:security/authorization/limitingidentitybyscheme) , na kterých závisí, aby ověřili uživatele. V předchozím příkladu se cookie schéma ověřování dá použít tak, že určíte jeho název ( `CookieAuthenticationDefaults.AuthenticationScheme` ve výchozím nastavení, přestože se při volání dá zadat jiný název `AddCookie` ).

V některých případech `AddAuthentication` je volání automaticky provedeno jinými metodami rozšíření. Například při použití [ASP.NET Core Identity](xref:security/authentication/identity) , `AddAuthentication` se nazývá interně.

Middleware ověřování se přidávají `Startup.Configure` voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> metody rozšíření v aplikaci `IApplicationBuilder` . Volání `UseAuthentication` registruje middleware, který používá dříve registrovaná schémata ověřování. Zavolejte `UseAuthentication` před jakýkoli middleware, který závisí na ověřených uživatelích. Při použití směrování koncového bodu musí volání metody `UseAuthentication` jít:

* Po `UseRouting` , takže informace o trasách jsou k dispozici pro rozhodování o ověřování.
* Před `UseEndpoints` přístupem k koncovým bodům, aby bylo možné uživatele ověřit.

## <a name="authentication-concepts"></a>Koncepty ověřování

### <a name="authentication-scheme"></a>Schéma ověřování

Schéma ověřování je název, který odpovídá:

* Obslužná rutina ověřování.
* Možnosti pro konfiguraci této konkrétní instance obslužné rutiny.

Schémata jsou užitečná jako mechanismus pro odkazování na chování související obslužné rutiny při ověřování, výzvě a nezakazujících. Například zásady autorizace můžou použít názvy schémat k určení, které schéma ověřování (nebo schémat) by se mělo použít k ověření uživatele. Při konfiguraci ověřování je běžné určit výchozí schéma ověřování. Výchozí schéma se použije, pokud prostředek nepožaduje konkrétní schéma. Je také možné:

* Zadejte různá výchozí schémata, která se mají použít pro akce ověřování, výzvy a zakazujení.
* Kombinování několika schémat do jednoho pomocí [schémat zásad](xref:security/authentication/policyschemes).

### <a name="authentication-handler"></a>Obslužná rutina ověřování

Obslužná rutina ověřování:

* Je typ, který implementuje chování schématu.
* Je odvozen z <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> nebo <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .
* Má primární zodpovědnost za ověřování uživatelů.

Obslužné rutiny ověřování na základě konfigurace schématu ověřování a kontextu příchozího požadavku:

* Konstrukce <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objektů představujících identitu uživatele, je-li ověření úspěšné.
* Pokud je ověřování neúspěšné, vraťte se k žádnému výsledku nebo neúspěchu.
* Mít metody pro výzvu a nezakazující akce, když se uživatelé pokusí o přístup k prostředkům:
  * Nemají oprávnění k přístupu (zakázat).
  * Pokud nejsou ověřeny (výzva).

### <a name="authenticate"></a>Ověření

Akce ověření schématu ověřování zodpovídá za sestavení identity uživatele na základě kontextu požadavku. Vrátí hodnotu <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> určující, zda bylo ověřování úspěšné, a pokud ano, identitu uživatele v ověřovacím lístku. Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Mezi příklady ověřování patří:

* cookieSchéma ověřování vytvářející identitu uživatele od cookie s.
* Deserializace a ověření nosného tokenu JWT pro sestavení identity uživatele.

### <a name="challenge"></a>Úkol

Ověření je vyvoláno autorizací, když neověřený uživatel požádá o koncový bod, který vyžaduje ověření. Výzva k ověření se vydá, například když anonymní uživatel požádá o omezený prostředek nebo klikne na odkaz pro přihlášení. Autorizace vyvolá výzvu pomocí zadaných schémat ověřování nebo výchozí hodnota, pokud není zadána. Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Příklady výzev k ověřování zahrnují:

* cookieSchéma ověřování přesměrovává uživatele na přihlašovací stránku.
* Schéma nosiče JWT vracející 401 výsledek s `www-authenticate: bearer` hlavičkou.

Akce výzvy by měla dát uživateli informace o tom, jaký ověřovací mechanismus použít pro přístup k požadovanému prostředku.

### <a name="forbid"></a>Zalomen

Akce zakázat autorizačnímu schématu je volána v případě, že se ověřený uživatel pokusí o přístup k prostředku, ke kterému nemají přístup. Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. Mezi příklady ověřování, které patří:
* cookieSchéma ověřování přesměrovává uživatele na stránku oznamující, že byl zakázán přístup.
* Schéma nosiče JWT vracející výsledek 403.
* Vlastní schéma ověřování přesměrované na stránku, kde může uživatel požádat o přístup k prostředku.

Akce zakázat může uživateli informovat:

* Jsou ověřeny.
* Nemají oprávnění k přístupu k požadovanému prostředku.

Rozdíly mezi výzvou a zakazují se zobrazují na následujících odkazech:

* [Pomocí obslužné rutiny provozního prostředku vyvolávat výzvu a nezakazují](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).
* [Rozdíly mezi výzvou a zakazují](xref:security/authorization/secure-data#challenge).

## <a name="authentication-providers-per-tenant"></a>Poskytovatelé ověřování na tenanta

Rozhraní ASP.NET Core Framework nemá integrované řešení pro ověřování s více klienty.
I když je možné, že si je zákazníci zapsali pomocí integrovaných funkcí, doporučujeme zákazníkům, aby si pro tento účel vyhledali [sadu jader](https://www.orchardcore.net/) .

Základní sadu:

* Open Source modulární a víceklientské architektura aplikací sestavená s ASP.NET Core.
* Systém pro správu obsahu (CMS) založený na daném rozhraní aplikace.

Příklad zprostředkovatelů ověřování pro každého klienta najdete v tématu [základní](https://github.com/OrchardCMS/OrchardCore) zdroj sady.

## <a name="additional-resources"></a>Další zdroje informací

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [Globálně vyžadovat ověřené uživatele](xref:security/authorization/secure-data#rau)