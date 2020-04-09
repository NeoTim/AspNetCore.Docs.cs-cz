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
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="52899-103">Přehled ověřování ASP.NET jádra</span><span class="sxs-lookup"><span data-stu-id="52899-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="52899-104">Podle [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="52899-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="52899-105">Ověřování je proces určení identity uživatele.</span><span class="sxs-lookup"><span data-stu-id="52899-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="52899-106">[Autorizace](xref:security/authorization/introduction) je proces určení, zda má uživatel přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="52899-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="52899-107">V ASP.NET Core, ověřování `IAuthenticationService`je zpracována , který se používá ověřování [middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="52899-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="52899-108">Ověřovací služba používá registrované obslužné rutiny ověřování k dokončení akcí souvisejících s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="52899-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="52899-109">Příklady akcí souvisejících s ověřováním:</span><span class="sxs-lookup"><span data-stu-id="52899-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="52899-110">Ověřování uživatele.</span><span class="sxs-lookup"><span data-stu-id="52899-110">Authenticating a user.</span></span>
* <span data-ttu-id="52899-111">Odpovídá, když se neověřený uživatel pokusí o přístup k omezenému prostředku.</span><span class="sxs-lookup"><span data-stu-id="52899-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="52899-112">Registrované obslužné rutiny ověřování a jejich možnosti konfigurace se nazývají "schémata".</span><span class="sxs-lookup"><span data-stu-id="52899-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="52899-113">Schémata ověřování jsou určena registrací `Startup.ConfigureServices`ověřovacích služeb v :</span><span class="sxs-lookup"><span data-stu-id="52899-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="52899-114">Voláním metody rozšíření specifické pro schéma `services.AddAuthentication` po `AddJwtBearer` volání `AddCookie`(například nebo , například).</span><span class="sxs-lookup"><span data-stu-id="52899-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="52899-115">Tyto metody rozšíření používají [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) k registraci schémat s příslušným nastavením.</span><span class="sxs-lookup"><span data-stu-id="52899-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="52899-116">Méně často voláním [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) přímo.</span><span class="sxs-lookup"><span data-stu-id="52899-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="52899-117">Například následující kód registruje ověřovací služby a obslužné rutiny pro schémata ověřování cookie a JWT nosiče:</span><span class="sxs-lookup"><span data-stu-id="52899-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="52899-118">Parametr `AddAuthentication` `JwtBearerDefaults.AuthenticationScheme` je název schématu, které se má použít ve výchozím nastavení, pokud není požadováno určité schéma.</span><span class="sxs-lookup"><span data-stu-id="52899-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="52899-119">Pokud se používá více schémat, mohou zásady autorizace (nebo atributy autorizace) [určit schéma ověřování (nebo schémata),](xref:security/authorization/limitingidentitybyscheme) na kterých závisí při ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="52899-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="52899-120">Ve výše uvedeném příkladu může být schéma ověřování`CookieAuthenticationDefaults.AuthenticationScheme` souborů cookie použito zadáním jeho názvu `AddCookie`(ve výchozím nastavení, i když při volání může být poskytnut jiný název).</span><span class="sxs-lookup"><span data-stu-id="52899-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="52899-121">V některých případech `AddAuthentication` je volání automaticky provedeno jinými metodami rozšíření.</span><span class="sxs-lookup"><span data-stu-id="52899-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="52899-122">Například při použití ASP.NET základní `AddAuthentication` [identity](xref:security/authentication/identity), se nazývá interně.</span><span class="sxs-lookup"><span data-stu-id="52899-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="52899-123">Middleware ověřování je `Startup.Configure` přidán voláním metody <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> rozšíření v `IApplicationBuilder`aplikaci .</span><span class="sxs-lookup"><span data-stu-id="52899-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="52899-124">Volání `UseAuthentication` registruje middleware, který používá dříve registrovaná schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="52899-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="52899-125">Volejte `UseAuthentication` před jakýmkoli middlewarem, který závisí na ověřených uživatelích.</span><span class="sxs-lookup"><span data-stu-id="52899-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="52899-126">Při použití směrování koncového `UseAuthentication` bodu musí volání přejít:</span><span class="sxs-lookup"><span data-stu-id="52899-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="52899-127">Po `UseRouting`, tak, aby informace o trase je k dispozici pro rozhodnutí o ověření.</span><span class="sxs-lookup"><span data-stu-id="52899-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="52899-128">Před `UseEndpoints`, tak, aby uživatelé jsou ověřeny před přístupem ke koncovým bodům.</span><span class="sxs-lookup"><span data-stu-id="52899-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="52899-129">Koncepty ověřování</span><span class="sxs-lookup"><span data-stu-id="52899-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="52899-130">Schéma ověřování</span><span class="sxs-lookup"><span data-stu-id="52899-130">Authentication scheme</span></span>

<span data-ttu-id="52899-131">Schéma ověřování je název, který odpovídá:</span><span class="sxs-lookup"><span data-stu-id="52899-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="52899-132">Obslužná rutina ověřování.</span><span class="sxs-lookup"><span data-stu-id="52899-132">An authentication handler.</span></span>
* <span data-ttu-id="52899-133">Možnosti konfigurace konkrétní instance obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="52899-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="52899-134">Schémata jsou užitečné jako mechanismus pro odkazování na ověřování, výzvu a zakázat chování přidružené obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="52899-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="52899-135">Zásady autorizace mohou například použít názvy schémat k určení, které schéma ověřování (nebo schémata) by měly být použity k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="52899-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="52899-136">Při konfiguraci ověřování je běžné určit výchozí schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="52899-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="52899-137">Výchozí schéma se používá, pokud zdroj nepožaduje určité schéma.</span><span class="sxs-lookup"><span data-stu-id="52899-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="52899-138">Je také možné:</span><span class="sxs-lookup"><span data-stu-id="52899-138">It's also possible to:</span></span>

* <span data-ttu-id="52899-139">Zadejte různá výchozí schémata, která mají být používána pro akce ověření, výzvy a zakázat.</span><span class="sxs-lookup"><span data-stu-id="52899-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="52899-140">Zkombinujte více schémat do jednoho pomocí [schémat zásad](xref:security/authentication/policyschemes).</span><span class="sxs-lookup"><span data-stu-id="52899-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="52899-141">Obslužná rutina ověřování</span><span class="sxs-lookup"><span data-stu-id="52899-141">Authentication handler</span></span>

<span data-ttu-id="52899-142">Obslužná rutina ověřování:</span><span class="sxs-lookup"><span data-stu-id="52899-142">An authentication handler:</span></span>

* <span data-ttu-id="52899-143">Je typ, který implementuje chování schématu.</span><span class="sxs-lookup"><span data-stu-id="52899-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="52899-144">Je odvozen <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> od <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>nebo .</span><span class="sxs-lookup"><span data-stu-id="52899-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="52899-145">Má primární odpovědnost za ověření uživatelů.</span><span class="sxs-lookup"><span data-stu-id="52899-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="52899-146">Na základě konfigurace schématu ověřování a kontextu příchozího požadavku jsou obslužné rutiny ověřování:</span><span class="sxs-lookup"><span data-stu-id="52899-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="52899-147">Vytvořte <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objekty představující identitu uživatele, pokud je ověření úspěšné.</span><span class="sxs-lookup"><span data-stu-id="52899-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="52899-148">Vrátí 'žádný výsledek' nebo 'selhání', pokud je neúspěšné ověřování.</span><span class="sxs-lookup"><span data-stu-id="52899-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="52899-149">Mít metody pro výzvu a zakázat akce, když se uživatelé pokusí o přístup k prostředkům:</span><span class="sxs-lookup"><span data-stu-id="52899-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="52899-150">Jsou neoprávněné přístup (zakázat).</span><span class="sxs-lookup"><span data-stu-id="52899-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="52899-151">Pokud jsou neověřené (výzva).</span><span class="sxs-lookup"><span data-stu-id="52899-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="52899-152">Ověření</span><span class="sxs-lookup"><span data-stu-id="52899-152">Authenticate</span></span>

<span data-ttu-id="52899-153">Akce ověření schématu ověřování je zodpovědná za vytvoření identity uživatele na základě kontextu požadavku.</span><span class="sxs-lookup"><span data-stu-id="52899-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="52899-154">Vrátí označující, <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> zda bylo ověření úspěšné, a pokud ano, identitu uživatele v lístku ověřování.</span><span class="sxs-lookup"><span data-stu-id="52899-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="52899-155">Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="52899-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="52899-156">Příklady ověření zahrnují:</span><span class="sxs-lookup"><span data-stu-id="52899-156">Authenticate examples include:</span></span>

* <span data-ttu-id="52899-157">Schéma ověřování souborů cookie, které vytváří identitu uživatele z souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="52899-157">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="52899-158">JWT nosič schéma rekonstruovat a ověřování JWT nosič token k vytvoření identity uživatele.</span><span class="sxs-lookup"><span data-stu-id="52899-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="52899-159">Úloha</span><span class="sxs-lookup"><span data-stu-id="52899-159">Challenge</span></span>

<span data-ttu-id="52899-160">Výzva ověřování je vyvolána autorizací, když neověřený uživatel požaduje koncový bod, který vyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="52899-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="52899-161">Výzva ověřování je vydána například v případě, že anonymní uživatel požaduje omezený prostředek nebo klikne na odkaz pro přihlášení.</span><span class="sxs-lookup"><span data-stu-id="52899-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="52899-162">Autorizace vyvolá výzvu pomocí zadaného schémata ověřování nebo výchozí, pokud není zadánžádný.</span><span class="sxs-lookup"><span data-stu-id="52899-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="52899-163">Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="52899-163">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="52899-164">Příklady výzev ověřování zahrnují:</span><span class="sxs-lookup"><span data-stu-id="52899-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="52899-165">Schéma ověřování souborů cookie přesměrovává uživatele na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="52899-165">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="52899-166">JWT nosič schéma vrací výsledek 401 `www-authenticate: bearer` s hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="52899-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="52899-167">Akce výzvy by měla dát uživateli vědět, jaký mechanismus ověřování použít pro přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="52899-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="52899-168">Zakázat</span><span class="sxs-lookup"><span data-stu-id="52899-168">Forbid</span></span>

<span data-ttu-id="52899-169">Akce zakázat schéma ověřování je volána autorizací, když se ověřený uživatel pokusí o přístup k prostředku, ke kterému nemá přístup.</span><span class="sxs-lookup"><span data-stu-id="52899-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="52899-170">Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="52899-170">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="52899-171">Mezi příklady, které zakazují ověřování, patří:</span><span class="sxs-lookup"><span data-stu-id="52899-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="52899-172">Schéma ověřování souborů cookie přesměrovávající uživatele na stránku označující přístup bylo zakázáno.</span><span class="sxs-lookup"><span data-stu-id="52899-172">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="52899-173">JWT nosič erace systém vrací 403 výsledek.</span><span class="sxs-lookup"><span data-stu-id="52899-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="52899-174">Vlastní schéma ověřování přesměrovávající na stránku, na které může uživatel požádat o přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="52899-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="52899-175">Zakázaná akce může dát uživateli vědět:</span><span class="sxs-lookup"><span data-stu-id="52899-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="52899-176">Jsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="52899-176">They are authenticated.</span></span>
* <span data-ttu-id="52899-177">Není jim povolen přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="52899-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="52899-178">Rozdíly mezi výzvou a příkazem k příkazu naleznete v následujících odkazech:</span><span class="sxs-lookup"><span data-stu-id="52899-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="52899-179">[Zavažte a zakažte provozní obslužnou rutinu zdrojů](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span><span class="sxs-lookup"><span data-stu-id="52899-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="52899-180">[Rozdíly mezi výzvou a zakázanou](xref:security/authorization/secure-data#challenge).</span><span class="sxs-lookup"><span data-stu-id="52899-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="52899-181">Zprostředkovatelé ověřování na klienta</span><span class="sxs-lookup"><span data-stu-id="52899-181">Authentication providers per tenant</span></span>

<span data-ttu-id="52899-182">ASP.NET core framework nemá integrované řešení pro ověřování více klientů.</span><span class="sxs-lookup"><span data-stu-id="52899-182">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="52899-183">I když je jistě možné, aby zákazníci napsali jeden, pomocí vestavěných funkcí, doporučujeme zákazníkům, aby se pro tento účel podívali do [Orchard Core.](https://www.orchardcore.net/)</span><span class="sxs-lookup"><span data-stu-id="52899-183">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="52899-184">Orchard Core je:</span><span class="sxs-lookup"><span data-stu-id="52899-184">Orchard Core is:</span></span>

* <span data-ttu-id="52899-185">Modulární a víceklientská architektura aplikací s otevřeným zdrojovým kódem vytvořená s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="52899-185">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="52899-186">Systém pro správu obsahu (CMS) postavený na tomto rozhraní aplikace.</span><span class="sxs-lookup"><span data-stu-id="52899-186">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="52899-187">Příklad poskytovatelů ověřování na klienta najdete ve zdroji [Orchard Core.](https://github.com/OrchardCMS/OrchardCore)</span><span class="sxs-lookup"><span data-stu-id="52899-187">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52899-188">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="52899-188">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
