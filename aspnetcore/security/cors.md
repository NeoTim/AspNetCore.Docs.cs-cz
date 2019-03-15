---
title: Povolení žádostí napříč zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak CORS jako standard pro povolení nebo odmítnutí žádostí nepůvodního v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/27/2019
uid: security/cors
ms.openlocfilehash: 6be8b4da1642a9eff021371c229a17071d6e9bfb
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978468"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Povolení žádostí napříč zdroji (CORS) v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento článek popisuje postup povolení CORS v aplikaci ASP.NET Core.

Zabezpečení prohlížečů brání zasílání požadavků na jiné doméně než ten, který obsluhuje webovou stránku na webové stránce. Toto omezení je volána *zásada stejného zdroje*. Zásada stejného zdroje brání škodlivým webům ve čtení citlivých dat z jiné lokality. V některých případech můžete chtít povolit, že ostatní lokality provádět požadavky cross-origin do vaší aplikace. Další informace najdete v tématu [Mozilla CORS článku](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).

[Pro různé zdroje sdílení prostředků](https://www.w3.org/TR/cors/) (CORS):

* Je standard, která umožňuje server zmírnit zásadu stejného zdroje W3C.
* Je **není** funkce zabezpečení, CORS zmírňuje zabezpečení. Rozhraní API není bezpečnější povolením CORS. Další informace najdete v tématu [funguje jak CORS](#how-cors).
* Umožňuje serveru tak, aby výslovně povolit některé požadavky cross-origin, zatímco jiné odmítnout.
* Je bezpečnější a flexibilnější, než starší techniky, jako například [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Stejného původu

Pokud mají stejné schémata, hostitele a porty mít dvě adresy URL stejného původu ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Tyto dvě adresy URL mají stejného původu:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Tyto adresy URL mají různé zdroje než předchozí dvě adresy URL:

* `https://example.net` &ndash; Jiné domény
* `https://www.example.com/foo.html` &ndash; Different subdomain
* `http://example.com/foo.html` &ndash; Jiné schéma
* `https://example.com:9000/foo.html` &ndash; Jiný port

Aplikace Internet Explorer nezahrne port při porovnání zdrojů.

## <a name="cors-with-named-policy-and-middleware"></a>S s názvem zásady a middlewarem CORS

CORS Middleware zpracovává požadavky cross-origin. Následující kód umožňuje použití CORS pro celou aplikaci se zadaném umístění:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Předchozí kód:

* Nastaví název zásady "\_myAllowSpecificOrigins". Název zásad je volitelný.
* Volání <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> rozšiřující metoda, která umožňuje jader.
* Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výraz lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Používá výraz lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objektu. [Možnosti konfigurace](#cors-policy-options), jako například `WithOrigins`, jsou popsány dále v tomto článku.

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Volání metody přidá CORS služby do aplikace služby kontejneru:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Další informace najdete v tématu [možnosti zásad CORS](#cpo) v tomto dokumentu.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Metoda můžete zřetězit metod, jak je znázorněno v následujícím kódu:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Následující zvýrazněný kód platí zásady CORS pro všechny koncové body pro aplikace přes CORS Middleware:

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

Zobrazit [povolení CORS v stránky Razor, kontrolerů a metod akcí](#ecors) použít zásady CORS na úrovni stránky nebo kontroler nebo akce.

Poznámka:

* `UseCors` musí být volána před `UseMvc`.
* Adresa URL musí **není** obsahovat koncové lomítko (`/`). Pokud adresa URL se ukončí s `/`, porovnání vrátí `false` a vrátí se bez záhlaví.

Zobrazit [Test CORS](#test) pokyny k testování předchozí kód.

<a name="ecors"></a>

## <a name="enable-cors-with-attributes"></a>Povolení CORS s atributy

[ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) atribut poskytuje alternativu k použití CORS globálně. `[EnableCors]` Atribut umožňuje použití CORS pro vybrané koncové body, nikoli všechny koncové body.

Použití `[EnableCors]` určit výchozí zásady a `[EnableCors("{Policy String}")]` zadat zásady.

`[EnableCors]` Atribut lze použít pro:

* Stránka Razor `PageModel`
* Kontroler
* Metoda akce kontroleru

Můžete použít různé zásady/stránky modelu/akce kontroleru se `[EnableCors]` atribut. Když `[EnableCors]` atribut je použít pro metodu řadiče/akce/stránky – model a CORS je povolený v middlewaru, obě zásady se použijí. Nedoporučujeme kombinování zásad. Použití `[EnableCors]` atribut nebo middleware, nikoli oba současně ve stejné aplikaci.

Následující kód platí jiné zásady pro jednotlivé metody:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Následující kód vytvoří výchozí zásady CORS a zásady s názvem `"AnotherPolicy"`:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Zákazu sdílení CORS

[ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) atribut zakáže CORS pro/stránky modelu/akce kontroleru.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Možnosti zásad CORS

Tato část popisuje různé možnosti, které je možné nastavit v zásadu CORS:

* [Nastavte povolené zdroje](#set-the-allowed-origins)
* [Nastavte povolené metody HTTP](#set-the-allowed-http-methods)
* [Nastavit hlavičku povolené žádosti](#set-the-allowed-request-headers)
* [Nastavit hlavičky vystavené odpovědi](#set-the-exposed-response-headers)
* [Přihlašovací údaje v požadavky cross-origin](#credentials-in-cross-origin-requests)
* [Nastavit čas vypršení platnosti předběžné](#set-the-preflight-expiration-time)

 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> je volána `Startup.ConfigureServices`. Pro některé možnosti, může být užitečné ke čtení [funguje jak CORS](#how-cors) první části.

## <a name="set-the-allowed-origins"></a>Nastavte povolené zdroje

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Umožňuje požadavků CORS z všechny původy žádné schéma (`http` nebo `https`). `AllowAnyOrigin` není bezpečné a protože *všechny weby* může aplikace provádět požadavky cross-origin.

  ::: moniker range=">= aspnetcore-2.2"

  > [!NOTE]
  > Určení `AllowAnyOrigin` a `AllowCredentials` je nezabezpečené konfigurace a může vést k padělání žádosti více webů. CORS služby vrátí neplatnou odpověď CORS, pokud aplikace je nakonfigurovaná u obou metod.

  ::: moniker-end

  ::: moniker range="< aspnetcore-2.2"

  > [!NOTE]
  > Určení `AllowAnyOrigin` a `AllowCredentials` je nezabezpečené konfigurace a může vést k padělání žádosti více webů. Pro zabezpečené aplikace zadejte přesný seznam původů, pokud klient musí autorizovat samotné pro přístup k prostředkům serveru.

  ::: moniker-end

<!-- REVIEW required
I changed from
Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration. **This** setting affects preflight requests and the ...
to
**`AllowAnyOrigin`** affects preflight requests and the

to remove the ambiguous **This**. 
-->

  `AllowAnyOrigin` ovlivňuje časový limit předběžné požadavky a `Access-Control-Allow-Origin` záhlaví. Další informace najdete v tématu [časový limit předběžné požadavky](#preflight-requests) oddílu.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásady tak, aby jako funkce, která umožňuje zdrojů tak, aby odpovídaly nakonfigurovaný zástupnou doménu, když vyhodnocuje se, jestli je povolený původ.

  [!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>Nastavte povolené metody HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Umožňuje libovolné metody HTTP:
* Ovlivňuje časový limit předběžné požadavky a `Access-Control-Allow-Methods` záhlaví. Další informace najdete v tématu [časový limit předběžné požadavky](#preflight-requests) oddílu.

### <a name="set-the-allowed-request-headers"></a>Nastavit hlavičku povolené žádosti

Volá se, aby konkrétní hlavičky se odešle žádost CORS *vytvářet hlavičky žádosti*, volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadat povolené hlavičky:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Chcete-li povolit všechny hlavičky žádosti, vytvářet volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Toto nastavení má vliv předběžných požadavků a `Access-Control-Request-Headers` záhlaví. Další informace najdete v tématu [časový limit předběžné požadavky](#preflight-requests) oddílu.

::: moniker range=">= aspnetcore-2.2"

Shoda se zásadami Middlewarem CORS pro konkrétní hlavičky určené `WithHeaders` je možné, pouze při odeslání hlaviček `Access-Control-Request-Headers` přesně odpovídat záhlaví uvádí `WithHeaders`.

Zvažte například aplikaci nakonfigurovány takto:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS Middleware předběžný požadavek s následující hlavičky žádosti odmítne, protože `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) není uveden ve `WithHeaders`:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Vrátí aplikaci *200 OK* odpověď, ale nebude odesílat hlavičky CORS zpět. Prohlížeč proto nebude se pokoušet žádosti nepůvodního zdroje.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS Middleware vždy povoluje čtyři záhlaví v `Access-Control-Request-Headers` k odeslání bez ohledu na nakonfigurované v CorsPolicy.Headers hodnoty. Tento seznam hlaviček obsahuje:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Zvažte například aplikaci nakonfigurovány takto:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS Middleware se úspěšně odpoví na předběžný požadavek s následující hlavičky žádosti protože `Content-Language` je vždy přidat na seznam povolených:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>Nastavit hlavičky vystavené odpovědi

Ve výchozím prohlížeči nezveřejňuje všechny hlavičky odpovědí do aplikace. Další informace najdete v tématu [W3C napříč sdílení prostředků různého původu (terminologie): Hlavička odpovědi jednoduché](https://www.w3.org/TR/cors/#simple-response-header).

Hlavičky odpovědi, které jsou k dispozici ve výchozím nastavení jsou:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Specifikace CORS volá tyto hlavičky *hlavičky odpovědi jednoduché*. Chcete-li zpřístupnit další hlavičky pro aplikace, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Přihlašovací údaje v požadavky cross-origin

Přihlašovací údaje vyžadují speciální zacházení v požadavku CORS. Ve výchozím nastavení nebude prohlížeč odesílá pověření s žádostí nepůvodního zdroje. Přihlašovací údaje zahrnují soubory cookie a schémat ověřování protokolu HTTP. Odesílá pověření s žádostí nepůvodního zdroje, musíte nastavit klienta `XMLHttpRequest.withCredentials` k `true`.

Pomocí `XMLHttpRequest` přímo:

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

Použití [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Na serveru, musíte povolit přihlašovací údaje. Chcete-li povolit přihlašovací údaje mezi zdroji, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

Odpověď HTTP, která zahrnuje `Access-Control-Allow-Credentials` hlavičky, která sděluje prohlížeči, že server umožňuje přihlašovací údaje pro žádosti nepůvodního zdroje.

Pokud prohlížeč odesílá pověření, ale odpověď neobsahuje platný `Access-Control-Allow-Credentials` záhlaví, v prohlížeči nezveřejňuje odpovědi do aplikace a nepůvodního požadavek selže.

Povolení nepůvodního pověření je bezpečnostním rizikem. Web v jiné doméně odeslat do aplikace jménem uživatele bez vědomí uživatele pověření přihlášeného uživatele. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Specifikace CORS také uvádí nastavení počátky k `"*"` (všechny původy) je neplatná-li `Access-Control-Allow-Credentials` záhlaví je k dispozici.

### <a name="preflight-requests"></a>Předběžných požadavků

U některých požadavků CORS prohlížeč odešle požadavek další před provedením aktuálního požadavku. Tento požadavek je volána *předběžný požadavek*. Prohlížeči můžete přeskočit předběžný požadavek, pokud jsou splněny následující podmínky:

* Metoda žádosti je GET, HEAD nebo POST.
* Aplikaci nelze nastavit hlavičku žádosti jiných než `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, nebo `Last-Event-ID`.
* `Content-Type` Záhlaví,-li nastavit, protože má jednu z následujících hodnot:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Sada pravidel na hlavičky požadavku pro požadavek klienta se vztahuje na hlavičky, které aplikace nastaví voláním `setRequestHeader` na `XMLHttpRequest` objektu. Specifikace CORS volá tyto hlavičky *vytvářet hlavičky požadavku*. Toto pravidlo neplatí pro záhlaví můžete nastavit v prohlížeči, jako například `User-Agent`, `Host`, nebo `Content-Length`.

Následuje příklad předběžný požadavek:

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

Přípravné požadavek používá metodu HTTP OPTIONS. Obsahuje dva speciálními záhlavími:

* `Access-Control-Request-Method`: Metoda protokolu HTTP, který se použije pro aktuálního požadavku.
* `Access-Control-Request-Headers`: Seznam hlaviček požadavků, které aplikace nastaví u aktuálního požadavku. Jak bylo uvedeno dříve, to nezahrnuje hlavičky, které nastaví v prohlížeči, jako například `User-Agent`.

Předběžný požadavek CORS patří `Access-Control-Request-Headers` hlavičky, která označuje hlavičky, které jsou odeslány pomocí aktuálního požadavku na server.

Chcete-li povolit konkrétní hlavičky, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Chcete-li povolit všechny hlavičky žádosti, vytvářet volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Nejsou zcela konzistentní v tom, jak je nastavit prohlížeče `Access-Control-Request-Headers`. Pokud nastavíte záhlaví na něco jiného než `"*"` (nebo použijte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), by měl obsahovat alespoň `Accept`, `Content-Type`, a `Origin`, a navíc jakékoli vlastní hlavičky, které chcete podporovat.

Následuje příklad odpovědi pro předběžný požadavek (za předpokladu, že server umožňuje žádosti):

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

Odpověď obsahuje `Access-Control-Allow-Methods` hlavičku, která uvádí povolené metody a volitelně `Access-Control-Allow-Headers` hlavičky, která uvádí povolené hlavičky. Pokud je předběžný požadavek úspěšné, prohlížeč odesílá aktuálního požadavku.

Pokud je předběžný požadavek, vrátí aplikaci *200 OK* odpovědi ale nebude odesílat hlavičky CORS zpět. Prohlížeč proto nebude se pokoušet žádosti nepůvodního zdroje.

### <a name="set-the-preflight-expiration-time"></a>Nastavit čas vypršení platnosti předběžné

`Access-Control-Max-Age` Záhlaví Určuje, jak dlouho může do mezipaměti odpovědi pro předběžný požadavek. Chcete-li nastavit tuto hlavičku, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Jak funguje CORS

Tato část popisuje, co se stane [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) požadavek na úrovni zprávy HTTP.

* CORS je **není** funkce zabezpečení. CORS je standard W3C, která umožňuje server zmírnit zásadu stejného zdroje.
  * Například může použít škodlivý objekt actor [zabránit webů skriptování mezi weby (XSS)](xref:security/cross-site-scripting) proti vaší lokality a spuštění podvržení žádosti do své lokality zapnuté CORS odcizit informace.
* Vaše rozhraní API není bezpečnější povolením CORS.
  * To je na klienta (prohlížeč) k vynucení CORS. Server zpracuje požadavek a vrátí odpověď, je klient, který vrátí odpověď na chybu a bloků. Například některé z následujících nástrojů se zobrazí odpověď serveru:
     * [Fiddler](https://www.telerik.com/fiddler)
     * [Postman](https://www.getpostman.com/)
     * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
     * Zadáním adresy URL do adresního řádku webového prohlížeče.
* Jedná se o způsob pro server, aby prohlížeče k provedení různých původů [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) nebo [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) požadavek, který by jinak zakázáno.
  * Prohlížeče, které (CORS) nemůže provádět požadavky cross-origin. Před CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) byl použit pro toto omezení obejít. JSONP nepoužívá XHR, použije `<script>` značky pro příjem odpovědi. Skripty mohou být načteny nepůvodního zdroje.

[Specifikace CORS]() zavedené několik nové hlavičky protokolu HTTP, které umožňují požadavky cross-origin. Pokud je prohlížeč podporuje CORS, nastaví tyto hlavičky automaticky pro požadavky cross-origin. Vlastní kód jazyka JavaScript není vyžadován k povolení sdílení CORS.

Následuje příklad žádosti nepůvodního zdroje. `Origin` Záhlaví obsahuje domény, lokality, která odeslala žádost:

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

Pokud server umožňuje, aby žádosti, nastaví `Access-Control-Allow-Origin` hlaviček v odpovědi. Hodnotu této hlavičky odpovídá buď `Origin` hlavička ze žádosti nebo je hodnota zástupného znaku `"*"`, což znamená, že jakýkoli původ je povoleno:

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

Pokud odpověď neobsahuje `Access-Control-Allow-Origin` hlavičky žádosti nepůvodního selže. Konkrétně v prohlížeči zakazuje požadavku. I v případě, že server vrátí úspěšné odpovědi, prohlížeč nevyužívá odpovědi k dispozici pro klientské aplikace.

<a name="test"></a>

## <a name="test-cors"></a>Test CORS

Test CORS:

1. [Vytvoření projektu aplikace API](xref:tutorials/first-web-api). Alternativně můžete [Stáhněte ukázku](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Povolení CORS pomocí jednoho z postupů v tomto dokumentu. Příklad:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]
  
  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` by měla sloužit pouze pro účely testování ukázkové aplikace podobně jako [stáhnout ukázkový kód](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Vytvoření projektu webové aplikace (pro stránky Razor nebo MVC). Ukázka používá pro stránky Razor. Můžete vytvořit webovou aplikaci ve stejném řešení jako projekt rozhraní API.
1. Přidejte následující zvýrazněný kód do *Index.cshtml* souboru:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` s adresou URL nasazené aplikace.
1. Nasazení projektu rozhraní API. Například [nasazení do Azure](xref:host-and-deploy/azure-apps/index).
1. Spuštění aplikace Razor Pages nebo MVC z plochy a klikněte na **Test** tlačítko. Pomocí nástrojů F12 Zkontrolujte chybové zprávy.
1. Odebrat localhost očátek z `WithOrigins` a nasazení aplikace. Můžete také spusťte klientskou aplikaci s jiným portem. Například spusťte ze sady Visual Studio.
1. Testování pomocí klientské aplikace. Selhání CORS vrátí chybu, ale chybová zpráva není k dispozici pro jazyk JavaScript. Na kartě konzoly v nástrojích F12 tools chybu. V závislosti na prohlížeči dojde k chybě (v konzole nástroje F12) podobný následujícímu:

  * Pomocí Microsoft Edge:

    **SEC7120: [CORS] původ `https://localhost:44375` nenalezl `https://localhost:44375` v hlavička odpovědi Access-Control-Allow-Origin pro prostředek nepůvodního zdroje `https://webapi.azurewebsites.net/api/values/1`**

  * Použití Chrome:

    **Přístup k XMLHttpRequest na `https://webapi.azurewebsites.net/api/values/1` z počátku `https://localhost:44375` nezablokoval zásada CORS: Žádné záhlaví 'Přístup-Control-Allow-Origin' je k dispozici u požadovaného prostředku.**

## <a name="additional-resources"></a>Další zdroje

* [Prostředků mezi zdroji (CORS) pro sdílení obsahu](https://developer.mozilla.org/docs/Web/HTTP/CORS)
