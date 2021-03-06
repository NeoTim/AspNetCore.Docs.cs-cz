---
title: Zabránit útokům na neCSRFelné žádosti mezi lokalitami (XSRF/) v ASP.NET Core
author: steve-smith
description: Zjistěte, jak zabránit útokům na webové aplikace, kde škodlivý web může ovlivnit interakci mezi prohlížečem klienta a aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: security/anti-request-forgery
ms.openlocfilehash: d0cce4f48151ab56774ab28eb6d89a687b3747af
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635122"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Zabránit útokům na neCSRFelné žádosti mezi lokalitami (XSRF/) v ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)a [Steve Smith](https://ardalis.com/)

Padělání požadavků napříč weby (označované také jako XSRF nebo CSRF) představuje útok proti aplikacím hostovaným na webu, kde může škodlivá webová aplikace ovlivnit interakci mezi klientským prohlížečem a webovou aplikací, která tento prohlížeč důvěřuje. Tyto útoky jsou možné, protože webové prohlížeče odesílají některé typy ověřovacích tokenů automaticky pomocí všech požadavků na web. Tato forma zneužití je také známá jako útok s *jedním kliknutím* nebo při *jízdě relace* , protože útok využívá dřív ověřenou relaci uživatele.

Příklad útoku CSRF:

1. Uživatel se přihlásí `www.good-banking-site.com` pomocí ověřování pomocí formulářů. Server ověří uživatele a vydá odpověď, která obsahuje ověření cookie . Lokalita je zranitelná vůči útokům, protože důvěřuje všem žádostem, které obdrží, s platným ověřením cookie .
1. Uživatel navštíví škodlivý web `www.bad-crook-site.com` .

   Škodlivý web, `www.bad-crook-site.com` obsahuje formulář HTML podobný následujícímu:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Všimněte si, že `action` příspěvky formuláře na ohrožený web, nikoli na škodlivý web. Toto je součást CSRF (pro různé lokality).

1. Uživatel vybere tlačítko Odeslat. Prohlížeč vytvoří požadavek a automaticky přidá ověřování cookie pro požadovanou doménu `www.good-banking-site.com` .
1. Požadavek se spouští na `www.good-banking-site.com` serveru s kontextem ověřování uživatele a může provádět všechny akce, které má ověřený uživatel povoleno provádět.

Kromě scénáře, kdy uživatel vybere tlačítko pro odeslání formuláře, může škodlivý web:

* Spustí skript, který automaticky odešle formulář.
* Odeslat odeslání formuláře jako požadavek AJAX.
* Skryjte formulář pomocí šablon stylů CSS.

Tyto alternativní scénáře nevyžadují žádnou akci ani vstup od uživatele kromě prvotního návštěvě škodlivého webu.

Použití protokolu HTTPS nebrání útoku CSRF. Škodlivý web může poslat `https://www.good-banking-site.com/` požadavek stejně snadno, protože může poslat nezabezpečený požadavek.

Některé útoky cílí na koncové body, které reagují na požadavky GET. v takovém případě lze k provedení této akce použít značku obrázku. Tato forma útoku je společná na webech fóra, které povolují image, ale blokují JavaScript. Aplikace, které mění stav u požadavků GET, kde se mění proměnné nebo prostředky, jsou zranitelné vůči škodlivým útokům. **Požadavky GET, které mění stav, jsou nezabezpečené. Osvědčeným postupem je nikdy změnit stav u žádosti o získání.**

Pro webové aplikace, které používají s pro ověřování, je možné CSRF útoky z těchto cookie důvodů:

* Prohlížeče cookie se ukládají pomocí webové aplikace.
* Uložené cookie s zahrnuje relaci cookie s pro ověřené uživatele.
* Prohlížeče odesílají všechny cookie požadavky spojené s doménou do webové aplikace každý požadavek bez ohledu na to, jak se žádost o aplikaci vygenerovala v prohlížeči.

Nicméně útoky CSRF nejsou omezené na zneužití cookie s. Například základní ověřování a ověřování algoritmem Digest je také zranitelné. Když se uživatel přihlásí pomocí základního ověřování nebo ověřování algoritmem Digest, prohlížeč automaticky pošle přihlašovací údaje, dokud relace &dagger; neskončí.

&dagger;V tomto kontextu *relace* odkazuje na relaci na straně klienta, během které je uživatel ověřený. Nesouvisí s relacemi na straně serveru nebo [middlewarem ASP.NET Core relace](xref:fundamentals/app-state).

Uživatelé můžou pomocí preventivních opatření chránit před CSRFmi chybami:

* Po dokončení používání webových aplikací se odhlaste.
* Pravidelně vymažte prohlížeč cookie .

Chyby zabezpečení CSRF ale představují zásadní problém s webovou aplikací, ne koncovým uživatelem.

## <a name="authentication-fundamentals"></a>Základy ověřování

Cookieověřování na základě je oblíbená forma ověřování. Systémy ověřování založené na tokenech se zvětšují v oblíbenosti, zejména u aplikací s jednou stránkou (jednostránkové).

### <a name="no-loccookie-based-authentication"></a>Cookieověřování na základě

Když se uživatel ověřuje pomocí svého uživatelského jména a hesla, vydává token, který obsahuje ověřovací lístek, který se dá použít k ověřování a autorizaci. Token se uloží jako cookie , který doprovází každý požadavek, který klient provede. Generování a ověřování cookie se provádí pomocí Cookie middleware ověřování. [Middleware](xref:fundamentals/middleware/index) serializace instančního objektu uživatele do šifrovaného cookie . Při následném požadavku middleware ověřuje cookie , znovu vytvoří objekt zabezpečení a přiřadí objektu zabezpečení vlastnost [uživatele](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).

### <a name="token-based-authentication"></a>Ověřování na základě tokenů

Když je uživatel ověřený, vydává token (nikoli token pro antipadělání). Token obsahuje informace o uživateli ve formě [deklarací identity](/dotnet/framework/security/claims-based-identity-model) nebo tokenu odkazu, který ukazuje aplikaci na stav uživatele udržované v aplikaci. Když se uživatel pokusí o přístup k prostředku, který vyžaduje ověření, token se do aplikace pošle pomocí další autorizační hlavičky ve formě nosných tokenů. Tím se aplikace bez stavu nastaví. V každém následném požadavku se token předává v žádosti o ověření na straně serveru. Tento token není *zašifrovaný*. je *kódovaný*. Na serveru je token dekóduje pro přístup k jeho informacím. Pokud chcete token odeslat na další požadavky, uložte token do místního úložiště prohlížeče. V případě, že je token uložený v místním úložišti v prohlížeči, nemusíte mít obavy o ohrožení zabezpečení CSRF. CSRF je problém, pokud je token uložen v cookie . Další informace najdete v ukázce kódu pro problém na GitHubu, který [přidává dvě cookie s](https://github.com/dotnet/AspNetCore.Docs/issues/13369).

### <a name="multiple-apps-hosted-at-one-domain"></a>Více aplikací hostovaných v jedné doméně

Sdílená hostující prostředí jsou zranitelná proti zneužití relace, přihlašování CSRF a dalším útokům.

I `example1.contoso.net` Když `example2.contoso.net` jsou a jsou různí hostitelé, existuje implicitní vztah důvěryhodnosti mezi hostiteli v `*.contoso.net` doméně. Tento implicitní vztah důvěryhodnosti umožňuje potenciálně nedůvěryhodným hostitelům navzájem ovlivnit cookie (zásady stejného původu, které řídí požadavky AJAX), nemusí nutně platit pro http cookie s).

Útoky, které využívají důvěryhodného cookie hostitele mezi aplikacemi hostovanými ve stejné doméně, se dají zabránit sdílením domén. Pokud je každá aplikace hostována v její vlastní doméně, není zneužití implicitní cookie vztah důvěryhodnosti.

## <a name="aspnet-core-antiforgery-configuration"></a>ASP.NET Core konfigurace antipadělání

> [!WARNING]
> ASP.NET Core implementuje ochranu proti padělání pomocí [ASP.NET Core ochrany dat](xref:security/data-protection/introduction). Zásobník ochrany dat musí být nakonfigurovaný tak, aby fungoval v serverové farmě. Další informace najdete v tématu [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview) .

::: moniker range=">= aspnetcore-3.0"

V případě, že je v nástroji volána jedna z následujících rozhraní API, je middleware pro vkládání proti padělání přidána do kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) `Startup.ConfigureServices` :

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

K kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) se přidá middleware proti padělání, když <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> se volá v `Startup.ConfigureServices`

::: moniker-end

V ASP.NET Core 2,0 nebo novějším [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) vloží tokeny proti padělání do prvků formuláře HTML. Následující kód v Razor souboru automaticky generuje tokeny proti padělání:

```cshtml
<form method="post">
    ...
</form>
```

Podobně [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generuje tokeny proti padělání ve výchozím nastavení, pokud není metoda formuláře Get.

Automatické generování tokenů antipadělání pro prvky formuláře HTML se stane, když `<form>` značka obsahuje `method="post"` atribut a jedna z následujících možností je pravdivá:

* Atribut Action je prázdný ( `action=""` ).
* Atribut action není dodaný ( `<form method="post">` ).

Automatické generování tokenů antipadělání pro prvky formuláře HTML lze zakázat:

* Explicitně zakažte tokeny proti padělání s `asp-antiforgery` atributem:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* Na prvek formuláře se zarezervuje pomocník značek pomocí pomocníka značek [! symbol pro odhlášení](xref:mvc/views/tag-helpers/intro#opt-out):

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* Odeberte `FormTagHelper` ze zobrazení. V `FormTagHelper` zobrazení lze odebrat přidáním následující direktivy do Razor zobrazení:

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [ Razor Stránky](xref:razor-pages/index) jsou automaticky chráněny z XSRF/CSRF. Další informace naleznete v tématu [XSRF/CSRF a Razor Pages](xref:razor-pages/index#xsrf).

Nejběžnějším přístupem k obraně před útoky CSRF je použití *vzoru tokenu synchronizátoru* (STP). STP se používá v případě, že uživatel požádá o stránku s daty formuláře:

1. Server pošle klientovi token přidružený k identitě aktuálního uživatele.
1. Klient pošle zpátky token na server kvůli ověření.
1. Pokud server obdrží token, který se neshoduje s identitou ověřeného uživatele, požadavek se odmítne.

Token je jedinečný a nepředvídatelný. Token lze také použít k zajištění správného pořadí posloupnosti požadavků (například zajištění pořadí požadavků: Stránka 1 > stránce 2 > stránce 3). Všechny formuláře v ASP.NET Core šablony MVC a Razor stránky generují tokeny proti padělání. Následující pár příkladů zobrazení generuje tokeny proti padělání:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

Explicitně přidejte token proti padělání do `<form>` elementu bez použití pomocných rutin značek s pomocníkem jazyka HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

V každém z předchozích případů ASP.NET Core přidá skryté pole formuláře podobné následujícímu:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core obsahuje tři [filtry](xref:mvc/controllers/filters) pro práci s tokeny proti padělání:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Možnosti padělání

Přizpůsobení [možností proti padělání](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) v nástroji `Startup.ConfigureServices` :

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

&dagger;Vlastnosti antipadělání nastavte `Cookie` pomocí vlastností třídy [ Cookie Tvůrce](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .

| Možnost | Popis |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Určuje nastavení používané k vytvoření antipadělání cookie . |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Název skrytého pole formuláře používaného systémem pro použití proti padělání pro vykreslování tokenů v zobrazeních. |
| [Záhlaví](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Název záhlaví používaného systémem pro použití proti padělání Pokud `null` systém považuje jenom formulářová data. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Určuje, zda se má potlačit generování `X-Frame-Options` hlavičky. Ve výchozím nastavení se záhlaví generuje s hodnotou "SAMEORIGIN". Výchozí hodnota je `false` . |

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

| Možnost | Popis |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Určuje nastavení používané k vytvoření antipadělání cookie . |
| [CookieDoména](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Doména cookie . Výchozí hodnota je `null` . Tato vlastnost je zastaralá a v budoucí verzi se odebere. Doporučená alternativa je Cookie . Domain. |
| [CookieNázev](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Název procesu cookie. Pokud není nastaven, systém vygeneruje jedinečný název začínající [výchozí Cookie předponou](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. antipadělání. "). Tato vlastnost je zastaralá a v budoucí verzi se odebere. Doporučená alternativa je Cookie . Jméno. |
| [CookieDílčí](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Cesta nastavená na cookie . Tato vlastnost je zastaralá a v budoucí verzi se odebere. Doporučená alternativa je Cookie . Dílčí. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Název skrytého pole formuláře používaného systémem pro použití proti padělání pro vykreslování tokenů v zobrazeních. |
| [Záhlaví](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Název záhlaví používaného systémem pro použití proti padělání Pokud `null` systém považuje jenom formulářová data. |
| [Vlastnost requireSSL](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Určuje, zda je protokol HTTPS vyžadován systémem pro vypozměňování. `true`V případě selhání neproběhne požadavek bez protokolu HTTPS. Výchozí hodnota je `false` . Tato vlastnost je zastaralá a v budoucí verzi se odebere. Doporučená alternativa je nastavena Cookie . SecurePolicy. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Určuje, zda se má potlačit generování `X-Frame-Options` hlavičky. Ve výchozím nastavení se záhlaví generuje s hodnotou "SAMEORIGIN". Výchozí hodnota je `false` . |

::: moniker-end

Další informace najdete v tématu [ Cookie AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Konfigurace funkcí antipadělání pomocí IAntiforgery

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) poskytuje rozhraní API pro konfiguraci funkcí pro antipadělání. `IAntiforgery` lze požadovat v `Configure` metodě `Startup` třídy. Následující příklad používá middleware z domovské stránky aplikace k vygenerování tokenu antipadělání a jeho odeslání v odpovědi jako cookie (pomocí výchozích úhlů pro vytváření názvů na základě standardu, které jsou popsány dále v tomto tématu):

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

### <a name="require-antiforgery-validation"></a>Vyžadovat ověření platnosti proti padělání

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) je filtr akcí, který lze použít na jednotlivé akce, kontrolora nebo globálně. Požadavky provedené na akce, které mají tento filtr použit, jsou blokovány, pokud požadavek neobsahuje platný token proti padělání.

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

`ValidateAntiForgeryToken`Atribut vyžaduje token pro požadavky na metody akce, které označuje, včetně požadavků HTTP GET. Pokud `ValidateAntiForgeryToken` je atribut použit v rámci řadičů aplikace, lze jej přepsat `IgnoreAntiforgeryToken` atributem.

> [!NOTE]
> ASP.NET Core nepodporuje přidávání tokenů antipadělání pro automatické získávání požadavků.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Automaticky ověřovat tokeny antipadělání pro nezabezpečené metody HTTP

Aplikace ASP.NET Core negenerují tokeny antipadělání pro bezpečné metody protokolu HTTP (GET, HEAD, OPTIONS a TRACE). Místo toho, abyste `ValidateAntiForgeryToken` atribut používali a pak ho přepsali `IgnoreAntiforgeryToken` atributy, je možné použít atribut [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) . Tento atribut funguje identicky s `ValidateAntiForgeryToken` atributem s tím rozdílem, že nevyžaduje tokeny pro požadavky vytvořené pomocí následujících metod http:

* GET
* HEAD
* NASTAVENÍ
* TRACE

`AutoValidateAntiforgeryToken`Pro jiné scénáře než rozhraní API doporučujeme používat široké možnosti. Tím je zajištěno, že akce příspěvků jsou ve výchozím nastavení chráněny. Alternativou je ignorovat tokeny proti padělání ve výchozím nastavení, pokud `ValidateAntiForgeryToken` se nepoužije na jednotlivé metody akcí. V tomto scénáři je pravděpodobnější, že metoda POST akce bude ponechána bez ochrany omylem, takže aplikace bude zranitelná vůči útokům CSRF. Všechny příspěvky by měly odeslat token proti padělání.

Rozhraní API nemají automatický mechanismus pro odeslání cookie nečásti tokenu. Implementace pravděpodobně závisí na implementaci klientského kódu. Níže jsou uvedeny některé příklady:

Příklad na úrovni třídy:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Globální příklad:

::: moniker range="< aspnetcore-3.0"

orgány. AddMvc (možnosti => možnosti. Filters. Add (New AutoValidateAntiforgeryTokenAttribute ());

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a>Přepsat globální atributy nebo atributy antipadělání řadiče

Filtr [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) se používá k odstranění potřeby tokenu antipadělání pro danou akci (nebo kontroler). Při použití tohoto filtru se přepíší `ValidateAntiForgeryToken` a `AutoValidateAntiforgeryToken` filtry zadané na vyšší úrovni (globálně nebo na řadiči).

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

## <a name="refresh-tokens-after-authentication"></a>Aktualizovat tokeny po ověření

Tokeny by se měly aktualizovat po ověření uživatele tak, že se uživatel přesměruje na stránku zobrazení nebo Razor stránky.

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX a jednostránkové

V tradičních aplikacích založených na HTML jsou tokeny proti padělání předány serveru pomocí skrytých polí formuláře. V moderních aplikacích založených na jazyce JavaScript a jednostránkové se mnoho požadavků provádí programově. Tyto požadavky AJAX můžou k odeslání tokenu použít jiné techniky (například hlavičky žádosti nebo cookie s).

Pokud cookie se používají k ukládání ověřovacích tokenů a k ověřování požadavků na rozhraní API na serveru, CSRF je potenciální problém. Pokud se k uložení tokenu používá místní úložiště, může dojít k zmírnění ohrožení zabezpečení CSRF, protože hodnoty z místního úložiště nejsou automaticky odesílány na server se všemi požadavky. Proto se použití místního úložiště uloží do klienta tokenu antipadělání a odeslání tokenu jako hlavičky požadavku je doporučeným přístupem.

### <a name="javascript"></a>JavaScript

Pomocí JavaScriptu se zobrazeními se token dá vytvořit pomocí služby v zobrazení. Do zobrazení a volání [GetAndStoreTokensu](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)založit službu [Microsoft. AspNetCore.. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) :

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Tento přístup eliminuje nutnost nabývat přímo s nastavením cookie s ze serveru nebo jejich čtením z klienta.

Předchozí příklad používá JavaScript pro čtení hodnoty skrytého pole pro hlavičku příspěvku AJAX.

JavaScript může také přistupovat k tokenům v cookie s a použít cookie obsah k vytvoření hlavičky s hodnotou tokenu.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Za předpokladu, že skript požaduje odeslání tokenu v záhlaví s názvem `X-CSRF-TOKEN` , nakonfigurujte službu proti padělání tak, aby hledala `X-CSRF-TOKEN` hlavičku:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

V následujícím příkladu se pomocí JavaScriptu provede požadavek AJAX s odpovídající hlavičkou:

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

### <a name="angularjs"></a>AngularJS

AngularJS používá konvenci pro řešení CSRF. Pokud server odešle cookie s názvem `XSRF-TOKEN` , `$http` Služba AngularJS cookie při odeslání požadavku na server přidá hodnotu do hlavičky. Tento proces je automatický. Záhlaví není v klientovi nutné nastavit explicitně. Název hlavičky je `X-XSRF-TOKEN` . Server by měl detekovat tuto hlavičku a ověřit její obsah.

ASP.NET Core rozhraní API pro práci s touto konvencí ve vašem spuštění aplikace:

* Nakonfigurujte svou aplikaci tak, aby poskytovala token ve cookie volání `XSRF-TOKEN` .
* Nakonfigurujte službu proti padělání, aby hledala hlavičku s názvem `X-XSRF-TOKEN` .

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

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="extend-antiforgery"></a>Rozšiřování antipadělání

Typ [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) umožňuje vývojářům roztáhnout chování systému anti-CSRF s kulatými Trip dalšími daty v každém tokenu. Metoda [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) je volána při každém vygenerování tokenu pole a návratová hodnota je vložena do vygenerovaného tokenu. Implementátor by mohl vrátit časové razítko, hodnotu NONCE nebo jakoukoli jinou hodnotu a pak zavolat [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) , aby ověřil tato data při ověření tokenu. Uživatelské jméno klienta je již vloženo do vygenerovaných tokenů, takže není nutné tyto informace zahrnout. Pokud token zahrnuje doplňková data, ale není `IAntiForgeryAdditionalDataProvider` nakonfigurovaná, doplňují se data neověřují.

## <a name="additional-resources"></a>Další zdroje informací

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) v [otevřeném projektu webové aplikace zabezpečení](https://www.owasp.org/index.php/Main_Page) (OWASP).
* <xref:host-and-deploy/web-farm>
