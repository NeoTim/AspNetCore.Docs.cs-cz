---
title: Útokům zabránilo webů žádosti padělání (XSRF/CSRF) v ASP.NET Core
author: steve-smith
description: Zjistěte, jak zabránit v napadení webových aplikací, kde škodlivý webu mohou mít vliv na interakci mezi prohlížeče klienta a aplikace.
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: 6e140717834b901e12ef7863fd07b983b0c55107
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667658"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="6d1e3-103">Útokům zabránilo webů žádosti padělání (XSRF/CSRF) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d1e3-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="6d1e3-104">Podle [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6d1e3-104">By [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6d1e3-105">Padělání žádosti více webů (označované také jako XSRF nebo CSRF, vyslovováno *viz procházení*) je útok na hostované webové aplikace, které škodlivý webové aplikace mohou mít vliv na interakci mezi klientského prohlížeče a webové aplikace, které důvěřuje, který prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-105">Cross-site request forgery (also known as XSRF or CSRF, pronounced *see-surf*) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="6d1e3-106">Tyto útoky nejsou možné, protože webových prohlížečů odesílat některé typy ověřování tokenů automaticky při každé žádosti na web.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="6d1e3-107">Tato forma před zneužitím se taky říká *jedním kliknutím útoku* nebo *relace záleželo* protože využívá výhod platformy útoku uživatele ověřený relace.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="6d1e3-108">Příklad útok CSRF:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="6d1e3-109">Uživatel se přihlásí do `www.good-banking-site.com` ověřování pomocí formulářů.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="6d1e3-110">Server ověřuje uživatele a vydá odpověď, která obsahuje soubor cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="6d1e3-111">Web je snadno napadnutelný vzhledem k tomu, že důvěřuje všechny požadavky, které přijímá s platný ověřovací soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="6d1e3-112">Uživatel navštíví škodlivým webům `www.bad-crook-site.com`.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="6d1e3-113">Škodlivé weby `www.bad-crook-site.com`, obsahuje formulář HTML, který je podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="6d1e3-114">Všimněte si, že formulář `action` příspěvků na webu zranitelné, nikoli na škodlivé weby.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="6d1e3-115">Toto je část "webů" CSRF.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="6d1e3-116">Uživatel vybere tlačítko pro odeslání.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-116">The user selects the submit button.</span></span> <span data-ttu-id="6d1e3-117">Prohlížeč provede požadavek a automaticky zahrne ověřovacího souboru cookie pro požadovaná doména `www.good-banking-site.com`.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="6d1e3-118">Požadavek poběží `www.good-banking-site.com` server s kontext ověřování uživatele a mohou provádět všechny akce, které ověřený uživatel smí provádět.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="6d1e3-119">Kromě tohoto scénáře, které si uživatel vybere tlačítko k odeslání formuláře může škodlivé weby:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="6d1e3-120">Spusťte skript, který automaticky odešle formulář.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="6d1e3-121">Odeslání formuláře odešlete jako požadavek AJAX.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="6d1e3-122">Skryjte formuláři pomocí šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-122">Hide the form using CSS.</span></span>

<span data-ttu-id="6d1e3-123">Tyto alternativní scénáře nevyžadují žádné akce ani vstup od uživatele, než je zpočátku navštívit škodlivé weby.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="6d1e3-124">Pomocí protokolu HTTPS není zabránění útoku CSRF.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="6d1e3-125">Může odesílat škodlivé weby `https://www.good-banking-site.com/` stejně snadno jako může odeslat požadavek nezabezpečeného požadavku.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="6d1e3-126">Některé útoky cílové koncové body, které reagují na požadavky GET, značka obrázku v takovém případě lze použít k provedení akce.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="6d1e3-127">Tato forma útoku je běžné v fór, které umožňují imagí, ale zablokuje JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="6d1e3-128">Aplikace, které změní stav pro požadavky GET, kde jsou změnit proměnné nebo prostředky, jsou ohrožená útoky se zlými úmysly.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="6d1e3-129">**Jsou nezabezpečené požadavky GET, které mění stav. Osvědčeným postupem je nikdy nezmění stav na požadavek GET.**</span><span class="sxs-lookup"><span data-stu-id="6d1e3-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="6d1e3-130">Útokům CSRF jsou možné proti webové aplikace, které používají soubory cookie pro ověřování, protože:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="6d1e3-131">Prohlížeče ukládání souborů cookie vydala webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="6d1e3-132">Uložené soubory cookie obsahují soubory cookie relace pro ověřené uživatele.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="6d1e3-133">Prohlížeče odesílají, že všechny soubory cookie přidružené k doméně do webové aplikace všechny požadavky bez ohledu na to, jak vygenerování této žádosti do aplikace v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="6d1e3-134">Však nejsou omezeni útokům CSRF k využívání soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="6d1e3-135">Například jsou Basic a Digest ověřování také zranitelné.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="6d1e3-136">Jakmile se uživatel přihlásí pomocí ověřování základní nebo Digest, prohlížeč automaticky odesílá přihlašovací údaje do relace&dagger; skončí.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="6d1e3-137">&dagger;V tomto kontextu *relace* odkazuje na relace na straně klienta, během které je uživatel ověřený.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="6d1e3-138">Je nezávislé na relace na straně serveru nebo [Middleware relace ASP.NET Core](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="6d1e3-139">Uživatelé můžou pomáhalo chránit před ohrožení zabezpečení CSRF provedením opatření:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="6d1e3-140">Přihlášení z webové aplikace po dokončení jejich používání.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="6d1e3-141">Vymazat soubory cookie v prohlížeči pravidelně.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="6d1e3-142">Ale CSRF ohrožení zabezpečení jsou v podstatě potíže s webovou aplikací, ne koncový uživatel.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="6d1e3-143">Základy ověřování</span><span class="sxs-lookup"><span data-stu-id="6d1e3-143">Authentication fundamentals</span></span>

<span data-ttu-id="6d1e3-144">Ověřování na základě souborů cookie je Oblíbené formu ověřování.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="6d1e3-145">Ověřování pomocí tokenu systémy jsou stále se rozšiřující stále oblíbenější, zejména pro jednostránkové aplikace (SPA).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="6d1e3-146">Ověřování na základě souborů cookie</span><span class="sxs-lookup"><span data-stu-id="6d1e3-146">Cookie-based authentication</span></span>

<span data-ttu-id="6d1e3-147">Když se uživatel přihlásí pomocí svého uživatelského jména a hesla, se už vystaví token, obsahující lístek pro ověřování, který slouží k ověřování a autorizaci.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="6d1e3-148">Token, který je uložen jako soubor cookie, který doprovází každého požadavku klienta.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="6d1e3-149">Generování a ověřování tento soubor cookie se provádí pomocí Middleware ověřování souborů Cookie.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="6d1e3-150">[Middleware](xref:fundamentals/middleware/index) serializuje hlavní název uživatele v podobě zašifrovaného souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="6d1e3-151">Na následné žádosti, middleware ověří souboru cookie, znovu vytvoří objekt zabezpečení a přiřadí instančního objektu pro [uživatele](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) vlastnost [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="6d1e3-152">Ověřování založené na tokenech</span><span class="sxs-lookup"><span data-stu-id="6d1e3-152">Token-based authentication</span></span>

<span data-ttu-id="6d1e3-153">Při ověření uživatele, že vystaví token (ne antiforgery token).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="6d1e3-154">Token, který obsahuje informace o uživateli ve formě [deklarace identity](/dotnet/framework/security/claims-based-identity-model) nebo odkaz na token, který odkazuje aplikaci na stav uživatele v aplikaci udržuje.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="6d1e3-155">Když se uživatel pokusí o přístup k prostředku, které vyžadují ověřování, token je odeslána do aplikace pomocí se další autorizační hlavička v podobě nosný token.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="6d1e3-156">Díky tomu aplikace bezstavové.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-156">This makes the app stateless.</span></span> <span data-ttu-id="6d1e3-157">V každé následné žádosti o token předaný požadavek pro ověřování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="6d1e3-158">Tento token není *šifrované*; má *kódovaný*.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="6d1e3-159">Na serveru je dekódováno token pro přístup k jeho informace.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="6d1e3-160">Odeslat token na následné žádosti, ukládání tokenu v místním úložišti v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="6d1e3-161">Nemusíte mít obavy o CSRF ohrožení zabezpečení, pokud token, který je uložený v místním úložišti v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="6d1e3-162">CSRF je důležité, pokud token je uložen v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-162">CSRF is a concern when the token is stored in a cookie.</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="6d1e3-163">Více aplikací hostovaných v jedné doméně</span><span class="sxs-lookup"><span data-stu-id="6d1e3-163">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="6d1e3-164">Sdílená hostitelská prostředí jsou citlivé na zneužití relace přihlášení CSRF a dalších útoků.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-164">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="6d1e3-165">I když `example1.contoso.net` a `example2.contoso.net` jsou různých hostitelích, existuje implicitní důvěryhodnost vztah mezi hostiteli v rámci `*.contoso.net` domény.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-165">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="6d1e3-166">Tento vztah důvěryhodnosti implicitní umožňuje potenciálně nedůvěryhodní hostitelé ovlivnit druhé strany soubory cookie (stejného zdroje zásad, kterými se řídí odesílání požadavků AJAX není nemusí nezbytně vztahovat k soubory cookie protokolu HTTP).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-166">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="6d1e3-167">Není sdílením domény jde zakázat útoků, které zneužívají důvěryhodných souborů cookie mezi aplikacemi, které jsou hostované ve stejné doméně.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-167">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="6d1e3-168">Při každé aplikaci, která je hostovaná ve vlastní doméně, neexistuje žádný vztah důvěry implicitní souboru cookie zneužít ohrožená místa.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-168">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="6d1e3-169">Konfigurace antiforgery ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d1e3-169">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="6d1e3-170">ASP.NET Core implementuje antiforgery pomocí [ochranu dat ASP.NET Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-170">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="6d1e3-171">Zásobník ochrany dat musí být nakonfigurováno pro práci v serverové farmě.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-171">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="6d1e3-172">Zobrazit [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview) Další informace.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-172">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

<span data-ttu-id="6d1e3-173">V technologii ASP.NET Core 2.0 nebo novější [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) vkládá antiforgery tokeny do prvků formuláře HTML.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-173">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="6d1e3-174">Následující kód do souboru Razor automaticky generuje antiforgery tokeny:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-174">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="6d1e3-175">Wijsova, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generuje antiforgery tokeny ve výchozím nastavení, pokud není formuláře metoda GET.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-175">Similarily, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="6d1e3-176">Automatické generování tokenů antiforgery elementů formuláře HTML se stane, když `<form>` značka obsahuje `method="post"` platí atribut a jednu z následujících:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-176">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

  * <span data-ttu-id="6d1e3-177">Atribut akce je prázdná (`action=""`).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-177">The action attribute is empty (`action=""`).</span></span>
  * <span data-ttu-id="6d1e3-178">Není zadán atribut akce (`<form method="post">`).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-178">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="6d1e3-179">Automatické generování tokenů antiforgery elementů formuláře HTML se dají zakázat:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-179">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="6d1e3-180">Explicitně zakázat antiforgery tokeny se `asp-antiforgery` atribut:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-180">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="6d1e3-181">Element formuláře je se rozhodli nesouhlas pomocných rutin značek s využitím pomocné rutiny značky [! symbol Odhlásit](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="6d1e3-181">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="6d1e3-182">Odeberte `FormTagHelper` ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-182">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="6d1e3-183">`FormTagHelper` Je možné odebrat ze zobrazení tak, že přidáte následující direktiva zobrazení Razor:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-183">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="6d1e3-184">[Stránky Razor](xref:razor-pages/index) automaticky chráněná před XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-184">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="6d1e3-185">Další informace najdete v tématu [XSRF/CSRF a Razor Pages](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-185">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="6d1e3-186">Nejběžnější přístup k obrana proti útokům CSRF je použít *Token vzor synchronizátor* (STP).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-186">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="6d1e3-187">STP se používá, když uživatel požádá o stránku s formuláři data:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-187">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="6d1e3-188">Server odešle token přidružená k identitě aktuálního uživatele na klienta.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-188">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="6d1e3-189">Klient odešle zpět token na server pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-189">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="6d1e3-190">Pokud server přijme token, který neodpovídá identity ověřeného uživatele, žádost se zamítá.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-190">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="6d1e3-191">Token, který je jedinečný a nepředvídatelné.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-191">The token is unique and unpredictable.</span></span> <span data-ttu-id="6d1e3-192">Token, který je také možné zajistit správné sekvenční sérii požadavků (například pro zajištění posloupnost požadavku: stránka 1 &ndash; stránka 2 &ndash; stránce 3).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-192">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 &ndash; page 2 &ndash; page 3).</span></span> <span data-ttu-id="6d1e3-193">Všechny formuláře v ASP.NET Core MVC a Razor Pages šablony generování antiforgery tokenů.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-193">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="6d1e3-194">Následující pár příkladů zobrazení generování antiforgery tokenů:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-194">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="6d1e3-195">Explicitně přidat antiforgery token `<form>` element bez použití pomocných rutin značek s pomocné rutiny HTML [ @Html.AntiForgeryToken ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span><span class="sxs-lookup"><span data-stu-id="6d1e3-195">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [@Html.AntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="6d1e3-196">ASP.NET Core v každém z předchozích případech přidá skryté pole formuláře podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-196">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="6d1e3-197">ASP.NET Core obsahuje tři [filtry](xref:mvc/controllers/filters) pro práci s antiforgery tokeny:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-197">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="6d1e3-198">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="6d1e3-198">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="6d1e3-199">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="6d1e3-199">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="6d1e3-200">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="6d1e3-200">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="6d1e3-201">Antiforgery možnosti</span><span class="sxs-lookup"><span data-stu-id="6d1e3-201">Antiforgery options</span></span>

<span data-ttu-id="6d1e3-202">Přizpůsobení [antiforgery možnosti](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-202">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="6d1e3-203">&dagger;Nastavte antiforgery `Cookie` vlastností pomocí vlastnosti [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) třídy.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-203">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="6d1e3-204">Možnost</span><span class="sxs-lookup"><span data-stu-id="6d1e3-204">Option</span></span> | <span data-ttu-id="6d1e3-205">Popis</span><span class="sxs-lookup"><span data-stu-id="6d1e3-205">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="6d1e3-206">Soubor cookie</span><span class="sxs-lookup"><span data-stu-id="6d1e3-206">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="6d1e3-207">Určuje nastavení použité k vytvoření antiforgery soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-207">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="6d1e3-208">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="6d1e3-208">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="6d1e3-209">Název skryté pole formuláře antiforgery systému použije k vykreslení antiforgery tokeny v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-209">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="6d1e3-210">HeaderName</span><span class="sxs-lookup"><span data-stu-id="6d1e3-210">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="6d1e3-211">Název hlavičky používá antiforgery systémem.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-211">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="6d1e3-212">Pokud `null`, systém bere v úvahu pouze data formuláře.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-212">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="6d1e3-213">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="6d1e3-213">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="6d1e3-214">Určuje, jestli se má potlačit generování `X-Frame-Options` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-214">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="6d1e3-215">Ve výchozím nastavení je záhlaví generovány s hodnotou "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="6d1e3-215">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="6d1e3-216">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-216">Defaults to `false`.</span></span> |

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

| <span data-ttu-id="6d1e3-217">Možnost</span><span class="sxs-lookup"><span data-stu-id="6d1e3-217">Option</span></span> | <span data-ttu-id="6d1e3-218">Popis</span><span class="sxs-lookup"><span data-stu-id="6d1e3-218">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="6d1e3-219">Soubor cookie</span><span class="sxs-lookup"><span data-stu-id="6d1e3-219">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="6d1e3-220">Určuje nastavení použité k vytvoření antiforgery soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-220">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="6d1e3-221">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="6d1e3-221">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="6d1e3-222">Doména souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-222">The domain of the cookie.</span></span> <span data-ttu-id="6d1e3-223">Výchozí hodnota je `null`.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-223">Defaults to `null`.</span></span> <span data-ttu-id="6d1e3-224">Tato vlastnost je zastaralá a bude v budoucí verzi odebrána.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-224">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="6d1e3-225">Doporučenou alternativou je Cookie.Domain.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-225">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="6d1e3-226">CookieName</span><span class="sxs-lookup"><span data-stu-id="6d1e3-226">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="6d1e3-227">Název souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-227">The name of the cookie.</span></span> <span data-ttu-id="6d1e3-228">Pokud není nastavený, systém generuje jedinečný název začíná [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore.Antiforgery.").</span><span class="sxs-lookup"><span data-stu-id="6d1e3-228">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="6d1e3-229">Tato vlastnost je zastaralá a bude v budoucí verzi odebrána.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-229">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="6d1e3-230">Doporučenou alternativou je Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-230">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="6d1e3-231">CookiePath</span><span class="sxs-lookup"><span data-stu-id="6d1e3-231">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="6d1e3-232">Cesta, nastavte v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-232">The path set on the cookie.</span></span> <span data-ttu-id="6d1e3-233">Tato vlastnost je zastaralá a bude v budoucí verzi odebrána.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-233">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="6d1e3-234">Doporučenou alternativou je Cookie.Path.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-234">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="6d1e3-235">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="6d1e3-235">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="6d1e3-236">Název skryté pole formuláře antiforgery systému použije k vykreslení antiforgery tokeny v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-236">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="6d1e3-237">HeaderName</span><span class="sxs-lookup"><span data-stu-id="6d1e3-237">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="6d1e3-238">Název hlavičky používá antiforgery systémem.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-238">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="6d1e3-239">Pokud `null`, systém bere v úvahu pouze data formuláře.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-239">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="6d1e3-240">Vlastnost RequireSsl</span><span class="sxs-lookup"><span data-stu-id="6d1e3-240">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="6d1e3-241">Určuje, zda antiforgery systému vyžadují protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-241">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="6d1e3-242">Pokud `true`, neúspěšné požadavky bez HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-242">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="6d1e3-243">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-243">Defaults to `false`.</span></span> <span data-ttu-id="6d1e3-244">Tato vlastnost je zastaralá a bude v budoucí verzi odebrána.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-244">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="6d1e3-245">Doporučenou alternativou je nastavit Cookie.SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-245">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="6d1e3-246">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="6d1e3-246">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="6d1e3-247">Určuje, jestli se má potlačit generování `X-Frame-Options` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-247">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="6d1e3-248">Ve výchozím nastavení je záhlaví generovány s hodnotou "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="6d1e3-248">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="6d1e3-249">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-249">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="6d1e3-250">Další informace najdete v tématu [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-250">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="6d1e3-251">Nakonfigurovat IAntiforgery antiforgery funkce</span><span class="sxs-lookup"><span data-stu-id="6d1e3-251">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="6d1e3-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) poskytuje rozhraní API pro konfiguraci antiforgery funkcí.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="6d1e3-253">`IAntiforgery` můžete požádat v `Configure` metodu `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-253">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="6d1e3-254">Následující příklad používá middleware z domovské stránky aplikaci k vygenerování tokenu antiforgery a odeslat v odpovědi jako soubor cookie (pomocí výchozí Angular konvence je popsáno dále v tomto tématu):</span><span class="sxs-lookup"><span data-stu-id="6d1e3-254">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

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

### <a name="require-antiforgery-validation"></a><span data-ttu-id="6d1e3-255">Vyžadování antiforgery ověření</span><span class="sxs-lookup"><span data-stu-id="6d1e3-255">Require antiforgery validation</span></span>

<span data-ttu-id="6d1e3-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) je filtru akce, který lze použít na jednotlivé akci, kontroler, nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="6d1e3-257">Požadavky na akce, které mají tento filtr použít jsou blokovány, pokud požadavek obsahuje antiforgery platný token.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-257">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

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

<span data-ttu-id="6d1e3-258">`ValidateAntiForgeryToken` Atribut vyžaduje token pro požadavky na metody akce upraví, včetně požadavků HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-258">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it decorates, including HTTP GET requests.</span></span> <span data-ttu-id="6d1e3-259">Pokud `ValidateAntiForgeryToken` napříč řadičů aplikace je použit atribut, jeho monitorconfigurationoverride lze přepsat `IgnoreAntiforgeryToken` atribut.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-259">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="6d1e3-260">ASP.NET Core nepodporuje přidávání antiforgery tokenů pro požadavky GET automaticky.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-260">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="6d1e3-261">Automaticky ověřte antiforgery tokeny pro pouze nezabezpečené metody HTTP</span><span class="sxs-lookup"><span data-stu-id="6d1e3-261">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="6d1e3-262">Aplikace ASP.NET Core negenerovat antiforgery tokeny pro bezpečné metody HTTP (GET, HEAD, možnosti a trasování).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-262">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="6d1e3-263">Místo použití široce `ValidateAntiForgeryToken` atribut a pak jeho pomocí přepsání `IgnoreAntiforgeryToken` atributy, [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) atribut lze použít.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-263">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="6d1e3-264">Tento atribut funguje stejně jako `ValidateAntiForgeryToken` atribut, s tím rozdílem, že nevyžaduje tokeny pro žádosti pomocí následujících metod HTTP:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-264">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="6d1e3-265">GET</span><span class="sxs-lookup"><span data-stu-id="6d1e3-265">GET</span></span>
* <span data-ttu-id="6d1e3-266">HLAVNÍ</span><span class="sxs-lookup"><span data-stu-id="6d1e3-266">HEAD</span></span>
* <span data-ttu-id="6d1e3-267">MOŽNOSTI</span><span class="sxs-lookup"><span data-stu-id="6d1e3-267">OPTIONS</span></span>
* <span data-ttu-id="6d1e3-268">TRACE</span><span class="sxs-lookup"><span data-stu-id="6d1e3-268">TRACE</span></span>

<span data-ttu-id="6d1e3-269">Doporučujeme použití `AutoValidateAntiforgeryToken` široce pro scénáře – rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-269">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="6d1e3-270">Tím se zajistí, že jsou ve výchozím nastavení chráněna akce POST.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-270">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="6d1e3-271">Alternativou je ignorovat antiforgery tokeny ve výchozím nastavení, pokud `ValidateAntiForgeryToken` platí do jednotlivých metod akcí.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-271">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="6d1e3-272">To má pravděpodobně v tomto scénáři pro metodu akce POST zůstat nechráněné omylem, opuštění zranitelný vůči útokům CSRF aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-272">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="6d1e3-273">Všechny příspěvky by měli poslat antiforgery token.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-273">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="6d1e3-274">Rozhraní API nemáte mechanismus automatického pro odesílání bez souborů cookie součást tokenu mechanismu.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-274">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="6d1e3-275">Implementace pravděpodobně závisí na implementaci kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-275">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="6d1e3-276">Níže je uvedeno několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-276">Some examples are shown below:</span></span>

<span data-ttu-id="6d1e3-277">Příklad úroveň třídy:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-277">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="6d1e3-278">Globální příklad:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-278">Global example:</span></span>

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="6d1e3-279">Přepsat globální nebo antiforgery atributů kontroleru</span><span class="sxs-lookup"><span data-stu-id="6d1e3-279">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="6d1e3-280">[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filtru se používá k eliminuje nutnost antiforgery token pro danou akci (nebo řadič).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-280">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="6d1e3-281">Při použití, přepíše tento filtr `ValidateAntiForgeryToken` a `AutoValidateAntiforgeryToken` filtrů zadaných na vyšší úrovni (globálně nebo na řadiči).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-281">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

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

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="6d1e3-282">Po ověření obnovovacích tokenů</span><span class="sxs-lookup"><span data-stu-id="6d1e3-282">Refresh tokens after authentication</span></span>

<span data-ttu-id="6d1e3-283">Tokeny musí aktualizovat, po ověření uživatele přesměrováním uživatele na zobrazení nebo stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-283">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="6d1e3-284">JavaScript a AJAX, SPA</span><span class="sxs-lookup"><span data-stu-id="6d1e3-284">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="6d1e3-285">V tradičních aplikacích založený na jazyce HTML jsou předány antiforgery tokeny k serveru pomocí skrytého pole.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-285">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="6d1e3-286">V moderních aplikací založené na jazyce JavaScript a SPA velký počet požadavků probíhají prostřednictvím kódu programu.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-286">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="6d1e3-287">Tyto požadavky AJAX použít jiné techniky (například hlavičky požadavků nebo soubory cookie) odeslat token.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-287">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="6d1e3-288">Pokud se soubory cookie se použijí k ukládání ověřovacích tokenů a k ověřování žádostí o rozhraní API na serveru, CSRF je potenciální problém.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-288">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="6d1e3-289">Pokud místní úložiště se používá k ukládání tokenu, CSRF ohrožení zabezpečení může zmírnit, protože hodnoty z místního úložiště se automaticky odeslány na server při každé žádosti.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-289">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="6d1e3-290">Díky tomu se pomocí místního úložiště k ukládání antiforgery token na klienta a odesílání tokenu hlavičky požadavku je doporučený postup.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-290">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="6d1e3-291">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6d1e3-291">JavaScript</span></span>

<span data-ttu-id="6d1e3-292">Pomocí jazyka JavaScript se zobrazeními, token, který je možné vytvořit pomocí služby z v rámci zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-292">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="6d1e3-293">Vložit [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) služby do zobrazení a volání [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="6d1e3-293">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="6d1e3-294">Tento přístup se eliminuje potřeba řešit přímo ze serveru nastavení souborů cookie nebo čtení z klienta.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-294">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="6d1e3-295">V předchozím příkladu používá JavaScript načíst hodnotu skryté pole pro hlavičku AJAX POST.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-295">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="6d1e3-296">JavaScript lze také přístupové tokeny v souborech cookie a použít k vytvoření záhlaví s hodnotou tokenu souboru cookie obsah.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-296">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="6d1e3-297">Za předpokladu, že skript žádostí o odeslání tokenu v záhlaví volá `X-CSRF-TOKEN`, konfigurovat službu antiforgery Hledat `X-CSRF-TOKEN` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-297">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="6d1e3-298">JavaScript provést požadavek AJAX s odpovídající hlavičku v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-298">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

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

### <a name="angularjs"></a><span data-ttu-id="6d1e3-299">AngularJS</span><span class="sxs-lookup"><span data-stu-id="6d1e3-299">AngularJS</span></span>

<span data-ttu-id="6d1e3-300">AngularJS používá konvenci adresu CSRF.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-300">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="6d1e3-301">Pokud server odešle soubor cookie s názvem `XSRF-TOKEN`, AngularJS `$http` služby přidá hodnota souboru cookie pro záhlaví při odesílání požadavku na server.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-301">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="6d1e3-302">Tento proces je automatické.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-302">This process is automatic.</span></span> <span data-ttu-id="6d1e3-303">Záhlaví nemusí být explicitně nastaveno v klientovi.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-303">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="6d1e3-304">Název záhlaví je `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-304">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="6d1e3-305">Server by měl zjistit této hlavičky a ověřit jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-305">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="6d1e3-306">Pro rozhraní ASP.NET Core API pro práci s touto konvencí ve spuštění vaší aplikace:</span><span class="sxs-lookup"><span data-stu-id="6d1e3-306">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="6d1e3-307">Konfigurace aplikace pro poskytnout token v souboru cookie s názvem `XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-307">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="6d1e3-308">Konfigurovat službu antiforgery hledat záhlaví s názvem `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-308">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

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

<span data-ttu-id="6d1e3-309">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6d1e3-309">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="6d1e3-310">Rozšíření antiforgery</span><span class="sxs-lookup"><span data-stu-id="6d1e3-310">Extend antiforgery</span></span>

<span data-ttu-id="6d1e3-311">[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) typ umožňuje vývojářům rozšířit chování systému anti-CSRF verzemi další data v jednotlivých tokenu.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-311">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="6d1e3-312">[GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) metoda je volána pokaždé, když se vygeneruje token pole, a návratová hodnota je vložený v rámci vygenerovaný token.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-312">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="6d1e3-313">Implementátora může vrátit časové razítko, hodnotu nonce nebo jakoukoli jinou hodnotu a pak vyvolejte [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) ověřit data při ověření tokenu.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-313">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="6d1e3-314">Uživatelské jméno klienta je již součástí do generovaných tokenů, takže není nutné zahrnout tyto informace.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-314">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="6d1e3-315">Pokud token obsahuje doplňková data, ale ne `IAntiForgeryAdditionalDataProvider` je nakonfigurován, nejsou dodatečná data ověřena.</span><span class="sxs-lookup"><span data-stu-id="6d1e3-315">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6d1e3-316">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6d1e3-316">Additional resources</span></span>

* <span data-ttu-id="6d1e3-317">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) na [otevřete projekt webové aplikace zabezpečení](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="6d1e3-317">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
