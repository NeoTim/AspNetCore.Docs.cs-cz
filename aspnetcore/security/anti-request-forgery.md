---
title: Zabránit útokům na neCSRFelné žádosti mezi lokalitami (XSRF/) v ASP.NET Core
author: steve-smith
description: Zjistěte, jak zabránit útokům na webové aplikace, kde škodlivý web může ovlivnit interakci mezi prohlížečem klienta a aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/anti-request-forgery
ms.openlocfilehash: 5fbbb7a468a820ddad30bb4727a261fb01b4a23a
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212835"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="9b168-103">Zabránit útokům na neCSRFelné žádosti mezi lokalitami (XSRF/) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b168-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="9b168-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9b168-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9b168-105">Padělání požadavků napříč weby (označované také jako XSRF nebo CSRF) představuje útok proti aplikacím hostovaným na webu, kde může škodlivá webová aplikace ovlivnit interakci mezi klientským prohlížečem a webovou aplikací, která tento prohlížeč důvěřuje.</span><span class="sxs-lookup"><span data-stu-id="9b168-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="9b168-106">Tyto útoky jsou možné, protože webové prohlížeče odesílají některé typy ověřovacích tokenů automaticky pomocí všech požadavků na web.</span><span class="sxs-lookup"><span data-stu-id="9b168-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="9b168-107">Tato forma zneužití je také známá jako útok s *jedním kliknutím* nebo při *jízdě relace* , protože útok využívá dřív ověřenou relaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="9b168-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="9b168-108">Příklad útoku CSRF:</span><span class="sxs-lookup"><span data-stu-id="9b168-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="9b168-109">Uživatel se přihlásí `www.good-banking-site.com` pomocí ověřování pomocí formulářů.</span><span class="sxs-lookup"><span data-stu-id="9b168-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="9b168-110">Server ověří uživatele a vydá odpověď, která obsahuje soubor cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="9b168-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="9b168-111">Lokalita je zranitelná vůči útokům, protože důvěřuje všem žádostem, které obdrží, pomocí platného ověřovacího souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="9b168-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="9b168-112">Uživatel navštíví škodlivý web `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="9b168-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="9b168-113">Škodlivý web, `www.bad-crook-site.com` obsahuje formulář HTML podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="9b168-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="9b168-114">Všimněte si, že `action` příspěvky formuláře na ohrožený web, nikoli na škodlivý web.</span><span class="sxs-lookup"><span data-stu-id="9b168-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="9b168-115">Toto je součást CSRF (pro různé lokality).</span><span class="sxs-lookup"><span data-stu-id="9b168-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="9b168-116">Uživatel vybere tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="9b168-116">The user selects the submit button.</span></span> <span data-ttu-id="9b168-117">Prohlížeč vytvoří požadavek a automaticky přidá ověřovací soubor cookie pro požadovanou doménu `www.good-banking-site.com` .</span><span class="sxs-lookup"><span data-stu-id="9b168-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="9b168-118">Požadavek se spouští na `www.good-banking-site.com` serveru s kontextem ověřování uživatele a může provádět všechny akce, které má ověřený uživatel povoleno provádět.</span><span class="sxs-lookup"><span data-stu-id="9b168-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="9b168-119">Kromě scénáře, kdy uživatel vybere tlačítko pro odeslání formuláře, může škodlivý web:</span><span class="sxs-lookup"><span data-stu-id="9b168-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="9b168-120">Spustí skript, který automaticky odešle formulář.</span><span class="sxs-lookup"><span data-stu-id="9b168-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="9b168-121">Odeslat odeslání formuláře jako požadavek AJAX.</span><span class="sxs-lookup"><span data-stu-id="9b168-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="9b168-122">Skryjte formulář pomocí šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="9b168-122">Hide the form using CSS.</span></span>

<span data-ttu-id="9b168-123">Tyto alternativní scénáře nevyžadují žádnou akci ani vstup od uživatele kromě prvotního návštěvě škodlivého webu.</span><span class="sxs-lookup"><span data-stu-id="9b168-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="9b168-124">Použití protokolu HTTPS nebrání útoku CSRF.</span><span class="sxs-lookup"><span data-stu-id="9b168-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="9b168-125">Škodlivý web může poslat `https://www.good-banking-site.com/` požadavek stejně snadno, protože může poslat nezabezpečený požadavek.</span><span class="sxs-lookup"><span data-stu-id="9b168-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="9b168-126">Některé útoky cílí na koncové body, které reagují na požadavky GET. v takovém případě lze k provedení této akce použít značku obrázku.</span><span class="sxs-lookup"><span data-stu-id="9b168-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="9b168-127">Tato forma útoku je společná na webech fóra, které povolují image, ale blokují JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9b168-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="9b168-128">Aplikace, které mění stav u požadavků GET, kde se mění proměnné nebo prostředky, jsou zranitelné vůči škodlivým útokům.</span><span class="sxs-lookup"><span data-stu-id="9b168-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="9b168-129">**Požadavky GET, které mění stav, jsou nezabezpečené. Osvědčeným postupem je nikdy změnit stav u žádosti o získání.**</span><span class="sxs-lookup"><span data-stu-id="9b168-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="9b168-130">Útoky CSRF na webové aplikace, které používají soubory cookie k ověřování, jsou možné z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="9b168-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="9b168-131">Prohlížeče ukládají soubory cookie vydané webovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="9b168-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="9b168-132">Uložené soubory cookie obsahují soubory cookie relace pro ověřené uživatele.</span><span class="sxs-lookup"><span data-stu-id="9b168-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="9b168-133">Prohlížeče odesílají všechny soubory cookie přidružené k doméně webové aplikaci každý požadavek bez ohledu na to, jak se žádost o aplikaci vygenerovala v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="9b168-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="9b168-134">Nicméně útoky CSRF nejsou omezené na zneužití souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="9b168-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="9b168-135">Například základní ověřování a ověřování algoritmem Digest je také zranitelné.</span><span class="sxs-lookup"><span data-stu-id="9b168-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="9b168-136">Když se uživatel přihlásí pomocí základního ověřování nebo ověřování algoritmem Digest, prohlížeč automaticky pošle přihlašovací údaje, dokud relace &dagger; neskončí.</span><span class="sxs-lookup"><span data-stu-id="9b168-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="9b168-137">&dagger;V tomto kontextu *relace* odkazuje na relaci na straně klienta, během které je uživatel ověřený.</span><span class="sxs-lookup"><span data-stu-id="9b168-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="9b168-138">Nesouvisí s relacemi na straně serveru nebo [middlewarem ASP.NET Core relace](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="9b168-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="9b168-139">Uživatelé můžou pomocí preventivních opatření chránit před CSRFmi chybami:</span><span class="sxs-lookup"><span data-stu-id="9b168-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="9b168-140">Po dokončení používání webových aplikací se odhlaste.</span><span class="sxs-lookup"><span data-stu-id="9b168-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="9b168-141">Pravidelně vymažte soubory cookie prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9b168-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="9b168-142">Chyby zabezpečení CSRF ale představují zásadní problém s webovou aplikací, ne koncovým uživatelem.</span><span class="sxs-lookup"><span data-stu-id="9b168-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="9b168-143">Základy ověřování</span><span class="sxs-lookup"><span data-stu-id="9b168-143">Authentication fundamentals</span></span>

<span data-ttu-id="9b168-144">Ověřování pomocí souborů cookie je oblíbená forma ověřování.</span><span class="sxs-lookup"><span data-stu-id="9b168-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="9b168-145">Systémy ověřování založené na tokenech se zvětšují v oblíbenosti, zejména u aplikací s jednou stránkou (jednostránkové).</span><span class="sxs-lookup"><span data-stu-id="9b168-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="9b168-146">Ověřování na základě souborů cookie</span><span class="sxs-lookup"><span data-stu-id="9b168-146">Cookie-based authentication</span></span>

<span data-ttu-id="9b168-147">Když se uživatel ověřuje pomocí svého uživatelského jména a hesla, vydává token, který obsahuje ověřovací lístek, který se dá použít k ověřování a autorizaci.</span><span class="sxs-lookup"><span data-stu-id="9b168-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="9b168-148">Token je uložen jako soubor cookie, který doprovází každý požadavek, který klient provede.</span><span class="sxs-lookup"><span data-stu-id="9b168-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="9b168-149">Generování a ověření tohoto souboru cookie provádí middleware pro ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="9b168-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="9b168-150">[Middleware](xref:fundamentals/middleware/index) serializace objektu zabezpečení uživatele do šifrovaného souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="9b168-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="9b168-151">Při následném požadavku middleware ověří soubor cookie, znovu vytvoří objekt zabezpečení a přiřadí objektu zabezpečení vlastnost [uživatele](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="9b168-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="9b168-152">Ověřování na základě tokenů</span><span class="sxs-lookup"><span data-stu-id="9b168-152">Token-based authentication</span></span>

<span data-ttu-id="9b168-153">Když je uživatel ověřený, vydává token (nikoli token pro antipadělání).</span><span class="sxs-lookup"><span data-stu-id="9b168-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="9b168-154">Token obsahuje informace o uživateli ve formě [deklarací identity](/dotnet/framework/security/claims-based-identity-model) nebo tokenu odkazu, který ukazuje aplikaci na stav uživatele udržované v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9b168-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="9b168-155">Když se uživatel pokusí o přístup k prostředku, který vyžaduje ověření, token se do aplikace pošle pomocí další autorizační hlavičky ve formě nosných tokenů.</span><span class="sxs-lookup"><span data-stu-id="9b168-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="9b168-156">Tím se aplikace bez stavu nastaví.</span><span class="sxs-lookup"><span data-stu-id="9b168-156">This makes the app stateless.</span></span> <span data-ttu-id="9b168-157">V každém následném požadavku se token předává v žádosti o ověření na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="9b168-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="9b168-158">Tento token není *zašifrovaný*. je *kódovaný*.</span><span class="sxs-lookup"><span data-stu-id="9b168-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="9b168-159">Na serveru je token dekóduje pro přístup k jeho informacím.</span><span class="sxs-lookup"><span data-stu-id="9b168-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="9b168-160">Pokud chcete token odeslat na další požadavky, uložte token do místního úložiště prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9b168-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="9b168-161">V případě, že je token uložený v místním úložišti v prohlížeči, nemusíte mít obavy o ohrožení zabezpečení CSRF.</span><span class="sxs-lookup"><span data-stu-id="9b168-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="9b168-162">CSRF je problém, pokud je token uložen v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="9b168-162">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="9b168-163">Další informace najdete v ukázce kódu, který problém GitHubu [přidá dva soubory cookie](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="9b168-163">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="9b168-164">Více aplikací hostovaných v jedné doméně</span><span class="sxs-lookup"><span data-stu-id="9b168-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="9b168-165">Sdílená hostující prostředí jsou zranitelná proti zneužití relace, přihlašování CSRF a dalším útokům.</span><span class="sxs-lookup"><span data-stu-id="9b168-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="9b168-166">I `example1.contoso.net` Když `example2.contoso.net` jsou a jsou různí hostitelé, existuje implicitní vztah důvěryhodnosti mezi hostiteli v `*.contoso.net` doméně.</span><span class="sxs-lookup"><span data-stu-id="9b168-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="9b168-167">Tento implicitní vztah důvěryhodnosti umožňuje potenciálním nedůvěryhodným hostitelům ovlivňovat soubory cookie ostatních souborů (zásady stejného původu, které řídí požadavky AJAX, nemusí nutně platit pro soubory cookie HTTP).</span><span class="sxs-lookup"><span data-stu-id="9b168-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="9b168-168">Útoky, které využívají důvěryhodné soubory cookie mezi aplikacemi hostovanými ve stejné doméně, je možné zabránit nesdílením domén.</span><span class="sxs-lookup"><span data-stu-id="9b168-168">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="9b168-169">Pokud je každá aplikace hostována v její vlastní doméně, nebudete mít k zneužití žádný implicitní vztah důvěryhodnosti souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="9b168-169">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="9b168-170">ASP.NET Core konfigurace antipadělání</span><span class="sxs-lookup"><span data-stu-id="9b168-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="9b168-171">ASP.NET Core implementuje ochranu proti padělání pomocí [ASP.NET Core ochrany dat](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="9b168-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="9b168-172">Zásobník ochrany dat musí být nakonfigurovaný tak, aby fungoval v serverové farmě.</span><span class="sxs-lookup"><span data-stu-id="9b168-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="9b168-173">Další informace najdete v tématu [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="9b168-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9b168-174">V případě, že je v nástroji volána jedna z následujících rozhraní API, je middleware pro vkládání proti padělání přidána do kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9b168-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9b168-175">K kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) se přidá middleware proti padělání, když <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> se volá v`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="9b168-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="9b168-176">V ASP.NET Core 2,0 nebo novějším [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) vloží tokeny proti padělání do prvků formuláře HTML.</span><span class="sxs-lookup"><span data-stu-id="9b168-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="9b168-177">Následující kód v Razor souboru automaticky generuje tokeny proti padělání:</span><span class="sxs-lookup"><span data-stu-id="9b168-177">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="9b168-178">Podobně [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generuje tokeny proti padělání ve výchozím nastavení, pokud není metoda formuláře Get.</span><span class="sxs-lookup"><span data-stu-id="9b168-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="9b168-179">Automatické generování tokenů antipadělání pro prvky formuláře HTML se stane, když `<form>` značka obsahuje `method="post"` atribut a jedna z následujících možností je pravdivá:</span><span class="sxs-lookup"><span data-stu-id="9b168-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="9b168-180">Atribut Action je prázdný ( `action=""` ).</span><span class="sxs-lookup"><span data-stu-id="9b168-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="9b168-181">Atribut action není dodaný ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="9b168-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="9b168-182">Automatické generování tokenů antipadělání pro prvky formuláře HTML lze zakázat:</span><span class="sxs-lookup"><span data-stu-id="9b168-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="9b168-183">Explicitně zakažte tokeny proti padělání s `asp-antiforgery` atributem:</span><span class="sxs-lookup"><span data-stu-id="9b168-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="9b168-184">Na prvek formuláře se zarezervuje pomocník značek pomocí pomocníka značek [! symbol pro odhlášení](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="9b168-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="9b168-185">Odeberte `FormTagHelper` ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="9b168-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="9b168-186">V `FormTagHelper` zobrazení lze odebrat přidáním následující direktivy do Razor zobrazení:</span><span class="sxs-lookup"><span data-stu-id="9b168-186">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="9b168-187">[ Razor Stránky](xref:razor-pages/index) jsou automaticky chráněny z XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="9b168-187">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="9b168-188">Další informace naleznete v tématu [XSRF/CSRF a Razor Pages](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="9b168-188">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="9b168-189">Nejběžnějším přístupem k obraně před útoky CSRF je použití *vzoru tokenu synchronizátoru* (STP).</span><span class="sxs-lookup"><span data-stu-id="9b168-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="9b168-190">STP se používá v případě, že uživatel požádá o stránku s daty formuláře:</span><span class="sxs-lookup"><span data-stu-id="9b168-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="9b168-191">Server pošle klientovi token přidružený k identitě aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="9b168-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="9b168-192">Klient pošle zpátky token na server kvůli ověření.</span><span class="sxs-lookup"><span data-stu-id="9b168-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="9b168-193">Pokud server obdrží token, který se neshoduje s identitou ověřeného uživatele, požadavek se odmítne.</span><span class="sxs-lookup"><span data-stu-id="9b168-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="9b168-194">Token je jedinečný a nepředvídatelný.</span><span class="sxs-lookup"><span data-stu-id="9b168-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="9b168-195">Token lze také použít k zajištění správného pořadí posloupnosti požadavků (například zajištění pořadí požadavků: Stránka 1 > stránce 2 > stránce 3).</span><span class="sxs-lookup"><span data-stu-id="9b168-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="9b168-196">Všechny formuláře v ASP.NET Core šablony MVC a Razor stránky generují tokeny proti padělání.</span><span class="sxs-lookup"><span data-stu-id="9b168-196">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="9b168-197">Následující pár příkladů zobrazení generuje tokeny proti padělání:</span><span class="sxs-lookup"><span data-stu-id="9b168-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="9b168-198">Explicitně přidejte token proti padělání do `<form>` elementu bez použití pomocných rutin značek s pomocníkem jazyka HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :</span><span class="sxs-lookup"><span data-stu-id="9b168-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="9b168-199">V každém z předchozích případů ASP.NET Core přidá skryté pole formuláře podobné následujícímu:</span><span class="sxs-lookup"><span data-stu-id="9b168-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="9b168-200">ASP.NET Core obsahuje tři [filtry](xref:mvc/controllers/filters) pro práci s tokeny proti padělání:</span><span class="sxs-lookup"><span data-stu-id="9b168-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="9b168-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="9b168-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="9b168-202">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="9b168-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="9b168-203">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="9b168-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="9b168-204">Možnosti padělání</span><span class="sxs-lookup"><span data-stu-id="9b168-204">Antiforgery options</span></span>

<span data-ttu-id="9b168-205">Přizpůsobení [možností proti padělání](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9b168-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="9b168-206">&dagger;Vlastnosti antipadělání nastavte `Cookie` pomocí vlastností třídy [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .</span><span class="sxs-lookup"><span data-stu-id="9b168-206">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="9b168-207">Možnost</span><span class="sxs-lookup"><span data-stu-id="9b168-207">Option</span></span> | <span data-ttu-id="9b168-208">Popis</span><span class="sxs-lookup"><span data-stu-id="9b168-208">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="9b168-209">Soubor</span><span class="sxs-lookup"><span data-stu-id="9b168-209">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="9b168-210">Určuje nastavení používané k vytvoření souborů cookie antipadělání.</span><span class="sxs-lookup"><span data-stu-id="9b168-210">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="9b168-211">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="9b168-211">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="9b168-212">Název skrytého pole formuláře používaného systémem pro použití proti padělání pro vykreslování tokenů v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="9b168-212">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="9b168-213">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="9b168-213">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="9b168-214">Název záhlaví používaného systémem pro použití proti padělání</span><span class="sxs-lookup"><span data-stu-id="9b168-214">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="9b168-215">Pokud `null` systém považuje jenom formulářová data.</span><span class="sxs-lookup"><span data-stu-id="9b168-215">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="9b168-216">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="9b168-216">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="9b168-217">Určuje, zda se má potlačit generování `X-Frame-Options` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="9b168-217">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="9b168-218">Ve výchozím nastavení se záhlaví generuje s hodnotou "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="9b168-218">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="9b168-219">Výchozí hodnota je `false` .</span><span class="sxs-lookup"><span data-stu-id="9b168-219">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="9b168-220">Možnost</span><span class="sxs-lookup"><span data-stu-id="9b168-220">Option</span></span> | <span data-ttu-id="9b168-221">Popis</span><span class="sxs-lookup"><span data-stu-id="9b168-221">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="9b168-222">Soubor</span><span class="sxs-lookup"><span data-stu-id="9b168-222">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="9b168-223">Určuje nastavení používané k vytvoření souborů cookie antipadělání.</span><span class="sxs-lookup"><span data-stu-id="9b168-223">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="9b168-224">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="9b168-224">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="9b168-225">Doména souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="9b168-225">The domain of the cookie.</span></span> <span data-ttu-id="9b168-226">Výchozí hodnota je `null` .</span><span class="sxs-lookup"><span data-stu-id="9b168-226">Defaults to `null`.</span></span> <span data-ttu-id="9b168-227">Tato vlastnost je zastaralá a v budoucí verzi se odebere.</span><span class="sxs-lookup"><span data-stu-id="9b168-227">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="9b168-228">Doporučenou alternativou je soubor cookie. domain.</span><span class="sxs-lookup"><span data-stu-id="9b168-228">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="9b168-229">Vlastnost CookieName</span><span class="sxs-lookup"><span data-stu-id="9b168-229">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="9b168-230">Název souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="9b168-230">The name of the cookie.</span></span> <span data-ttu-id="9b168-231">Pokud není nastaven, systém vygeneruje jedinečný název začínající na [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. antipadělání. ").</span><span class="sxs-lookup"><span data-stu-id="9b168-231">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="9b168-232">Tato vlastnost je zastaralá a v budoucí verzi se odebere.</span><span class="sxs-lookup"><span data-stu-id="9b168-232">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="9b168-233">Doporučená alternativa je Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="9b168-233">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="9b168-234">CookiePath</span><span class="sxs-lookup"><span data-stu-id="9b168-234">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="9b168-235">Cesta nastavená na souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="9b168-235">The path set on the cookie.</span></span> <span data-ttu-id="9b168-236">Tato vlastnost je zastaralá a v budoucí verzi se odebere.</span><span class="sxs-lookup"><span data-stu-id="9b168-236">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="9b168-237">Doporučená alternativa je soubor cookie. Path.</span><span class="sxs-lookup"><span data-stu-id="9b168-237">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="9b168-238">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="9b168-238">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="9b168-239">Název skrytého pole formuláře používaného systémem pro použití proti padělání pro vykreslování tokenů v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="9b168-239">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="9b168-240">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="9b168-240">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="9b168-241">Název záhlaví používaného systémem pro použití proti padělání</span><span class="sxs-lookup"><span data-stu-id="9b168-241">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="9b168-242">Pokud `null` systém považuje jenom formulářová data.</span><span class="sxs-lookup"><span data-stu-id="9b168-242">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="9b168-243">Vlastnost requireSSL</span><span class="sxs-lookup"><span data-stu-id="9b168-243">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="9b168-244">Určuje, zda je protokol HTTPS vyžadován systémem pro vypozměňování.</span><span class="sxs-lookup"><span data-stu-id="9b168-244">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="9b168-245">`true`V případě selhání neproběhne požadavek bez protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9b168-245">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="9b168-246">Výchozí hodnota je `false` .</span><span class="sxs-lookup"><span data-stu-id="9b168-246">Defaults to `false`.</span></span> <span data-ttu-id="9b168-247">Tato vlastnost je zastaralá a v budoucí verzi se odebere.</span><span class="sxs-lookup"><span data-stu-id="9b168-247">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="9b168-248">Doporučenou možností je nastavit soubor cookie. SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="9b168-248">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="9b168-249">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="9b168-249">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="9b168-250">Určuje, zda se má potlačit generování `X-Frame-Options` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="9b168-250">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="9b168-251">Ve výchozím nastavení se záhlaví generuje s hodnotou "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="9b168-251">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="9b168-252">Výchozí hodnota je `false` .</span><span class="sxs-lookup"><span data-stu-id="9b168-252">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="9b168-253">Další informace najdete v tématu [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="9b168-253">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="9b168-254">Konfigurace funkcí antipadělání pomocí IAntiforgery</span><span class="sxs-lookup"><span data-stu-id="9b168-254">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="9b168-255">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) poskytuje rozhraní API pro konfiguraci funkcí pro antipadělání.</span><span class="sxs-lookup"><span data-stu-id="9b168-255">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="9b168-256">`IAntiforgery`lze požadovat v `Configure` metodě `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="9b168-256">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="9b168-257">Následující příklad používá middleware z domovské stránky aplikace k vygenerování tokenu antipadělání a jeho odeslání v odpovědi jako soubor cookie (pomocí výchozí hodnoty úhlů pojmenování popsaných dále v tomto tématu):</span><span class="sxs-lookup"><span data-stu-id="9b168-257">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="9b168-258">Vyžadovat ověření platnosti proti padělání</span><span class="sxs-lookup"><span data-stu-id="9b168-258">Require antiforgery validation</span></span>

<span data-ttu-id="9b168-259">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) je filtr akcí, který lze použít na jednotlivé akce, kontrolora nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="9b168-259">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="9b168-260">Požadavky provedené na akce, které mají tento filtr použit, jsou blokovány, pokud požadavek neobsahuje platný token proti padělání.</span><span class="sxs-lookup"><span data-stu-id="9b168-260">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="9b168-261">`ValidateAntiForgeryToken`Atribut vyžaduje token pro požadavky na metody akce, které označuje, včetně požadavků HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9b168-261">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="9b168-262">Pokud `ValidateAntiForgeryToken` je atribut použit v rámci řadičů aplikace, lze jej přepsat `IgnoreAntiforgeryToken` atributem.</span><span class="sxs-lookup"><span data-stu-id="9b168-262">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="9b168-263">ASP.NET Core nepodporuje přidávání tokenů antipadělání pro automatické získávání požadavků.</span><span class="sxs-lookup"><span data-stu-id="9b168-263">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="9b168-264">Automaticky ověřovat tokeny antipadělání pro nezabezpečené metody HTTP</span><span class="sxs-lookup"><span data-stu-id="9b168-264">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="9b168-265">Aplikace ASP.NET Core negenerují tokeny antipadělání pro bezpečné metody protokolu HTTP (GET, HEAD, OPTIONS a TRACE).</span><span class="sxs-lookup"><span data-stu-id="9b168-265">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="9b168-266">Místo toho, abyste `ValidateAntiForgeryToken` atribut používali a pak ho přepsali `IgnoreAntiforgeryToken` atributy, je možné použít atribut [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) .</span><span class="sxs-lookup"><span data-stu-id="9b168-266">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="9b168-267">Tento atribut funguje identicky s `ValidateAntiForgeryToken` atributem s tím rozdílem, že nevyžaduje tokeny pro požadavky vytvořené pomocí následujících metod http:</span><span class="sxs-lookup"><span data-stu-id="9b168-267">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="9b168-268">GET</span><span class="sxs-lookup"><span data-stu-id="9b168-268">GET</span></span>
* <span data-ttu-id="9b168-269">HEAD</span><span class="sxs-lookup"><span data-stu-id="9b168-269">HEAD</span></span>
* <span data-ttu-id="9b168-270">NASTAVENÍ</span><span class="sxs-lookup"><span data-stu-id="9b168-270">OPTIONS</span></span>
* <span data-ttu-id="9b168-271">TRACE</span><span class="sxs-lookup"><span data-stu-id="9b168-271">TRACE</span></span>

<span data-ttu-id="9b168-272">`AutoValidateAntiforgeryToken`Pro jiné scénáře než rozhraní API doporučujeme používat široké možnosti.</span><span class="sxs-lookup"><span data-stu-id="9b168-272">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="9b168-273">Tím je zajištěno, že akce příspěvků jsou ve výchozím nastavení chráněny.</span><span class="sxs-lookup"><span data-stu-id="9b168-273">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="9b168-274">Alternativou je ignorovat tokeny proti padělání ve výchozím nastavení, pokud `ValidateAntiForgeryToken` se nepoužije na jednotlivé metody akcí.</span><span class="sxs-lookup"><span data-stu-id="9b168-274">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="9b168-275">V tomto scénáři je pravděpodobnější, že metoda POST akce bude ponechána bez ochrany omylem, takže aplikace bude zranitelná vůči útokům CSRF.</span><span class="sxs-lookup"><span data-stu-id="9b168-275">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="9b168-276">Všechny příspěvky by měly odeslat token proti padělání.</span><span class="sxs-lookup"><span data-stu-id="9b168-276">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="9b168-277">Rozhraní API nemají automatický mechanismus pro odeslání části tokenu bez souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="9b168-277">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="9b168-278">Implementace pravděpodobně závisí na implementaci klientského kódu.</span><span class="sxs-lookup"><span data-stu-id="9b168-278">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="9b168-279">Níže jsou uvedeny některé příklady:</span><span class="sxs-lookup"><span data-stu-id="9b168-279">Some examples are shown below:</span></span>

<span data-ttu-id="9b168-280">Příklad na úrovni třídy:</span><span class="sxs-lookup"><span data-stu-id="9b168-280">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="9b168-281">Globální příklad:</span><span class="sxs-lookup"><span data-stu-id="9b168-281">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9b168-282">orgány. AddMvc (možnosti => možnosti. Filters. Add (New AutoValidateAntiforgeryTokenAttribute ());</span><span class="sxs-lookup"><span data-stu-id="9b168-282">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="9b168-283">Přepsat globální atributy nebo atributy antipadělání řadiče</span><span class="sxs-lookup"><span data-stu-id="9b168-283">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="9b168-284">Filtr [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) se používá k odstranění potřeby tokenu antipadělání pro danou akci (nebo kontroler).</span><span class="sxs-lookup"><span data-stu-id="9b168-284">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="9b168-285">Při použití tohoto filtru se přepíší `ValidateAntiForgeryToken` a `AutoValidateAntiforgeryToken` filtry zadané na vyšší úrovni (globálně nebo na řadiči).</span><span class="sxs-lookup"><span data-stu-id="9b168-285">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="9b168-286">Aktualizovat tokeny po ověření</span><span class="sxs-lookup"><span data-stu-id="9b168-286">Refresh tokens after authentication</span></span>

<span data-ttu-id="9b168-287">Tokeny by se měly aktualizovat po ověření uživatele tak, že se uživatel přesměruje na stránku zobrazení nebo Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="9b168-287">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="9b168-288">JavaScript, AJAX a jednostránkové</span><span class="sxs-lookup"><span data-stu-id="9b168-288">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="9b168-289">V tradičních aplikacích založených na HTML jsou tokeny proti padělání předány serveru pomocí skrytých polí formuláře.</span><span class="sxs-lookup"><span data-stu-id="9b168-289">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="9b168-290">V moderních aplikacích založených na jazyce JavaScript a jednostránkové se mnoho požadavků provádí programově.</span><span class="sxs-lookup"><span data-stu-id="9b168-290">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="9b168-291">Tyto požadavky AJAX můžou k odeslání tokenu použít jiné techniky (například hlavičky požadavků nebo soubory cookie).</span><span class="sxs-lookup"><span data-stu-id="9b168-291">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="9b168-292">Pokud se soubory cookie používají k ukládání ověřovacích tokenů a k ověřování požadavků na rozhraní API na serveru, CSRF je potenciální problém.</span><span class="sxs-lookup"><span data-stu-id="9b168-292">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="9b168-293">Pokud se k uložení tokenu používá místní úložiště, může dojít k zmírnění ohrožení zabezpečení CSRF, protože hodnoty z místního úložiště nejsou automaticky odesílány na server se všemi požadavky.</span><span class="sxs-lookup"><span data-stu-id="9b168-293">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="9b168-294">Proto se použití místního úložiště uloží do klienta tokenu antipadělání a odeslání tokenu jako hlavičky požadavku je doporučeným přístupem.</span><span class="sxs-lookup"><span data-stu-id="9b168-294">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="9b168-295">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b168-295">JavaScript</span></span>

<span data-ttu-id="9b168-296">Pomocí JavaScriptu se zobrazeními se token dá vytvořit pomocí služby v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="9b168-296">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="9b168-297">Do zobrazení a volání [GetAndStoreTokensu](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)založit službu [Microsoft. AspNetCore.. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) :</span><span class="sxs-lookup"><span data-stu-id="9b168-297">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="9b168-298">Tento přístup eliminuje nutnost nabývat přímo s nastavením souborů cookie ze serveru nebo jejich čtením z klienta.</span><span class="sxs-lookup"><span data-stu-id="9b168-298">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="9b168-299">Předchozí příklad používá JavaScript pro čtení hodnoty skrytého pole pro hlavičku příspěvku AJAX.</span><span class="sxs-lookup"><span data-stu-id="9b168-299">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="9b168-300">JavaScript může také přistupovat k tokenům v souborech cookie a použít obsah souboru cookie k vytvoření hlavičky s hodnotou tokenu.</span><span class="sxs-lookup"><span data-stu-id="9b168-300">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="9b168-301">Za předpokladu, že skript požaduje odeslání tokenu v záhlaví s názvem `X-CSRF-TOKEN` , nakonfigurujte službu proti padělání tak, aby hledala `X-CSRF-TOKEN` hlavičku:</span><span class="sxs-lookup"><span data-stu-id="9b168-301">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="9b168-302">V následujícím příkladu se pomocí JavaScriptu provede požadavek AJAX s odpovídající hlavičkou:</span><span class="sxs-lookup"><span data-stu-id="9b168-302">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="9b168-303">AngularJS</span><span class="sxs-lookup"><span data-stu-id="9b168-303">AngularJS</span></span>

<span data-ttu-id="9b168-304">AngularJS používá konvenci pro řešení CSRF.</span><span class="sxs-lookup"><span data-stu-id="9b168-304">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="9b168-305">Pokud server odešle soubor cookie s názvem `XSRF-TOKEN` , `$http` Služba AngularJS přidá hodnotu cookie do hlavičky při odeslání požadavku na server.</span><span class="sxs-lookup"><span data-stu-id="9b168-305">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="9b168-306">Tento proces je automatický.</span><span class="sxs-lookup"><span data-stu-id="9b168-306">This process is automatic.</span></span> <span data-ttu-id="9b168-307">Záhlaví není v klientovi nutné nastavit explicitně.</span><span class="sxs-lookup"><span data-stu-id="9b168-307">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="9b168-308">Název hlavičky je `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="9b168-308">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="9b168-309">Server by měl detekovat tuto hlavičku a ověřit její obsah.</span><span class="sxs-lookup"><span data-stu-id="9b168-309">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="9b168-310">ASP.NET Core rozhraní API pro práci s touto konvencí ve vašem spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="9b168-310">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="9b168-311">Nakonfigurujte svou aplikaci tak, aby poskytovala token ve volání cookie `XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="9b168-311">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="9b168-312">Nakonfigurujte službu proti padělání, aby hledala hlavičku s názvem `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="9b168-312">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="9b168-313">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9b168-313">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="9b168-314">Rozšiřování antipadělání</span><span class="sxs-lookup"><span data-stu-id="9b168-314">Extend antiforgery</span></span>

<span data-ttu-id="9b168-315">Typ [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) umožňuje vývojářům roztáhnout chování systému anti-CSRF s kulatými Trip dalšími daty v každém tokenu.</span><span class="sxs-lookup"><span data-stu-id="9b168-315">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="9b168-316">Metoda [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) je volána při každém vygenerování tokenu pole a návratová hodnota je vložena do vygenerovaného tokenu.</span><span class="sxs-lookup"><span data-stu-id="9b168-316">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="9b168-317">Implementátor by mohl vrátit časové razítko, hodnotu NONCE nebo jakoukoli jinou hodnotu a pak zavolat [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) , aby ověřil tato data při ověření tokenu.</span><span class="sxs-lookup"><span data-stu-id="9b168-317">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="9b168-318">Uživatelské jméno klienta je již vloženo do vygenerovaných tokenů, takže není nutné tyto informace zahrnout.</span><span class="sxs-lookup"><span data-stu-id="9b168-318">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="9b168-319">Pokud token zahrnuje doplňková data, ale není `IAntiForgeryAdditionalDataProvider` nakonfigurovaná, doplňují se data neověřují.</span><span class="sxs-lookup"><span data-stu-id="9b168-319">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b168-320">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="9b168-320">Additional resources</span></span>

* <span data-ttu-id="9b168-321">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) v [otevřeném projektu webové aplikace zabezpečení](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="9b168-321">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
