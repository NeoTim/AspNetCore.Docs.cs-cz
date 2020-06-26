---
title: Přehled ověřování ASP.NET Core
author: mjrousos
description: Přečtěte si o ověřování v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/index
ms.openlocfilehash: a58d48d390eefdc26cf3feb394874b0ba733e9f3
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408341"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="f9cd7-103">Přehled ověřování ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9cd7-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="f9cd7-104">O [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="f9cd7-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="f9cd7-105">Ověřování je proces určování identity uživatele.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="f9cd7-106">[Autorizace](xref:security/authorization/introduction) je proces, který určuje, jestli má uživatel přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="f9cd7-107">V ASP.NET Core se ověřování zpracovává pomocí `IAuthenticationService` , který používá [middleware](xref:fundamentals/middleware/index)ověřování.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="f9cd7-108">Ověřovací služba používá zaregistrované obslužné rutiny ověřování k dokončení akcí souvisejících s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="f9cd7-109">Mezi příklady akcí souvisejících s ověřováním patří:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="f9cd7-110">Ověřování uživatele.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-110">Authenticating a user.</span></span>
* <span data-ttu-id="f9cd7-111">Odpověď, když se neověřený uživatel pokusí o přístup k prostředku s omezeným přístupem.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="f9cd7-112">Registrované obslužné rutiny ověřování a jejich možnosti konfigurace se nazývají "schémata".</span><span class="sxs-lookup"><span data-stu-id="f9cd7-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="f9cd7-113">Schémata ověřování jsou určena registrací služby ověřování v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f9cd7-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="f9cd7-114">Voláním metody rozšíření specifické pro schéma po volání `services.AddAuthentication` (například `AddJwtBearer` nebo například `AddCookie` ).</span><span class="sxs-lookup"><span data-stu-id="f9cd7-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="f9cd7-115">Tyto metody rozšíření používají [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) k registraci schémat s odpovídajícím nastavením.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="f9cd7-116">Méně často zavoláním [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) přímo.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="f9cd7-117">Následující kód například zaregistruje ověřovací služby a obslužné rutiny pro ověřovací schémata souborů cookie a JWT nosiče:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="f9cd7-118">`AddAuthentication`Parametr `JwtBearerDefaults.AuthenticationScheme` je název schématu, který se použije ve výchozím nastavení, když konkrétní schéma není požadováno.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="f9cd7-119">Pokud se používá víc schémat, zásady autorizace (nebo autorizační atributy) můžou [určit schéma ověřování (nebo schémata)](xref:security/authorization/limitingidentitybyscheme) , na kterých závisí, aby ověřili uživatele.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="f9cd7-120">V předchozím příkladu by se schéma ověřování souborů cookie dalo použít tak, že určíte jeho název ( `CookieAuthenticationDefaults.AuthenticationScheme` ve výchozím nastavení, přestože se při volání dá zadat jiný název `AddCookie` ).</span><span class="sxs-lookup"><span data-stu-id="f9cd7-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="f9cd7-121">V některých případech `AddAuthentication` je volání automaticky provedeno jinými metodami rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="f9cd7-122">Například při použití [ASP.NET Core Identity ](xref:security/authentication/identity) `AddAuthentication` se nazývá interně.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="f9cd7-123">Middleware ověřování se přidávají `Startup.Configure` voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> metody rozšíření v aplikaci `IApplicationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f9cd7-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="f9cd7-124">Volání `UseAuthentication` registruje middleware, který používá dříve registrovaná schémata ověřování.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="f9cd7-125">Zavolejte `UseAuthentication` před jakýkoli middleware, který závisí na ověřených uživatelích.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="f9cd7-126">Při použití směrování koncového bodu musí volání metody `UseAuthentication` jít:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="f9cd7-127">Po `UseRouting` , takže informace o trasách jsou k dispozici pro rozhodování o ověřování.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="f9cd7-128">Před `UseEndpoints` přístupem k koncovým bodům, aby bylo možné uživatele ověřit.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="f9cd7-129">Koncepty ověřování</span><span class="sxs-lookup"><span data-stu-id="f9cd7-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="f9cd7-130">Schéma ověřování</span><span class="sxs-lookup"><span data-stu-id="f9cd7-130">Authentication scheme</span></span>

<span data-ttu-id="f9cd7-131">Schéma ověřování je název, který odpovídá:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="f9cd7-132">Obslužná rutina ověřování.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-132">An authentication handler.</span></span>
* <span data-ttu-id="f9cd7-133">Možnosti pro konfiguraci této konkrétní instance obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="f9cd7-134">Schémata jsou užitečná jako mechanismus pro odkazování na chování související obslužné rutiny při ověřování, výzvě a nezakazujících.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="f9cd7-135">Například zásady autorizace můžou použít názvy schémat k určení, které schéma ověřování (nebo schémat) by se mělo použít k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="f9cd7-136">Při konfiguraci ověřování je běžné určit výchozí schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="f9cd7-137">Výchozí schéma se použije, pokud prostředek nepožaduje konkrétní schéma.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="f9cd7-138">Je také možné:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-138">It's also possible to:</span></span>

* <span data-ttu-id="f9cd7-139">Zadejte různá výchozí schémata, která se mají použít pro akce ověřování, výzvy a zakazujení.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="f9cd7-140">Kombinování několika schémat do jednoho pomocí [schémat zásad](xref:security/authentication/policyschemes).</span><span class="sxs-lookup"><span data-stu-id="f9cd7-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="f9cd7-141">Obslužná rutina ověřování</span><span class="sxs-lookup"><span data-stu-id="f9cd7-141">Authentication handler</span></span>

<span data-ttu-id="f9cd7-142">Obslužná rutina ověřování:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-142">An authentication handler:</span></span>

* <span data-ttu-id="f9cd7-143">Je typ, který implementuje chování schématu.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="f9cd7-144">Je odvozen z <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> nebo <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="f9cd7-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="f9cd7-145">Má primární zodpovědnost za ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="f9cd7-146">Obslužné rutiny ověřování na základě konfigurace schématu ověřování a kontextu příchozího požadavku:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="f9cd7-147">Konstrukce <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objektů představujících identitu uživatele, je-li ověření úspěšné.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="f9cd7-148">Pokud je ověřování neúspěšné, vraťte se k žádnému výsledku nebo neúspěchu.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="f9cd7-149">Mít metody pro výzvu a nezakazující akce, když se uživatelé pokusí o přístup k prostředkům:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="f9cd7-150">Nemají oprávnění k přístupu (zakázat).</span><span class="sxs-lookup"><span data-stu-id="f9cd7-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="f9cd7-151">Pokud nejsou ověřeny (výzva).</span><span class="sxs-lookup"><span data-stu-id="f9cd7-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="f9cd7-152">Ověření</span><span class="sxs-lookup"><span data-stu-id="f9cd7-152">Authenticate</span></span>

<span data-ttu-id="f9cd7-153">Akce ověření schématu ověřování zodpovídá za sestavení identity uživatele na základě kontextu požadavku.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="f9cd7-154">Vrátí hodnotu <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> určující, zda bylo ověřování úspěšné, a pokud ano, identitu uživatele v ověřovacím lístku.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="f9cd7-155">Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="f9cd7-156">Mezi příklady ověřování patří:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-156">Authenticate examples include:</span></span>

* <span data-ttu-id="f9cd7-157">Schéma ověřování souborů cookie, které vytváří identitu uživatele z souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-157">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="f9cd7-158">Deserializace a ověření nosného tokenu JWT pro sestavení identity uživatele.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="f9cd7-159">Úloha</span><span class="sxs-lookup"><span data-stu-id="f9cd7-159">Challenge</span></span>

<span data-ttu-id="f9cd7-160">Ověření je vyvoláno autorizací, když neověřený uživatel požádá o koncový bod, který vyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="f9cd7-161">Výzva k ověření se vydá, například když anonymní uživatel požádá o omezený prostředek nebo klikne na odkaz pro přihlášení.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="f9cd7-162">Autorizace vyvolá výzvu pomocí zadaných schémat ověřování nebo výchozí hodnota, pokud není zadána.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="f9cd7-163">Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-163">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="f9cd7-164">Příklady výzev k ověřování zahrnují:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="f9cd7-165">Schéma ověřování souborů cookie přesměruje uživatele na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-165">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="f9cd7-166">Schéma nosiče JWT vracející 401 výsledek s `www-authenticate: bearer` hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="f9cd7-167">Akce výzvy by měla dát uživateli informace o tom, jaký ověřovací mechanismus použít pro přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="f9cd7-168">Zalomen</span><span class="sxs-lookup"><span data-stu-id="f9cd7-168">Forbid</span></span>

<span data-ttu-id="f9cd7-169">Akce zakázat autorizačnímu schématu je volána v případě, že se ověřený uživatel pokusí o přístup k prostředku, ke kterému nemají přístup.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="f9cd7-170">Viz třída <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-170">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="f9cd7-171">Mezi příklady ověřování, které patří:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="f9cd7-172">Schéma ověřování souborů cookie přesměruje uživatele na stránku oznamující, že přístup byl zakázán.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-172">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="f9cd7-173">Schéma nosiče JWT vracející výsledek 403.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="f9cd7-174">Vlastní schéma ověřování přesměrované na stránku, kde může uživatel požádat o přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="f9cd7-175">Akce zakázat může uživateli informovat:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="f9cd7-176">Jsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-176">They are authenticated.</span></span>
* <span data-ttu-id="f9cd7-177">Nemají oprávnění k přístupu k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="f9cd7-178">Rozdíly mezi výzvou a zakazují se zobrazují na následujících odkazech:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="f9cd7-179">[Pomocí obslužné rutiny provozního prostředku vyvolávat výzvu a nezakazují](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span><span class="sxs-lookup"><span data-stu-id="f9cd7-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="f9cd7-180">[Rozdíly mezi výzvou a zakazují](xref:security/authorization/secure-data#challenge).</span><span class="sxs-lookup"><span data-stu-id="f9cd7-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="f9cd7-181">Poskytovatelé ověřování na tenanta</span><span class="sxs-lookup"><span data-stu-id="f9cd7-181">Authentication providers per tenant</span></span>

<span data-ttu-id="f9cd7-182">Rozhraní ASP.NET Core Framework nemá integrované řešení pro ověřování s více klienty.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-182">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="f9cd7-183">I když je možné, že si je zákazníci zapsali pomocí integrovaných funkcí, doporučujeme zákazníkům, aby si pro tento účel vyhledali [sadu jader](https://www.orchardcore.net/) .</span><span class="sxs-lookup"><span data-stu-id="f9cd7-183">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="f9cd7-184">Základní sadu:</span><span class="sxs-lookup"><span data-stu-id="f9cd7-184">Orchard Core is:</span></span>

* <span data-ttu-id="f9cd7-185">Open Source modulární a víceklientské architektura aplikací sestavená s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-185">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="f9cd7-186">Systém pro správu obsahu (CMS) založený na daném rozhraní aplikace.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-186">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="f9cd7-187">Příklad zprostředkovatelů ověřování pro každého klienta najdete v tématu [základní](https://github.com/OrchardCMS/OrchardCore) zdroj sady.</span><span class="sxs-lookup"><span data-stu-id="f9cd7-187">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9cd7-188">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f9cd7-188">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
