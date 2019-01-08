---
title: Útokům zabránilo webů žádosti padělání (XSRF/CSRF) v ASP.NET Core
author: steve-smith
description: Zjistěte, jak zabránit v napadení webových aplikací, kde škodlivý webu mohou mít vliv na interakci mezi prohlížeče klienta a aplikace.
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: 3c1ea8f41eb6ed847bf24141ef0ae0c7e03d8a79
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099218"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Útokům zabránilo webů žádosti padělání (XSRF/CSRF) v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), a [Rick Anderson](https://twitter.com/RickAndMSFT)

Padělání žádosti více webů (označované také jako XSRF nebo CSRF, vyslovováno *viz procházení*) je útok na hostované webové aplikace, které škodlivý webové aplikace mohou mít vliv na interakci mezi klientského prohlížeče a webové aplikace, které důvěřuje, který prohlížeč. Tyto útoky nejsou možné, protože webových prohlížečů odesílat některé typy ověřování tokenů automaticky při každé žádosti na web. Tato forma před zneužitím se taky říká *jedním kliknutím útoku* nebo *relace záleželo* protože využívá výhod platformy útoku uživatele ověřený relace.

Příklad útok CSRF:

1. Uživatel se přihlásí do `www.good-banking-site.com` ověřování pomocí formulářů. Server ověřuje uživatele a vydá odpověď, která obsahuje soubor cookie ověřování. Web je snadno napadnutelný vzhledem k tomu, že důvěřuje všechny požadavky, které přijímá s platný ověřovací soubor cookie.
1. Uživatel navštíví škodlivým webům `www.bad-crook-site.com`.

   Škodlivé weby `www.bad-crook-site.com`, obsahuje formulář HTML, který je podobný následujícímu:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Všimněte si, že formulář `action` příspěvků na webu zranitelné, nikoli na škodlivé weby. Toto je část "webů" CSRF.

1. Uživatel vybere tlačítko pro odeslání. Prohlížeč provede požadavek a automaticky zahrne ověřovacího souboru cookie pro požadovaná doména `www.good-banking-site.com`.
1. Požadavek poběží `www.good-banking-site.com` server s kontext ověřování uživatele a mohou provádět všechny akce, které ověřený uživatel smí provádět.

Kromě tohoto scénáře, které si uživatel vybere tlačítko k odeslání formuláře může škodlivé weby:

* Spusťte skript, který automaticky odešle formulář.
* Odeslání formuláře odešlete jako požadavek AJAX.
* Skryjte formuláři pomocí šablon stylů CSS.

Tyto alternativní scénáře nevyžadují žádné akce ani vstup od uživatele, než je zpočátku navštívit škodlivé weby.

Pomocí protokolu HTTPS není zabránění útoku CSRF. Může odesílat škodlivé weby `https://www.good-banking-site.com/` stejně snadno jako může odeslat požadavek nezabezpečeného požadavku.

Některé útoky cílové koncové body, které reagují na požadavky GET, značka obrázku v takovém případě lze použít k provedení akce. Tato forma útoku je běžné v fór, které umožňují imagí, ale zablokuje JavaScript. Aplikace, které změní stav pro požadavky GET, kde jsou změnit proměnné nebo prostředky, jsou ohrožená útoky se zlými úmysly. **Jsou nezabezpečené požadavky GET, které mění stav. Osvědčeným postupem je nikdy nezmění stav na požadavek GET.**

Útokům CSRF jsou možné proti webové aplikace, které používají soubory cookie pro ověřování, protože:

* Prohlížeče ukládání souborů cookie vydala webové aplikace.
* Uložené soubory cookie obsahují soubory cookie relace pro ověřené uživatele.
* Prohlížeče odesílají, že všechny soubory cookie přidružené k doméně do webové aplikace všechny požadavky bez ohledu na to, jak vygenerování této žádosti do aplikace v prohlížeči.

Však nejsou omezeni útokům CSRF k využívání soubory cookie. Například jsou Basic a Digest ověřování také zranitelné. Jakmile se uživatel přihlásí pomocí ověřování základní nebo Digest, prohlížeč automaticky odesílá přihlašovací údaje do relace&dagger; skončí.

&dagger;V tomto kontextu *relace* odkazuje na relace na straně klienta, během které je uživatel ověřený. Je nezávislé na relace na straně serveru nebo [Middleware relace ASP.NET Core](xref:fundamentals/app-state).

Uživatelé můžou pomáhalo chránit před ohrožení zabezpečení CSRF provedením opatření:

* Přihlášení z webové aplikace po dokončení jejich používání.
* Vymazat soubory cookie v prohlížeči pravidelně.

Ale CSRF ohrožení zabezpečení jsou v podstatě potíže s webovou aplikací, ne koncový uživatel.

## <a name="authentication-fundamentals"></a>Základy ověřování

Ověřování na základě souborů cookie je Oblíbené formu ověřování. Ověřování pomocí tokenu systémy jsou stále se rozšiřující stále oblíbenější, zejména pro jednostránkové aplikace (SPA).

### <a name="cookie-based-authentication"></a>Ověřování na základě souborů cookie

Když se uživatel přihlásí pomocí svého uživatelského jména a hesla, se už vystaví token, obsahující lístek pro ověřování, který slouží k ověřování a autorizaci. Token, který je uložen jako soubor cookie, který doprovází každého požadavku klienta. Generování a ověřování tento soubor cookie se provádí pomocí Middleware ověřování souborů Cookie. [Middleware](xref:fundamentals/middleware/index) serializuje hlavní název uživatele v podobě zašifrovaného souboru cookie. Na následné žádosti, middleware ověří souboru cookie, znovu vytvoří objekt zabezpečení a přiřadí instančního objektu pro [uživatele](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) vlastnost [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).

### <a name="token-based-authentication"></a>Ověřování založené na tokenech

Při ověření uživatele, že vystaví token (ne antiforgery token). Token, který obsahuje informace o uživateli ve formě [deklarace identity](/dotnet/framework/security/claims-based-identity-model) nebo odkaz na token, který odkazuje aplikaci na stav uživatele v aplikaci udržuje. Když se uživatel pokusí o přístup k prostředku, které vyžadují ověřování, token je odeslána do aplikace pomocí se další autorizační hlavička v podobě nosný token. Díky tomu aplikace bezstavové. V každé následné žádosti o token předaný požadavek pro ověřování na straně serveru. Tento token není *šifrované*; má *kódovaný*. Na serveru je dekódováno token pro přístup k jeho informace. Odeslat token na následné žádosti, ukládání tokenu v místním úložišti v prohlížeči. Nemusíte mít obavy o CSRF ohrožení zabezpečení, pokud token, který je uložený v místním úložišti v prohlížeči. CSRF je důležité, pokud token je uložen v souboru cookie.

### <a name="multiple-apps-hosted-at-one-domain"></a>Více aplikací hostovaných v jedné doméně

Sdílená hostitelská prostředí jsou citlivé na zneužití relace přihlášení CSRF a dalších útoků.

I když `example1.contoso.net` a `example2.contoso.net` jsou různých hostitelích, existuje implicitní důvěryhodnost vztah mezi hostiteli v rámci `*.contoso.net` domény. Tento vztah důvěryhodnosti implicitní umožňuje potenciálně nedůvěryhodní hostitelé ovlivnit druhé strany soubory cookie (stejného zdroje zásad, kterými se řídí odesílání požadavků AJAX není nemusí nezbytně vztahovat k soubory cookie protokolu HTTP).

Není sdílením domény jde zakázat útoků, které zneužívají důvěryhodných souborů cookie mezi aplikacemi, které jsou hostované ve stejné doméně. Při každé aplikaci, která je hostovaná ve vlastní doméně, neexistuje žádný vztah důvěry implicitní souboru cookie zneužít ohrožená místa.

## <a name="aspnet-core-antiforgery-configuration"></a>Konfigurace antiforgery ASP.NET Core

> [!WARNING]
> ASP.NET Core implementuje antiforgery pomocí [ochranu dat ASP.NET Core](xref:security/data-protection/introduction). Zásobník ochrany dat musí být nakonfigurováno pro práci v serverové farmě. Zobrazit [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview) Další informace.

V technologii ASP.NET Core 2.0 nebo novější [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) vkládá antiforgery tokeny do prvků formuláře HTML. Následující kód do souboru Razor automaticky generuje antiforgery tokeny:

```cshtml
<form method="post">
    ...
</form>
```

Wijsova, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generuje antiforgery tokeny ve výchozím nastavení, pokud není formuláře metoda GET.

Automatické generování tokenů antiforgery elementů formuláře HTML se stane, když `<form>` značka obsahuje `method="post"` platí atribut a jednu z následujících:

  * Atribut akce je prázdná (`action=""`).
  * Není zadán atribut akce (`<form method="post">`).

Automatické generování tokenů antiforgery elementů formuláře HTML se dají zakázat:

* Explicitně zakázat antiforgery tokeny se `asp-antiforgery` atribut:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* Element formuláře je se rozhodli nesouhlas pomocných rutin značek s využitím pomocné rutiny značky [! symbol Odhlásit](xref:mvc/views/tag-helpers/intro#opt-out):

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* Odeberte `FormTagHelper` ze zobrazení. `FormTagHelper` Je možné odebrat ze zobrazení tak, že přidáte následující direktiva zobrazení Razor:

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Stránky Razor](xref:razor-pages/index) automaticky chráněná před XSRF/CSRF. Další informace najdete v tématu [XSRF/CSRF a Razor Pages](xref:razor-pages/index#xsrf).

Nejběžnější přístup k obrana proti útokům CSRF je použít *Token vzor synchronizátor* (STP). STP se používá, když uživatel požádá o stránku s formuláři data:

1. Server odešle token přidružená k identitě aktuálního uživatele na klienta.
1. Klient odešle zpět token na server pro ověřování.
1. Pokud server přijme token, který neodpovídá identity ověřeného uživatele, žádost se zamítá.

Token, který je jedinečný a nepředvídatelné. Token, který je také možné zajistit správné sekvenční sérii požadavků (například pro zajištění posloupnost požadavku: stránka 1 &ndash; stránka 2 &ndash; stránce 3). Všechny formuláře v ASP.NET Core MVC a Razor Pages šablony generování antiforgery tokenů. Následující pár příkladů zobrazení generování antiforgery tokenů:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

Explicitně přidat antiforgery token `<form>` element bez použití pomocných rutin značek s pomocné rutiny HTML [ @Html.AntiForgeryToken ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

ASP.NET Core v každém z předchozích případech přidá skryté pole formuláře podobný následujícímu:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core obsahuje tři [filtry](xref:mvc/controllers/filters) pro práci s antiforgery tokeny:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Antiforgery možnosti

Přizpůsobení [antiforgery možnosti](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) v `Startup.ConfigureServices`:

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

&dagger;Nastavte antiforgery `Cookie` vlastností pomocí vlastnosti [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) třídy.

| Možnost | Popis |
| ------ | ----------- |
| [Soubor cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Určuje nastavení použité k vytvoření antiforgery soubory cookie. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Název skryté pole formuláře antiforgery systému použije k vykreslení antiforgery tokeny v zobrazeních. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Název hlavičky používá antiforgery systémem. Pokud `null`, systém bere v úvahu pouze data formuláře. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Určuje, jestli se má potlačit generování `X-Frame-Options` záhlaví. Ve výchozím nastavení je záhlaví generovány s hodnotou "SAMEORIGIN". Výchozí hodnota je `false`. |

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
| [Soubor cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Určuje nastavení použité k vytvoření antiforgery soubory cookie. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Doména souboru cookie. Výchozí hodnota je `null`. Tato vlastnost je zastaralá a bude v budoucí verzi odebrána. Doporučenou alternativou je Cookie.Domain. |
| [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Název souboru cookie. Pokud není nastavený, systém generuje jedinečný název začíná [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore.Antiforgery."). Tato vlastnost je zastaralá a bude v budoucí verzi odebrána. Doporučenou alternativou je Cookie.Name. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Cesta, nastavte v souboru cookie. Tato vlastnost je zastaralá a bude v budoucí verzi odebrána. Doporučenou alternativou je Cookie.Path. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Název skryté pole formuláře antiforgery systému použije k vykreslení antiforgery tokeny v zobrazeních. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Název hlavičky používá antiforgery systémem. Pokud `null`, systém bere v úvahu pouze data formuláře. |
| [Vlastnost RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Určuje, zda antiforgery systému vyžadují protokol HTTPS. Pokud `true`, neúspěšné požadavky bez HTTPS. Výchozí hodnota je `false`. Tato vlastnost je zastaralá a bude v budoucí verzi odebrána. Doporučenou alternativou je nastavit Cookie.SecurePolicy. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Určuje, jestli se má potlačit generování `X-Frame-Options` záhlaví. Ve výchozím nastavení je záhlaví generovány s hodnotou "SAMEORIGIN". Výchozí hodnota je `false`. |

::: moniker-end

Další informace najdete v tématu [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Nakonfigurovat IAntiforgery antiforgery funkce

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) poskytuje rozhraní API pro konfiguraci antiforgery funkcí. `IAntiforgery` můžete požádat v `Configure` metodu `Startup` třídy. Následující příklad používá middleware z domovské stránky aplikaci k vygenerování tokenu antiforgery a odeslat v odpovědi jako soubor cookie (pomocí výchozí Angular konvence je popsáno dále v tomto tématu):

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

### <a name="require-antiforgery-validation"></a>Vyžadování antiforgery ověření

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) je filtru akce, který lze použít na jednotlivé akci, kontroler, nebo globálně. Požadavky na akce, které mají tento filtr použít jsou blokovány, pokud požadavek obsahuje antiforgery platný token.

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

`ValidateAntiForgeryToken` Atribut vyžaduje token pro požadavky na metody akce upraví, včetně požadavků HTTP GET. Pokud `ValidateAntiForgeryToken` napříč řadičů aplikace je použit atribut, jeho monitorconfigurationoverride lze přepsat `IgnoreAntiforgeryToken` atribut.

> [!NOTE]
> ASP.NET Core nepodporuje přidávání antiforgery tokenů pro požadavky GET automaticky.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Automaticky ověřte antiforgery tokeny pro pouze nezabezpečené metody HTTP

Aplikace ASP.NET Core negenerovat antiforgery tokeny pro bezpečné metody HTTP (GET, HEAD, možnosti a trasování). Místo použití široce `ValidateAntiForgeryToken` atribut a pak jeho pomocí přepsání `IgnoreAntiforgeryToken` atributy, [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) atribut lze použít. Tento atribut funguje stejně jako `ValidateAntiForgeryToken` atribut, s tím rozdílem, že nevyžaduje tokeny pro žádosti pomocí následujících metod HTTP:

* GET
* HLAVNÍ
* MOŽNOSTI
* TRACE

Doporučujeme použití `AutoValidateAntiforgeryToken` široce pro scénáře – rozhraní API. Tím se zajistí, že jsou ve výchozím nastavení chráněna akce POST. Alternativou je ignorovat antiforgery tokeny ve výchozím nastavení, pokud `ValidateAntiForgeryToken` platí do jednotlivých metod akcí. To má pravděpodobně v tomto scénáři pro metodu akce POST zůstat nechráněné omylem, opuštění zranitelný vůči útokům CSRF aplikace. Všechny příspěvky by měli poslat antiforgery token.

Rozhraní API nemáte mechanismus automatického pro odesílání bez souborů cookie součást tokenu mechanismu. Implementace pravděpodobně závisí na implementaci kódu klienta. Níže je uvedeno několik příkladů:

Příklad úroveň třídy:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Globální příklad:

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a>Přepsat globální nebo antiforgery atributů kontroleru

[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filtru se používá k eliminuje nutnost antiforgery token pro danou akci (nebo řadič). Při použití, přepíše tento filtr `ValidateAntiForgeryToken` a `AutoValidateAntiforgeryToken` filtrů zadaných na vyšší úrovni (globálně nebo na řadiči).

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

## <a name="refresh-tokens-after-authentication"></a>Po ověření obnovovacích tokenů

Tokeny musí aktualizovat, po ověření uživatele přesměrováním uživatele na zobrazení nebo stránky Razor.

## <a name="javascript-ajax-and-spas"></a>JavaScript a AJAX, SPA

V tradičních aplikacích založený na jazyce HTML jsou předány antiforgery tokeny k serveru pomocí skrytého pole. V moderních aplikací založené na jazyce JavaScript a SPA velký počet požadavků probíhají prostřednictvím kódu programu. Tyto požadavky AJAX použít jiné techniky (například hlavičky požadavků nebo soubory cookie) odeslat token.

Pokud se soubory cookie se použijí k ukládání ověřovacích tokenů a k ověřování žádostí o rozhraní API na serveru, CSRF je potenciální problém. Pokud místní úložiště se používá k ukládání tokenu, CSRF ohrožení zabezpečení může zmírnit, protože hodnoty z místního úložiště se automaticky odeslány na server při každé žádosti. Díky tomu se pomocí místního úložiště k ukládání antiforgery token na klienta a odesílání tokenu hlavičky požadavku je doporučený postup.

### <a name="javascript"></a>JavaScript

Pomocí jazyka JavaScript se zobrazeními, token, který je možné vytvořit pomocí služby z v rámci zobrazení. Vložit [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) služby do zobrazení a volání [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Tento přístup se eliminuje potřeba řešit přímo ze serveru nastavení souborů cookie nebo čtení z klienta.

V předchozím příkladu používá JavaScript načíst hodnotu skryté pole pro hlavičku AJAX POST.

JavaScript lze také přístupové tokeny v souborech cookie a použít k vytvoření záhlaví s hodnotou tokenu souboru cookie obsah.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Za předpokladu, že skript žádostí o odeslání tokenu v záhlaví volá `X-CSRF-TOKEN`, konfigurovat službu antiforgery Hledat `X-CSRF-TOKEN` hlavičky:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

JavaScript provést požadavek AJAX s odpovídající hlavičku v následujícím příkladu:

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

AngularJS používá konvenci adresu CSRF. Pokud server odešle soubor cookie s názvem `XSRF-TOKEN`, AngularJS `$http` služby přidá hodnota souboru cookie pro záhlaví při odesílání požadavku na server. Tento proces je automatické. Záhlaví nemusí být explicitně nastaveno. Název záhlaví je `X-XSRF-TOKEN`. Server by měl zjistit této hlavičky a ověřit jeho obsah.

Pro rozhraní API pro ASP.NET Core pracovat s Tato konvence:

* Konfigurace aplikace pro poskytnout token v souboru cookie s názvem `XSRF-TOKEN`.
* Konfigurovat službu antiforgery hledat záhlaví s názvem `X-XSRF-TOKEN`.

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
```

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="extend-antiforgery"></a>Rozšíření antiforgery

[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) typ umožňuje vývojářům rozšířit chování systému anti-CSRF verzemi další data v jednotlivých tokenu. [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) metoda je volána pokaždé, když se vygeneruje token pole, a návratová hodnota je vložený v rámci vygenerovaný token. Implementátora může vrátit časové razítko, hodnotu nonce nebo jakoukoli jinou hodnotu a pak vyvolejte [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) ověřit data při ověření tokenu. Uživatelské jméno klienta je již součástí do generovaných tokenů, takže není nutné zahrnout tyto informace. Pokud token obsahuje doplňková data, ale ne `IAntiForgeryAdditionalDataProvider` je nakonfigurován, nejsou dodatečná data ověřena.

## <a name="additional-resources"></a>Další zdroje

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) na [otevřete projekt webové aplikace zabezpečení](https://www.owasp.org/index.php/Main_Page) (OWASP).
* <xref:host-and-deploy/web-farm>
