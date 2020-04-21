---
title: Bezpečná Blazor ASP.NET základní webová sestava
author: guardrex
description: Zjistěte, Blazor jak zabezpečit aplikace WebAssemlby jako jednostránkové aplikace (SP).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: b82341f3d1d0665d4ee31bb6d967ccf305bae9c3
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661784"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>Bezpečná Blazor ASP.NET základní webová sestava

Podle [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

BlazorAplikace WebAssembly jsou zabezpečeny stejným způsobem jako jednostránkové aplikace (SPA). Existuje několik přístupů pro ověřování uživatelů na SPA, ale nejběžnější a komplexní přístup je použití implementace založené na [protokolu OAuth 2.0](https://oauth.net/), například [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Ověřovací knihovna

BlazorWebAssembly podporuje ověřování a autorizaci aplikací pomocí `Microsoft.AspNetCore.Components.WebAssembly.Authentication` OIDC prostřednictvím knihovny. Knihovna poskytuje sadu primitiv pro bezproblémovou autentici proti ASP.NET back-endů jádra. Knihovna integruje ASP.NET základní identity s podporou autorizace rozhraní API, která je postavena na [serveru Identity Server](https://identityserver.io/). Knihovna se může ověřit proti libovolnému poskytovateli identity (IP) jiného výrobce, který podporuje OIDC, které se nazývají OpenID Providers (OP).

Podpora ověřování Blazor v aplikaci WebAssembly je postavena na knihovně *oidc-client.js,* která se používá ke zpracování základních podrobností ověřovacího protokolu.

Existují další možnosti ověřování místních jmen, například použití souborů cookie SameSite. Inženýrský návrh Blazor WebAssembly je však vyřešen na OAuth a OIDC Blazor jako nejlepší volba pro ověřování v aplikacích WebAssembly. [Ověřování založené na tokenech](xref:security/anti-request-forgery#token-based-authentication) založené na [webových tokenech JSON (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) bylo vybráno přes [ověřování založené na souborech cookie](xref:security/anti-request-forgery#cookie-based-authentication) z funkčních a bezpečnostních důvodů:

* Pomocí protokolu založeného na tokenech nabízí menší oblast útoku, protože tokeny nejsou odesílány ve všech požadavcích.
* Koncové body serveru nevyžadují ochranu proti [padělání požadavků mezi servery (CSRF),](xref:security/anti-request-forgery) protože tokeny jsou odesílány explicitně. To vám umožní Blazor hostovat aplikace WebAssembly vedle aplikací MVC nebo Razor stránky.
* Tokeny mají užší oprávnění než soubory cookie. Tokeny nelze například použít ke správě uživatelského účtu nebo ke změně hesla uživatele, pokud není tato funkce explicitně implementována.
* Tokeny mají krátkou životnost, ve výchozím nastavení jednu hodinu, což omezuje okno útoku. Tokeny lze také kdykoli odvolat.
* Samostatné JWTnabízejí klientovi a serveru záruky ohledně procesu ověřování. Klient má například prostředky ke zjištění a ověření, že tokeny, které obdrží, jsou legitimní a byly emitovány jako součást daného procesu ověřování. Pokud se třetí strana pokusí přepnout token uprostřed procesu ověřování, klient může rozpoznat přepínaný token a vyhnout se jeho použití.
* Tokeny s OAuth a OIDC nespoléhají na uživatelského agenta, který se chová správně, aby zajistil, že aplikace je zabezpečená.
* Protokoly založené na tokenech, jako jsou OAuth a OIDC, umožňují ověřování a autorizaci hostovaných a samostatných aplikací se stejnou sadou charakteristik zabezpečení.

## <a name="authentication-process-with-oidc"></a>Proces ověřování pomocí OIDC

Knihovna `Microsoft.AspNetCore.Components.WebAssembly.Authentication` nabízí několik primitiv k implementaci ověřování a autorizace pomocí OIDC. Obecně platí, že ověřování funguje následovně:

* Když anonymní uživatel vybere přihlašovací tlačítko nebo požádá [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) o stránku s použitým atributem, je`/authentication/login`uživatel přesměrován na přihlašovací stránku aplikace ( ).
* Na přihlašovací stránce se ověřovací knihovna připraví na přesměrování na koncový bod autorizace. Koncový bod autorizace je Blazor mimo aplikaci WebAssembly a může být hostován v samostatném původu. Koncový bod je zodpovědný za určení, zda je uživatel ověřen a pro vydávání jednoho nebo více tokenů v odpovědi. Ověřovací knihovna poskytuje zpětné volání přihlášení pro příjem odpovědi na ověření.
  * Pokud uživatel není ověřen, je uživatel přesměrován do základního ověřovacího systému, který je obvykle ASP.NET základní identity.
  * Pokud byl uživatel již ověřen, koncový bod autorizace vygeneruje příslušné tokeny a přesměruje`/authentication/login-callback`prohlížeč zpět do koncového bodu zpětného volání přihlášení ( ).
* Když Blazor aplikace WebAssembly načte koncový bod`/authentication/login-callback`zpětného volání přihlášení ( ), zpracuje se odpověď na ověření.
  * Pokud se proces ověřování úspěšně dokončí, je uživatel ověřen a volitelně odeslán zpět na původní chráněnou adresu URL, kterou uživatel požadoval.
  * Pokud se proces ověřování z nějakého důvodu nezdaří,`/authentication/login-failed`uživatel je odeslán na přihlašovací neúspěšnou stránku ( ) a zobrazí se chyba.

## <a name="support-prerendering-with-authentication"></a>Podpora předběžného vykreslování pomocí ověřování

Po provedení pokynů v jednom Blazor z témat hostované aplikace WebAssembly vytvořte aplikaci, která:

* Prevykresluje cesty, pro které není vyžadováno autorizace.
* Není prerender cesty, pro které je vyžadováno autorizace.

Ve třídě klientské `Program` aplikace *(Program.cs)* faktor běžné registrace služeb do `ConfigureCommonServices`samostatné metody (například ):

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

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

V aplikaci `Startup.ConfigureServices`Server zaregistrujte následující další služby:

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

V `Startup.Configure` metodě aplikace Server `endpoints.MapFallbackToFile("index.html")` nahraďte `endpoints.MapFallbackToPage("/_Host")`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

V aplikaci Server vytvořte složku *Stránky,* pokud neexistuje. Vytvořte stránku *_Host.cshtml* ve složce *Stránky* aplikace Server. Vložte obsah ze souboru *wwwroot/index.html* klientské aplikace do souboru *Pages/_Host.cshtml.* Aktualizace obsahu souboru:

* Přidat `@page "_Host"` do horní části souboru.
* Nahraďte `<app>Loading...</app>` značku následujícím:

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Možnosti hostovaných aplikací a poskytovatelů přihlášení třetích stran

Při ověřování a autorizaci hostované Blazor aplikace WebAssembly s poskytovatelem třetí strany je k dispozici několik možností pro ověření uživatele. Který z nich si vyberete, závisí na vašem scénáři.

Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Ověření uživatelů pouze volání chráněných api třetích stran

Ověřte uživatele pomocí toku OAuth na straně klienta proti poskytovateli rozhraní API třetí strany:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 V tomto scénáři:

* Server hostující aplikaci nehraje roli.
* Api na serveru nelze chránit.
* Aplikace může volat pouze chráněná api třetích stran.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Ověření uživatelů pomocí zprostředkovatele jiného výrobce a oprávnění API chráněná voláním na hostitelském serveru a u třetí strany

Nakonfigurujte identitu pomocí zprostředkovatele přihlášení třetí strany. Získejte tokeny požadované pro přístup k rozhraní API třetích stran a uložte je.

Když se uživatel přihlásí, identita shromažďuje přístup ové a obnovovací tokeny jako součást procesu ověřování. V tomto okamžiku existuje několik přístupů k dispozici pro volání rozhraní API pro rozhraní API rozhraní API.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Načtení přístupového tokenu jiného výrobce pomocí tokenu přístupu serveru

Pomocí přístupového tokenu generovaného na serveru načtěte přístupový token třetí strany z koncového bodu rozhraní API serveru. Odtud použijte přístupový token třetí strany k volání prostředků rozhraní API třetích stran přímo z identity na straně klienta.

Tento přístup nedoporučujeme. Tento přístup vyžaduje, aby se přístupový token třetí strany zacházel, jako by byl vygenerován pro veřejného klienta. V podmínkách OAuth veřejná aplikace nemá tajný klíč klienta, protože nelze důvěřovat bezpečnému ukládání tajných kódů a přístupový token se vytváří pro důvěrného klienta. Důvěrný klient je klient, který má tajný klíč klienta a předpokládá se, že bude schopen bezpečně ukládat tajné klíče.

* Přístupový token jiného výrobce může být udělen další obory provádět citlivé operace na základě skutečnosti, že třetí strana vyzařovala token pro důvěryhodnější klienta.
* Podobně aktualizovat tokeny by neměly být vydávány klientovi, který není důvěryhodný, protože tím poskytuje klientovi neomezený přístup, pokud nejsou zavedena jiná omezení.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Volání rozhraní API z klienta do rozhraní API serveru za účelem volání rozhraní API třetích stran

Proveďte volání rozhraní API z klienta do rozhraní API serveru. Ze serveru načtěte přístupový token pro prostředek rozhraní API třetí strany a vystavte jakékoli volání, které je nezbytné.

Zatímco tento přístup vyžaduje další síťový směrování přes server pro volání rozhraní API jiného výrobce, nakonec má za následek bezpečnější prostředí:

* Server může ukládat obnovovací tokeny a zajistit, aby aplikace neztratila přístup k prostředkům třetích stran.
* Aplikace nemůže unikat přístupové tokeny ze serveru, které mohou obsahovat citlivější oprávnění.
