---
title: Povolit žádosti mezi zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak CORS jako standard pro povolení nebo odmítnutí žádostí o více zdrojů v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: e0e0e1abf1ecaa12038b3ee1bdaa384d979be254
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666248"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Povolit žádosti mezi zdroji (CORS) v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality. V některých případech můžete chtít, aby ostatní weby ve vaší aplikaci provedly žádosti o více zdrojů. Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).

[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):

* Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.
* Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit. Rozhraní API není bezpečnější díky povolení CORS. Další informace najdete v tématu [jak CORS funguje](#how-cors).
* Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.
* Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Stejný původ

Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Tyto dvě adresy URL mají stejný původ:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Tyto adresy URL mají různé zdroje, než jsou předchozí dvě adresy URL:

* `https://example.net` &ndash; jiné doméně
* `https://www.example.com/foo.html` &ndash; různé subdomény
* `http://example.com/foo.html` &ndash; různých schémat
* `https://example.com:9000/foo.html` &ndash; jiný port

Internet Explorer při porovnávání zdrojů nebere v úvahu port.

## <a name="cors-with-named-policy-and-middleware"></a>CORS s pojmenovanými zásadami a middlewarem

Middleware CORS zpracovává požadavky mezi zdroji. Následující kód umožňuje CORS pro celou aplikaci se zadaným počátkem:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Předchozí kód:

* Nastaví název zásady na "\_myAllowSpecificOrigins". Název zásady je libovolný.
* Volá metodu rozšíření <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>, která umožňuje CORS.
* Volá <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambda převezme objekt <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>. [Možnosti konfigurace](#cors-policy-options), například `WithOrigins`, jsou popsány dále v tomto článku.

Volání metody <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> přidá služby CORS do kontejneru služby aplikace:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.

Metoda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> může řetězit metody, jak je znázorněno v následujícím kódu:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Poznámka: adresa URL nesmí **obsahovat koncové** lomítko (`/`). Pokud se adresa URL ukončí s `/`, porovnávání vrátí `false` a nevrátí se žádné záhlaví.

::: moniker range=">= aspnetcore-3.0"

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a>Použití zásad CORS u všech koncových bodů

Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím middlewaru CORS:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> Při směrování koncových bodů musí být middleware CORS nakonfigurované tak, aby se spouštěla mezi voláními `UseRouting` a `UseEndpoints`. Nesprávná konfigurace způsobí, že middleware přestane fungovat správně.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím middlewaru CORS:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Poznámka: před `UseMvc`je nutné volat `UseCors`.

::: moniker-end

Viz [Povolení CORS v Razor Pages, řadičích a metodách akcí](#ecors) pro aplikování zásad CORS na úrovni stránky, řadiče nebo akce.

Pokyny k testování předchozího kódu najdete v části [test CORS](#test) .

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a>Povolení CORS s směrováním koncových bodů

S směrováním koncových bodů je možné CORS povolit na základě jednotlivých koncových bodů pomocí `RequireCors` sady rozšiřujících metod.

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

Podobně lze CORS povolit také pro všechny řadiče:

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a>Povolení CORS s atributy

Atribut [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) poskytuje alternativu k použití CORS globálně. Atribut `[EnableCors]` povoluje CORS pro vybrané koncové body místo všech koncových bodů.

Pomocí `[EnableCors]` můžete zadat výchozí zásady a `[EnableCors("{Policy String}")]` zadat zásadu.

Atribut `[EnableCors]` lze použít pro:

* `PageModel` stránky Razor
* Kontrolér
* Metoda akce kontroleru

Pomocí atributu `[EnableCors]` můžete použít různé zásady na řadič nebo stránku-model/akce. Pokud je atribut `[EnableCors]` aplikován na řadiče/stránky – model/akce a v middleware je povolená CORS, uplatní se obě zásady. Doporučujeme před kombinováním zásad. Použijte atribut `[EnableCors]` nebo middleware, nikoli oba ve stejné aplikaci.

Následující kód používá pro každou metodu jinou zásadu:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Následující kód vytvoří výchozí zásadu CORS a zásadu s názvem `"AnotherPolicy"`:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Zakázání CORS

Atribut [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) zakáže CORS pro kontroler/Page-model/Action.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Možnosti zásad CORS

Tato část popisuje různé možnosti, které je možné nastavit v zásadách CORS:

* [Nastavení povolených zdrojů](#set-the-allowed-origins)
* [Nastavení povolených metod HTTP](#set-the-allowed-http-methods)
* [Nastavení povolených hlaviček žádosti](#set-the-allowed-request-headers)
* [Nastavení hlaviček vystavené odpovědi](#set-the-exposed-response-headers)
* [Přihlašovací údaje v žádostech mezi zdroji](#credentials-in-cross-origin-requests)
* [Nastavit čas vypršení platnosti předběžné kontroly](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> se volá v `Startup.ConfigureServices`. U některých možností může být užitečné si nejdřív přečíst oddíl [jak CORS funguje](#how-cors) .

## <a name="set-the-allowed-origins"></a>Nastavení povolených zdrojů

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem (`http` nebo `https`). `AllowAnyOrigin` je nezabezpečený, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> Zadání `AllowAnyOrigin` a `AllowCredentials` je nezabezpečená konfigurace a může vést k padělání požadavků mezi lokalitami. Služba CORS vrátí neplatnou odpověď CORS, pokud je aplikace nakonfigurovaná pomocí obou metod.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> Zadání `AllowAnyOrigin` a `AllowCredentials` je nezabezpečená konfigurace a může vést k padělání požadavků mezi lokalitami. V případě zabezpečené aplikace zadejte přesný seznam zdrojů, pokud je klient musí autorizovat pro přístup k prostředkům serveru.

::: moniker-end

`AllowAnyOrigin` má vliv na požadavky na kontrolu a `Access-Control-Allow-Origin` hlavičku. Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; nastaví vlastnost <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> zásady tak, aby byla funkce, která umožňuje, aby se zdroje shodovaly s konfigurovanou doménou se zástupnými znaky při vyhodnocování, jestli je původ povolený.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>Nastavení povolených metod HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Umožňuje jakoukoli metodu HTTP:
* Má vliv na požadavky na kontrolu a `Access-Control-Allow-Methods` hlavičku. Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Nastavení povolených hlaviček žádosti

Chcete-li povolit odeslání konkrétních hlaviček v žádosti CORS s názvem *záhlaví žádosti o autora*, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Chcete-li povolení všech hlaviček žádostí o autora, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Toto nastavení má vliv na žádosti o kontrolu a `Access-Control-Request-Headers` hlavičku. Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .

::: moniker range=">= aspnetcore-2.2"

Zásada middlewaru CORS odpovídá konkrétním hlavičkám určeným `WithHeaders` je možné pouze v případě, že hlavičky odeslané `Access-Control-Request-Headers` přesně odpovídají hlavičkám uvedeným v `WithHeaders`.

Představte si například aplikaci nakonfigurovanou takto:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Middleware CORS odmítá požadavek na kontrolu před výstupem s následující hlavičkou požadavku, protože `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) není uveden v `WithHeaders`:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky. Proto prohlížeč nezkouší požadavek mezi zdroji.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Middleware CORS vždycky povoluje posílání čtyř hlaviček v `Access-Control-Request-Headers` bez ohledu na hodnoty nakonfigurované v CorsPolicy. Headers. Tento seznam hlaviček obsahuje:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Představte si například aplikaci nakonfigurovanou takto:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Middleware CORS odpoví úspěšně do žádosti o kontrolu před výstupem s následující hlavičkou požadavku, protože `Content-Language` je vždy na seznamu povolených:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>Nastavení hlaviček vystavené odpovědi

Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace. Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): jednoduchá hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).

Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*. K zpřístupnění dalších hlaviček pro aplikaci volejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Přihlašovací údaje v žádostech mezi zdroji

Přihlašovací údaje vyžadují zvláštní zpracování v žádosti CORS. V prohlížeči se ve výchozím nastavení neodesílají přihlašovací údaje s žádostí o více zdrojů. Přihlašovací údaje zahrnují soubory cookie a schémata ověřování HTTP. Aby bylo možné odesílat přihlašovací údaje pomocí žádosti o více zdrojů, musí klient nastavit `XMLHttpRequest.withCredentials` `true`.

Přímé použití `XMLHttpRequest`:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Pomocí jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Server musí přihlašovací údaje umožňovat. Pokud chcete povolení přihlašovacích údajů pro více zdrojů, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

Odpověď HTTP obsahuje hlavičku `Access-Control-Allow-Credentials`, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.

Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou hlavičku `Access-Control-Allow-Credentials`, prohlížeč nezveřejňuje odpověď na aplikaci a žádost o více zdrojů se nezdařila.

Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko. Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Specifikace CORS také uvádí, že nastavení původes na `"*"` (všechny zdroje) je neplatné, pokud je k dispozici `Access-Control-Allow-Credentials` záhlaví.

### <a name="preflight-requests"></a>Požadavky na kontrolu před výstupem

U některých požadavků CORS prohlížeč před provedením samotného požadavku pošle další požadavek. Tento požadavek se nazývá *žádost o kontrolu před výstupem*. Prohlížeč může požadavek na předběžné kontroly přeskočit, pokud jsou splněné následující podmínky:

* Metoda Request je GET, HEAD nebo POST.
* Aplikace nenastaví záhlaví žádostí kromě `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`nebo `Last-Event-ID`.
* `Content-Type` záhlaví, pokud je nastaveno, má jednu z následujících hodnot:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada App Sets nastaví voláním `setRequestHeader` na objektu `XMLHttpRequest`. Specifikace CORS volá *záhlaví požadavku autora*záhlaví. Toto pravidlo se nevztahuje na záhlaví, která může prohlížeč nastavit, například `User-Agent`, `Host`nebo `Content-Length`.

Následuje příklad žádosti o kontrolu před výstupem:

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

Požadavek na lety používá metodu HTTP. Obsahuje dvě speciální hlavičky:

* `Access-Control-Request-Method`: metoda HTTP, která se bude používat pro skutečný požadavek.
* `Access-Control-Request-Headers`: seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku. Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent`.

Požadavek předběžné kontroly CORS může zahrnovat hlavičku `Access-Control-Request-Headers`, která serveru oznamuje hlavičkám, které jsou odesílány se skutečným požadavkem.

Pro povolení konkrétních hlaviček volejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Chcete-li povolení všech hlaviček žádostí o autora, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Prohlížeče nejsou zcela konzistentní v tom, jak nastavují `Access-Control-Request-Headers`. Pokud jste nastavili záhlaví na jinou hodnotu než `"*"` (nebo používáte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), měli byste zahrnout alespoň `Accept`, `Content-Type`a `Origin`a také všechna vlastní záhlaví, která chcete podporovat.

Následuje příklad odpovědi na žádost o kontrolu před výstupem (za předpokladu, že server povoluje požadavek):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

Odpověď obsahuje `Access-Control-Allow-Methods` záhlaví, ve kterém jsou uvedeny povolené metody a volitelně `Access-Control-Allow-Headers` záhlaví, které obsahuje seznam povolených hlaviček. Pokud je žádost o kontrolu před výstupem úspěšná, prohlížeč pošle skutečný požadavek.

Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky. Proto prohlížeč nezkouší požadavek mezi zdroji.

### <a name="set-the-preflight-expiration-time"></a>Nastavit čas vypršení platnosti předběžné kontroly

Hlavička `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti. Chcete-li nastavit tuto hlavičku, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Jak CORS funguje

Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.

* CORS není **funkce** zabezpečení. CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.
  * Škodlivý objekt actor by například mohl použít možnost [zabránit skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s povoleným CORS, aby mohl ukrást informace.
* Vaše rozhraní API není bezpečnější díky povolení CORS.
  * Pro vymáhání CORS je to až klient (prohlížeč). Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď. Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Webový prohlížeč zadáním adresy URL do panelu Adresa.
* To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.
  * Prohlížeče (bez CORS) nemůžou provádět žádosti mezi zdroji. Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) . JSONP nepoužívá XHR, používá značku `<script>` k přijetí odpovědi. Skripty mohou být načteny mezi zdroji.

[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji. Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky. Pro povolení CORS není nutný vlastní kód JavaScriptu.

Následuje příklad žádosti o více zdrojů. Hlavička `Origin` poskytuje doménu lokality, ze které se vytváří požadavek. Hlavička `Origin` je povinná a musí se lišit od hostitele.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Pokud server tuto žádost povoluje, nastaví v odpovědi hlavičku `Access-Control-Allow-Origin`. Hodnota této hlavičky se buď shoduje s hlavičkou `Origin` z požadavku, nebo se jedná o zástupnou hodnotu `"*"`, což znamená, že je povolený libovolný původ:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Pokud odpověď neobsahuje hlavičku `Access-Control-Allow-Origin`, požadavek na více zdrojů se nezdařil. Konkrétně prohlížeč požadavek nepovoluje. I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.

<a name="test"></a>

## <a name="test-cors"></a>Test CORS

Testování CORS:

1. [Vytvořte projekt API](xref:tutorials/first-web-api). Alternativně si můžete [Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Povolte CORS pomocí jednoho z přístupů v tomto dokumentu. Příklad:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` by se měla použít jenom pro testování ukázkové aplikace podobné [ukázkovému kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Vytvořte projekt webové aplikace (Razor Pages nebo MVC). Ukázka používá Razor Pages. Webovou aplikaci můžete vytvořit ve stejném řešení jako projekt rozhraní API.
1. Do souboru *index. cshtml* přidejte následující zvýrazněný kód:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresou URL nasazené aplikace.
1. Nasaďte projekt API. Nasaďte například [do Azure](xref:host-and-deploy/azure-apps/index).
1. Spusťte aplikaci Razor Pages nebo MVC z plochy a klikněte na tlačítko **test** . Pomocí nástrojů F12 zkontrolujte chybové zprávy.
1. Odeberte původ localhost z `WithOrigins` a aplikaci nasaďte. Případně spusťte klientskou aplikaci s jiným portem. Například spusťte ze sady Visual Studio.
1. Otestujte pomocí klientské aplikace. Chyby CORS vracejí chybu, ale chybová zpráva není k dispozici pro JavaScript. K zobrazení chyby použijte kartu konzola v nástrojích F12. V závislosti na prohlížeči se zobrazí chyba (v konzole nástrojů F12), která je podobná následující:

   * Používání Microsoft Edge:

     **SEC7120: [CORS] původ `https://localhost:44375` nebyl nalezen `https://localhost:44375` v hlavičce odpovědi Access-Control-Allow-Origin pro prostředek pro více zdrojů, v `https://webapi.azurewebsites.net/api/values/1`**

   * Použití Chrome:

     **Zásada CORS zablokovala přístup k XMLHttpRequest v `https://webapi.azurewebsites.net/api/values/1` od původu `https://localhost:44375`: v požadovaném prostředku není k dispozici hlavička Access-Control-Allow-Origin.**
     
Koncové body s podporou CORS se dají testovat pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [post](https://www.getpostman.com/). Při použití nástroje se musí původ požadavku určeného hlavičkou `Origin` lišit od hostitele, který požadavek přijal. Pokud požadavek není *mezi zdroji* založen na hodnotě hlavičky `Origin`:

* Pro zpracování žádosti není nutné middleware CORS.
* V odpovědi nejsou vraceny hlavičky CORS.

## <a name="cors-in-iis"></a>CORS ve službě IIS

Při nasazování do služby IIS musí CORS běžet před ověřováním systému Windows, pokud server není nakonfigurovaný tak, aby povoloval anonymní přístup. Pro podporu tohoto scénáře je nutné nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.

## <a name="additional-resources"></a>Další zdroje

* [Sdílení prostředků mezi zdroji (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Začínáme s modulem IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)
