---
title: Bezpečná Blazor ASP.NET základní webová sestava
author: guardrex
description: Zjistěte, Blazor jak zabezpečit aplikace WebAssemlby jako jednostránkové aplikace (SP).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: be286d770cd8d6e5cf7885b91be8654f74ffd743
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80538971"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="9e352-103">Bezpečná Blazor ASP.NET základní webová sestava</span><span class="sxs-lookup"><span data-stu-id="9e352-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="9e352-104">Podle [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="9e352-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor<span data-ttu-id="9e352-105">Aplikace WebAssembly jsou zabezpečeny stejným způsobem jako jednostránkové aplikace (SPA).</span><span class="sxs-lookup"><span data-stu-id="9e352-105"> WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="9e352-106">Existuje několik přístupů pro ověřování uživatelů na SPA, ale nejběžnější a komplexní přístup je použití implementace založené na [protokolu OAuth 2.0](https://oauth.net/), například [Open ID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="9e352-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [Open ID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="9e352-107">Ověřovací knihovna</span><span class="sxs-lookup"><span data-stu-id="9e352-107">Authentication library</span></span>

Blazor<span data-ttu-id="9e352-108">WebAssembly podporuje ověřování a autorizaci aplikací pomocí `Microsoft.AspNetCore.Components.WebAssembly.Authentication` OIDC prostřednictvím knihovny.</span><span class="sxs-lookup"><span data-stu-id="9e352-108"> WebAssembly supports authenticating and authorizing apps using OIDC via the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library.</span></span> <span data-ttu-id="9e352-109">Knihovna poskytuje sadu primitiv pro bezproblémovou autentici proti ASP.NET back-endů jádra.</span><span class="sxs-lookup"><span data-stu-id="9e352-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="9e352-110">Knihovna integruje ASP.NET základní identity s podporou autorizace rozhraní API, která je postavena na [serveru Identity Server](https://identityserver.io/).</span><span class="sxs-lookup"><span data-stu-id="9e352-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="9e352-111">Knihovna se může ověřit proti libovolnému poskytovateli identity (IP) jiného výrobce, který podporuje OIDC, které se nazývají OpenID Providers (OP).</span><span class="sxs-lookup"><span data-stu-id="9e352-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="9e352-112">Podpora ověřování Blazor v aplikaci WebAssembly je postavena na knihovně *oidc-client.js,* která se používá ke zpracování základních podrobností ověřovacího protokolu.</span><span class="sxs-lookup"><span data-stu-id="9e352-112">The authentication support in Blazor WebAssembly is built on top of the *oidc-client.js* library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="9e352-113">Existují další možnosti ověřování místních jmen, například použití souborů cookie SameSite.</span><span class="sxs-lookup"><span data-stu-id="9e352-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="9e352-114">Inženýrský návrh Blazor WebAssembly je však vyřešen na OAuth a OIDC Blazor jako nejlepší volba pro ověřování v aplikacích WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="9e352-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="9e352-115">[Ověřování založené na tokenech](xref:security/anti-request-forgery#token-based-authentication) založené na [webových tokenech JSON (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) bylo vybráno přes [ověřování založené na souborech cookie](xref:security/anti-request-forgery#cookie-based-authentication) z funkčních a bezpečnostních důvodů:</span><span class="sxs-lookup"><span data-stu-id="9e352-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="9e352-116">Pomocí protokolu založeného na tokenech nabízí menší oblast útoku, protože tokeny nejsou odesílány ve všech požadavcích.</span><span class="sxs-lookup"><span data-stu-id="9e352-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="9e352-117">Koncové body serveru nevyžadují ochranu proti [padělání požadavků mezi servery (CSRF),](xref:security/anti-request-forgery) protože tokeny jsou odesílány explicitně.</span><span class="sxs-lookup"><span data-stu-id="9e352-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="9e352-118">To vám umožní Blazor hostovat aplikace WebAssembly vedle aplikací MVC nebo Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="9e352-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="9e352-119">Tokeny mají užší oprávnění než soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="9e352-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="9e352-120">Tokeny nelze například použít ke správě uživatelského účtu nebo ke změně hesla uživatele, pokud není tato funkce explicitně implementována.</span><span class="sxs-lookup"><span data-stu-id="9e352-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="9e352-121">Tokeny mají krátkou životnost, ve výchozím nastavení jednu hodinu, což omezuje okno útoku.</span><span class="sxs-lookup"><span data-stu-id="9e352-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="9e352-122">Tokeny lze také kdykoli odvolat.</span><span class="sxs-lookup"><span data-stu-id="9e352-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="9e352-123">Samostatné JWTnabízejí klientovi a serveru záruky ohledně procesu ověřování.</span><span class="sxs-lookup"><span data-stu-id="9e352-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="9e352-124">Klient má například prostředky ke zjištění a ověření, že tokeny, které obdrží, jsou legitimní a byly emitovány jako součást daného procesu ověřování.</span><span class="sxs-lookup"><span data-stu-id="9e352-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="9e352-125">Pokud se třetí strana pokusí přepnout token uprostřed procesu ověřování, klient může rozpoznat přepínaný token a vyhnout se jeho použití.</span><span class="sxs-lookup"><span data-stu-id="9e352-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="9e352-126">Tokeny s OAuth a OIDC nespoléhají na uživatelského agenta, který se chová správně, aby zajistil, že aplikace je zabezpečená.</span><span class="sxs-lookup"><span data-stu-id="9e352-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="9e352-127">Protokoly založené na tokenech, jako jsou OAuth a OIDC, umožňují ověřování a autorizaci hostovaných a samostatných aplikací se stejnou sadou charakteristik zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="9e352-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="9e352-128">Proces ověřování pomocí OIDC</span><span class="sxs-lookup"><span data-stu-id="9e352-128">Authentication process with OIDC</span></span>

<span data-ttu-id="9e352-129">Knihovna `Microsoft.AspNetCore.Components.WebAssembly.Authentication` nabízí několik primitiv k implementaci ověřování a autorizace pomocí OIDC.</span><span class="sxs-lookup"><span data-stu-id="9e352-129">The `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="9e352-130">Obecně platí, že ověřování funguje následovně:</span><span class="sxs-lookup"><span data-stu-id="9e352-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="9e352-131">Když anonymní uživatel vybere přihlašovací tlačítko nebo požádá [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) o stránku s použitým atributem, je`/authentication/login`uživatel přesměrován na přihlašovací stránku aplikace ( ).</span><span class="sxs-lookup"><span data-stu-id="9e352-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="9e352-132">Na přihlašovací stránce se ověřovací knihovna připraví na přesměrování na koncový bod autorizace.</span><span class="sxs-lookup"><span data-stu-id="9e352-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="9e352-133">Koncový bod autorizace je Blazor mimo aplikaci WebAssembly a může být hostován v samostatném původu.</span><span class="sxs-lookup"><span data-stu-id="9e352-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="9e352-134">Koncový bod je zodpovědný za určení, zda je uživatel ověřen a pro vydávání jednoho nebo více tokenů v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="9e352-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="9e352-135">Ověřovací knihovna poskytuje zpětné volání přihlášení pro příjem odpovědi na ověření.</span><span class="sxs-lookup"><span data-stu-id="9e352-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="9e352-136">Pokud uživatel není ověřen, je uživatel přesměrován do základního ověřovacího systému, který je obvykle ASP.NET základní identity.</span><span class="sxs-lookup"><span data-stu-id="9e352-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="9e352-137">Pokud byl uživatel již ověřen, koncový bod autorizace vygeneruje příslušné tokeny a přesměruje`/authentication/login-callback`prohlížeč zpět do koncového bodu zpětného volání přihlášení ( ).</span><span class="sxs-lookup"><span data-stu-id="9e352-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="9e352-138">Když Blazor aplikace WebAssembly načte koncový bod`/authentication/login-callback`zpětného volání přihlášení ( ), zpracuje se odpověď na ověření.</span><span class="sxs-lookup"><span data-stu-id="9e352-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="9e352-139">Pokud se proces ověřování úspěšně dokončí, je uživatel ověřen a volitelně odeslán zpět na původní chráněnou adresu URL, kterou uživatel požadoval.</span><span class="sxs-lookup"><span data-stu-id="9e352-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="9e352-140">Pokud se proces ověřování z nějakého důvodu nezdaří,`/authentication/login-failed`uživatel je odeslán na přihlašovací neúspěšnou stránku ( ) a zobrazí se chyba.</span><span class="sxs-lookup"><span data-stu-id="9e352-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="9e352-141">Podpora předběžného vykreslování pomocí ověřování</span><span class="sxs-lookup"><span data-stu-id="9e352-141">Support prerendering with authentication</span></span>

<span data-ttu-id="9e352-142">Po provedení pokynů v jednom Blazor z témat hostované aplikace WebAssembly vytvořte aplikaci, která:</span><span class="sxs-lookup"><span data-stu-id="9e352-142">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="9e352-143">Prevykresluje cesty, pro které není vyžadováno autorizace.</span><span class="sxs-lookup"><span data-stu-id="9e352-143">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="9e352-144">Není prerender cesty, pro které je vyžadováno autorizace.</span><span class="sxs-lookup"><span data-stu-id="9e352-144">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="9e352-145">Ve třídě klientské `Program` aplikace *(Program.cs)* faktor běžné registrace služeb do `ConfigureCommonServices`samostatné metody (například ):</span><span class="sxs-lookup"><span data-stu-id="9e352-145">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        services.AddBaseAddressHttpClient();
        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="9e352-146">V aplikaci `Startup.ConfigureServices`Server zaregistrujte následující další služby:</span><span class="sxs-lookup"><span data-stu-id="9e352-146">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="9e352-147">V `Startup.Configure` metodě aplikace Server `endpoints.MapFallbackToFile("index.html")` nahraďte `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="9e352-147">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="9e352-148">V aplikaci Server vytvořte složku *Stránky,* pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="9e352-148">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="9e352-149">Vytvořte stránku *_Host.cshtml* ve složce *Stránky* aplikace Server.</span><span class="sxs-lookup"><span data-stu-id="9e352-149">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="9e352-150">Vložte obsah ze souboru *wwwroot/index.html* klientské aplikace do souboru *Pages/_Host.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="9e352-150">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="9e352-151">Aktualizace obsahu souboru:</span><span class="sxs-lookup"><span data-stu-id="9e352-151">Update the file's contents:</span></span>

* <span data-ttu-id="9e352-152">Přidat `@page "_Host"` do horní části souboru.</span><span class="sxs-lookup"><span data-stu-id="9e352-152">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="9e352-153">Nahraďte `<app>Loading...</app>` značku následujícím:</span><span class="sxs-lookup"><span data-stu-id="9e352-153">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="9e352-154">Možnosti hostovaných aplikací a poskytovatelů přihlášení třetích stran</span><span class="sxs-lookup"><span data-stu-id="9e352-154">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="9e352-155">Při ověřování a autorizaci hostované Blazor aplikace WebAssembly s poskytovatelem třetí strany je k dispozici několik možností pro ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="9e352-155">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="9e352-156">Který z nich si vyberete, závisí na vašem scénáři.</span><span class="sxs-lookup"><span data-stu-id="9e352-156">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="9e352-157">Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="9e352-157">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="9e352-158">Ověření uživatelů pouze volání chráněných api třetích stran</span><span class="sxs-lookup"><span data-stu-id="9e352-158">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="9e352-159">Ověřte uživatele pomocí toku OAuth na straně klienta proti poskytovateli rozhraní API třetí strany:</span><span class="sxs-lookup"><span data-stu-id="9e352-159">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="9e352-160">V tomto scénáři:</span><span class="sxs-lookup"><span data-stu-id="9e352-160">In this scenario:</span></span>

* <span data-ttu-id="9e352-161">Server hostující aplikaci nehraje roli.</span><span class="sxs-lookup"><span data-stu-id="9e352-161">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="9e352-162">Api na serveru nelze chránit.</span><span class="sxs-lookup"><span data-stu-id="9e352-162">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="9e352-163">Aplikace může volat pouze chráněná api třetích stran.</span><span class="sxs-lookup"><span data-stu-id="9e352-163">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="9e352-164">Ověření uživatelů pomocí zprostředkovatele jiného výrobce a oprávnění API chráněná voláním na hostitelském serveru a u třetí strany</span><span class="sxs-lookup"><span data-stu-id="9e352-164">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="9e352-165">Nakonfigurujte identitu pomocí zprostředkovatele přihlášení třetí strany.</span><span class="sxs-lookup"><span data-stu-id="9e352-165">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="9e352-166">Získejte tokeny požadované pro přístup k rozhraní API třetích stran a uložte je.</span><span class="sxs-lookup"><span data-stu-id="9e352-166">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="9e352-167">Když se uživatel přihlásí, identita shromažďuje přístup ové a obnovovací tokeny jako součást procesu ověřování.</span><span class="sxs-lookup"><span data-stu-id="9e352-167">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="9e352-168">V tomto okamžiku existuje několik přístupů k dispozici pro volání rozhraní API pro rozhraní API rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9e352-168">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="9e352-169">Načtení přístupového tokenu jiného výrobce pomocí tokenu přístupu serveru</span><span class="sxs-lookup"><span data-stu-id="9e352-169">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="9e352-170">Pomocí přístupového tokenu generovaného na serveru načtěte přístupový token třetí strany z koncového bodu rozhraní API serveru.</span><span class="sxs-lookup"><span data-stu-id="9e352-170">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="9e352-171">Odtud použijte přístupový token třetí strany k volání prostředků rozhraní API třetích stran přímo z identity na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9e352-171">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="9e352-172">Tento přístup nedoporučujeme.</span><span class="sxs-lookup"><span data-stu-id="9e352-172">We don't recommend this approach.</span></span> <span data-ttu-id="9e352-173">Tento přístup vyžaduje, aby se přístupový token třetí strany zacházel, jako by byl vygenerován pro veřejného klienta.</span><span class="sxs-lookup"><span data-stu-id="9e352-173">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="9e352-174">V podmínkách OAuth veřejná aplikace nemá tajný klíč klienta, protože nelze důvěřovat bezpečnému ukládání tajných kódů a přístupový token se vytváří pro důvěrného klienta.</span><span class="sxs-lookup"><span data-stu-id="9e352-174">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="9e352-175">Důvěrný klient je klient, který má tajný klíč klienta a předpokládá se, že bude schopen bezpečně ukládat tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="9e352-175">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="9e352-176">Přístupový token jiného výrobce může být udělen další obory provádět citlivé operace na základě skutečnosti, že třetí strana vyzařovala token pro důvěryhodnější klienta.</span><span class="sxs-lookup"><span data-stu-id="9e352-176">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="9e352-177">Podobně aktualizovat tokeny by neměly být vydávány klientovi, který není důvěryhodný, protože tím poskytuje klientovi neomezený přístup, pokud nejsou zavedena jiná omezení.</span><span class="sxs-lookup"><span data-stu-id="9e352-177">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="9e352-178">Volání rozhraní API z klienta do rozhraní API serveru za účelem volání rozhraní API třetích stran</span><span class="sxs-lookup"><span data-stu-id="9e352-178">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="9e352-179">Proveďte volání rozhraní API z klienta do rozhraní API serveru.</span><span class="sxs-lookup"><span data-stu-id="9e352-179">Make an API call from the client to the server API.</span></span> <span data-ttu-id="9e352-180">Ze serveru načtěte přístupový token pro prostředek rozhraní API třetí strany a vystavte jakékoli volání, které je nezbytné.</span><span class="sxs-lookup"><span data-stu-id="9e352-180">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="9e352-181">Zatímco tento přístup vyžaduje další síťový směrování přes server pro volání rozhraní API jiného výrobce, nakonec má za následek bezpečnější prostředí:</span><span class="sxs-lookup"><span data-stu-id="9e352-181">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="9e352-182">Server může ukládat obnovovací tokeny a zajistit, aby aplikace neztratila přístup k prostředkům třetích stran.</span><span class="sxs-lookup"><span data-stu-id="9e352-182">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="9e352-183">Aplikace nemůže unikat přístupové tokeny ze serveru, které mohou obsahovat citlivější oprávnění.</span><span class="sxs-lookup"><span data-stu-id="9e352-183">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
