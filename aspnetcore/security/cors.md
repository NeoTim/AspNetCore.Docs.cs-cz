---
title: Povolit žádosti mezi zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak CORS jako standard pro povolení nebo odmítnutí žádostí o více zdrojů v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: security/cors
ms.openlocfilehash: a02b3497684979c1a9e792437f9f1a4c467600f0
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187251"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Povolit žádosti mezi zdroji (CORS) v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality. V některých případech můžete chtít, aby ostatní weby ve vaší aplikaci provedly žádosti o více zdrojů. Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).

[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):

* Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.
* Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit. Rozhraní API není bezpečnější díky povolení CORS. Další informace najdete v tématu [jak CORS funguje](#how-cors).
* Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.
* Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Stejný původ

Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Tyto dvě adresy URL mají stejný původ:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Tyto adresy URL mají různé zdroje, než jsou předchozí dvě adresy URL:

* `https://example.net`&ndash; Jiná doména
* `https://www.example.com/foo.html`&ndash; Jiná subdoména
* `http://example.com/foo.html`&ndash; Jiné schéma
* `https://example.com:9000/foo.html`&ndash; Jiný port

Internet Explorer při porovnávání zdrojů nebere v úvahu port.

## <a name="cors-with-named-policy-and-middleware"></a>CORS s pojmenovanými zásadami a middlewarem

Middleware CORS zpracovává požadavky mezi zdroji. Následující kód umožňuje CORS pro celou aplikaci se zadaným počátkem:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Předchozí kód:

* Nastaví název zásady na "\_myAllowSpecificOrigins". Název zásady je libovolný.
* Volá metodu <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> rozšíření, která umožňuje CORS.
* Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambda převezme <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objekt. [Možnosti konfigurace](#cors-policy-options), například `WithOrigins`, jsou popsány dále v tomto článku.

Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody přidá služby CORS do kontejneru služby aplikace:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Metoda může řetězit metody, jak je znázorněno v následujícím kódu:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Poznámka: Adresa URL nesmí **obsahovat koncové** lomítko (`/`). Pokud adresa URL končí `/`, porovnávání se vrátí `false` a nevrátí se žádné záhlaví.

::: moniker range=">= aspnetcore-3.0"

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
> Při směrování koncových bodů musí být middleware CORS nakonfigurované tak, aby se `UseRouting` spustilo mezi voláními a `UseEndpoints`. Nesprávná konfigurace způsobí, že middleware přestane fungovat správně.

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
Poznámka: `UseCors` musí být volána před `UseMvc`.

::: moniker-end

Viz [Povolení CORS v Razor Pages, řadičích a metodách akcí](#ecors) pro aplikování zásad CORS na úrovni stránky, řadiče nebo akce.

Pokyny k testování předchozího kódu najdete v části [test CORS](#test) .

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a>Povolení CORS s směrováním koncových bodů

S směrováním koncových bodů je možné CORS povolit na základě jednotlivých koncových bodů `RequireCors` pomocí sady rozšiřujících metod.

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

Atribut [EnableCors&rbrack;představuje alternativu k použití CORS globálně. &lbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) `[EnableCors]` Atribut povoluje CORS pro vybrané koncové body místo všech koncových bodů.

Slouží `[EnableCors]` k zadání výchozích zásad a `[EnableCors("{Policy String}")]` k určení zásad.

`[EnableCors]` Atribut lze použít pro:

* Stránka Razor`PageModel`
* kontrolér
* Metoda akce kontroleru

S `[EnableCors]` atributem lze použít různé zásady pro kontroler/Page-model/Action. Pokud je `[EnableCors]` atribut použit na řadičích, na úrovni stránky nebo na metodu a akci a v middlewaru je povoleno CORS, jsou obě zásady aplikovány. Doporučujeme před kombinováním zásad. `[EnableCors]` Použijte atribut nebo middleware, nikoli oba ve stejné aplikaci.

Následující kód používá pro každou metodu jinou zásadu:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Následující kód vytvoří výchozí zásadu CORS a zásadu s názvem `"AnotherPolicy"`:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Zakázání CORS

Atribut [DisableCors&rbrack;zakáže CORS pro kontroler/Page-model/Action. &lbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Možnosti zásad CORS

Tato část popisuje různé možnosti, které je možné nastavit v zásadách CORS:

* [Nastavení povolených zdrojů](#set-the-allowed-origins)
* [Nastavení povolených metod HTTP](#set-the-allowed-http-methods)
* [Nastavení povolených hlaviček žádosti](#set-the-allowed-request-headers)
* [Nastavení hlaviček vystavené odpovědi](#set-the-exposed-response-headers)
* [Přihlašovací údaje v žádostech mezi zdroji](#credentials-in-cross-origin-requests)
* [Nastavit čas vypršení platnosti předběžné kontroly](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je volána v `Startup.ConfigureServices`. U některých možností může být užitečné si nejdřív přečíst oddíl [jak CORS funguje](#how-cors) .

## <a name="set-the-allowed-origins"></a>Nastavení povolených zdrojů

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>Povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem`http` ( `https`nebo). &ndash; `AllowAnyOrigin`je nezabezpečené, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> Určení `AllowAnyOrigin` a`AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby. Služba CORS vrátí neplatnou odpověď CORS, pokud je aplikace nakonfigurovaná pomocí obou metod.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> Určení `AllowAnyOrigin` a`AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby. V případě zabezpečené aplikace zadejte přesný seznam zdrojů, pokud je klient musí autorizovat pro přístup k prostředkům serveru.

::: moniker-end

`AllowAnyOrigin`má vliv na požadavky na `Access-Control-Allow-Origin` kontrolu a hlavičku. Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Nastaví vlastnostzásadynafunkci,kteráumožňuje,abysepřivyhodnocování,jestlijepůvodpovolený,shodovalyskonfigurovanoudoménouse<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> zástupnými znaky.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>Nastavení povolených metod HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Umožňuje jakoukoli metodu HTTP:
* Má vliv na požadavky na `Access-Control-Allow-Methods` kontrolu a hlavičku. Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Nastavení povolených hlaviček žádosti

Pokud chcete povolit odeslání konkrétních hlaviček v žádosti CORS s názvem *záhlaví žádosti o autora*, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Pokud chcete povolení všech hlaviček žádostí o autora <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>, zavolejte:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Toto nastavení má vliv na `Access-Control-Request-Headers` žádosti o předběžné kontroly a hlavičku. Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .

::: moniker range=">= aspnetcore-2.2"

Zásada middlewaru CORS odpovídající konkrétním hlavičkám, které `WithHeaders` určuje, je možná jenom v případě, `Access-Control-Request-Headers` že se záhlaví poslala přesně `WithHeaders`a odpovídají hlavičkám uvedeným v.

Představte si například aplikaci nakonfigurovanou takto:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Middleware CORS odmítá požadavek na předběžné kontroly s následující hlavičkou `Content-Language` požadavku, protože ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) `WithHeaders`není uvedená v tomto seznamu:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky. Proto prohlížeč nezkouší požadavek mezi zdroji.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Middleware CORS vždycky povoluje posílání čtyř `Access-Control-Request-Headers` hlaviček v, bez ohledu na hodnoty nakonfigurované v CorsPolicy. Headers. Tento seznam hlaviček obsahuje:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Představte si například aplikaci nakonfigurovanou takto:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Middleware CORS odpoví úspěšně do žádosti o kontrolu před výstupem s následující `Content-Language` hlavičkou požadavku, protože je vždy na seznamu povolených:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>Nastavení hlaviček vystavené odpovědi

Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace. Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): Jednoduchá hlavička](https://www.w3.org/TR/cors/#simple-response-header)odpovědi

Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*. Pokud chcete, aby byla aplikace k dispozici ostatním <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>hlavičkám, zavolejte:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Přihlašovací údaje v žádostech mezi zdroji

Přihlašovací údaje vyžadují zvláštní zpracování v žádosti CORS. V prohlížeči se ve výchozím nastavení neodesílají přihlašovací údaje s žádostí o více zdrojů. Přihlašovací údaje zahrnují soubory cookie a schémata ověřování HTTP. Aby bylo možné odesílat přihlašovací údaje pomocí žádosti o více zdrojů, musí být `XMLHttpRequest.withCredentials` klient `true`nastaven na.

Přímé `XMLHttpRequest` použití:

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

Server musí přihlašovací údaje umožňovat. Pokud chcete povolení přihlašovacích údajů mezi zdroji <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>, zavolejte:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` hlavičku, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.

Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou `Access-Control-Allow-Credentials` hlavičku, prohlížeč nezveřejňuje odpověď na aplikaci a žádost o více zdrojů se nezdařila.

Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko. Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Specifikace CORS také uvádí, že `"*"` `Access-Control-Allow-Credentials` Pokud je hlavička k dispozici, nastavení původu na (všechny zdroje) je neplatné.

### <a name="preflight-requests"></a>Požadavky na kontrolu před výstupem

U některých požadavků CORS prohlížeč před provedením samotného požadavku pošle další požadavek. Tento požadavek se nazývá *žádost o kontrolu před výstupem*. Prohlížeč může požadavek na předběžné kontroly přeskočit, pokud jsou splněné následující podmínky:

* Metoda Request je GET, HEAD nebo POST.
* Aplikace nenastaví záhlaví `Accept`žádostí s výjimkou, `Accept-Language`, `Content-Language` `Content-Type`, nebo `Last-Event-ID`.
* `Content-Type` Záhlaví, pokud je nastaveno, má jednu z následujících hodnot:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada aplikací zavolá `setRequestHeader` `XMLHttpRequest` na objekt. Specifikace CORS volá *záhlaví požadavku autora*záhlaví. Pravidlo se nevztahuje na hlavičky, které může prohlížeč nastavit, například `User-Agent`, `Host`nebo `Content-Length`.

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

* `Access-Control-Request-Method`: Metoda HTTP, která se bude používat pro skutečný požadavek.
* `Access-Control-Request-Headers`: Seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku. Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent`.

Požadavek předběžné kontroly CORS může zahrnovat `Access-Control-Request-Headers` hlavičku, která serveru oznamuje hlavičkám, které jsou odesílány se skutečným požadavkem.

Pro povolení konkrétních hlaviček zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Pokud chcete povolení všech hlaviček žádostí o autora <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>, zavolejte:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Prohlížeče nejsou zcela konzistentní v tom, jak `Access-Control-Request-Headers`nastavily. Pokud nastavíte záhlaví na jinou hodnotu než `"*"` (nebo použít <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), měli byste zahrnout aspoň `Accept`, `Content-Type`a a `Origin`také libovolné vlastní hlavičky, které chcete podporovat.

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

Odpověď obsahuje `Access-Control-Allow-Methods` hlavičku, která obsahuje seznam povolených metod a `Access-Control-Allow-Headers` volitelně záhlaví, ve kterém jsou uvedeny povolené hlavičky. Pokud je žádost o kontrolu před výstupem úspěšná, prohlížeč pošle skutečný požadavek.

Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky. Proto prohlížeč nezkouší požadavek mezi zdroji.

### <a name="set-the-preflight-expiration-time"></a>Nastavit čas vypršení platnosti předběžné kontroly

Záhlaví `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti. Chcete-li nastavit tuto hlavičku <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>, zavolejte:

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
  * Prohlížeče (bez CORS) nemůžou provádět žádosti mezi zdroji. Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) . JSONP nepoužívá XHR, používá `<script>` značku k přijetí odpovědi. Skripty mohou být načteny mezi zdroji.

[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji. Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky. Pro povolení CORS není nutný vlastní kód JavaScriptu.

Následuje příklad žádosti o více zdrojů. `Origin` Hlavička poskytuje doménu webu, který vytváří požadavek:

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

Pokud server tuto žádost povoluje, nastaví `Access-Control-Allow-Origin` hlavičku v odpovědi. Hodnota této hlavičky se shoduje s `Origin` hlavičkou z požadavku nebo se jedná o zástupnou hodnotu `"*"`, což znamená, že je povolen libovolný původ:

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

Pokud odpověď nezahrnuje `Access-Control-Allow-Origin` hlavičku, požadavek na více zdrojů se nezdařil. Konkrétně prohlížeč požadavek nepovoluje. I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.

<a name="test"></a>

## <a name="test-cors"></a>Test CORS

Testování CORS:

1. [Vytvořte projekt API](xref:tutorials/first-web-api). Alternativně si můžete [Stáhnout ukázku](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Povolte CORS pomocí jednoho z přístupů v tomto dokumentu. Příklad:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`by se mělo používat jenom pro testování ukázkové aplikace, podobně jako u [ukázkového kódu ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Vytvořte projekt webové aplikace (Razor Pages nebo MVC). Ukázka používá Razor Pages. Webovou aplikaci můžete vytvořit ve stejném řešení jako projekt rozhraní API.
1. Do souboru *index. cshtml* přidejte následující zvýrazněný kód:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresu URL nasazené aplikace.
1. Nasaďte projekt API. Nasaďte například [do Azure](xref:host-and-deploy/azure-apps/index).
1. Spusťte aplikaci Razor Pages nebo MVC z plochy a klikněte na tlačítko **test** . Pomocí nástrojů F12 zkontrolujte chybové zprávy.
1. Odeberte původ localhost z `WithOrigins` a nasaďte aplikaci. Případně spusťte klientskou aplikaci s jiným portem. Například spusťte ze sady Visual Studio.
1. Otestujte pomocí klientské aplikace. Chyby CORS vracejí chybu, ale chybová zpráva není k dispozici pro JavaScript. K zobrazení chyby použijte kartu konzola v nástrojích F12. V závislosti na prohlížeči se zobrazí chyba (v konzole nástrojů F12), která je podobná následující:

   * Používání Microsoft Edge:

     **SEC7120: [CORS] původ `https://localhost:44375` nebyl nalezen `https://localhost:44375` v hlavičce odpovědi Access-Control-Allow-Origin pro prostředek pro více zdrojů.`https://webapi.azurewebsites.net/api/values/1`**

   * Použití Chrome:

     **Zásada CORS zablokovala přístup `https://localhost:44375` k XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` od původu: U požadovaného prostředku není k dispozici hlavička Access-Control-Allow-Origin.**

## <a name="additional-resources"></a>Další zdroje

* [Sdílení prostředků mezi zdroji (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
