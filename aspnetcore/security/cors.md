---
title: Povolení žádostí napříč zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak CORS jako standard pro povolení nebo odmítnutí žádostí nepůvodního v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/05/2018
uid: security/cors
ms.openlocfilehash: 8e5056b448d47d75272e9394b03ce8a58b05a0f4
ms.sourcegitcommit: 09affee3d234cb27ea6fe33bc113b79e68900d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51191318"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Povolení žádostí napříč zdroji (CORS) v ASP.NET Core

Podle [Mike Wasson](https://github.com/mikewasson), [Shayne Boyer](https://twitter.com/spboyer), a [Petr Dykstra](https://github.com/tdykstra)

Zabezpečení prohlížečů brání zasílání požadavků na jiné doméně než ten, který obsluhuje webovou stránku na webové stránce. Toto omezení je volána *zásada stejného zdroje*. Zásada stejného zdroje brání škodlivým webům ve čtení citlivých dat z jiné lokality. V některých případech můžete chtít povolit, že ostatní lokality provádět požadavky cross-origin do vaší aplikace.

[Mezi sdílení zdrojů původu](https://www.w3.org/TR/cors/) (CORS) je standard W3C, která umožňuje server zmírnit zásadu stejného zdroje. Pomocí CORS, server explicitně můžou některé požadavky cross-origin zatímco jiné odmítnout. CORS je bezpečnější a flexibilnější, než starší techniky, jako například [JSONP](https://wikipedia.org/wiki/JSONP). Toto téma ukazuje, jak povolit CORS v aplikaci ASP.NET Core.

## <a name="same-origin"></a>Stejného původu

Pokud mají stejné schémata, hostitele a porty mít dvě adresy URL stejného původu ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Tyto dvě adresy URL mají stejného původu:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Tyto adresy URL mají různé zdroje než předchozí dvě adresy URL:

* `https://example.net` &ndash; Jiné domény
* `https://www.example.com/foo.html` &ndash; Různé subdomény
* `http://example.com/foo.html` &ndash; Jiné schéma
* `https://example.com:9000/foo.html` &ndash; Jiný port

> [!NOTE]
> Aplikace Internet Explorer nezahrne port při porovnání zdrojů.

## <a name="register-cors-services"></a>Registrace služby CORS

::: moniker range=">= aspnetcore-2.1"

Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.AspNetCore.Cors](https://www.nuget.org/packages/Microsoft.AspNetCore.Cors/) balíčku.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Odkaz [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage) nebo přidat odkaz na balíček [Microsoft.AspNetCore.Cors](https://www.nuget.org/packages/Microsoft.AspNetCore.Cors/) balíčku.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Přidejte odkaz na balíček [Microsoft.AspNetCore.Cors](https://www.nuget.org/packages/Microsoft.AspNetCore.Cors/) balíčku.

::: moniker-end

Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> v `Startup.ConfigureServices` přidejte CORS služby do aplikace služby kontejneru:

[!code-csharp[](cors/sample/CorsExample1/Startup.cs?name=snippet_addcors&highlight=3)]

## <a name="enable-cors"></a>Povolení CORS

Po registraci služby CORS, povolení CORS v aplikaci ASP.NET Core pomocí některé z následujících postupů:

* [CORS Middleware](#enable-cors-with-cors-middleware) &ndash; zásady CORS platí globálně do aplikace přes middleware.
* [CORS v MVC](#enable-cors-in-mvc) &ndash; CORS použít zásady podle akce nebo kontroleru. CORS Middleware se nepoužívá.

### <a name="enable-cors-with-cors-middleware"></a>Povolení CORS s Middlewarem CORS

CORS Middleware zpracovává požadavky cross-origin do aplikace. Chcete-li povolit CORS Middleware v kanálu zpracování požadavků, zavolejte <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metody rozšíření v `Startup.Configure`.

CORS Middleware musí předcházet všechny koncové body definované ve vaší aplikaci ve které chcete podporovat požadavky cross-origin (například před voláním `UseMvc` pro Middleware stránky MVC a Razor).

A *nepůvodního zdroje zásad* lze zadat při přidání pomocí CORS Middleware <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> třídy. Existují dva přístupy k definování zásad CORS:

* Volání `UseCors` pomocí výrazu lambda:

  [!code-csharp[](cors/sample/CorsExample1/Startup.cs?highlight=11,12&range=22-38)]

  Používá výraz lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objektu. [Možnosti konfigurace](#cors-policy-options), jako například `WithOrigins`, jsou popsány dále v tomto tématu. V předchozím příkladu, zásada umožňuje požadavky cross-origin z `https://example.com` a žádné jiné zdroje.

  Adresa URL musí být zadán bez koncové lomítko (`/`). Pokud adresa URL se ukončí s `/`, porovnání vrátí `false` a vrátí se bez záhlaví.

  `CorsPolicyBuilder` obsahuje rozhraní API fluent, takže můžete vytvořit řetězové volání metody:

  [!code-csharp[](cors/sample/CorsExample3/Startup.cs?highlight=2-3&range=29-32)]

* Definovat jeden nebo více s názvem zásady CORS a vyberte zásadu podle názvu za běhu. Následující příklad přidá uživatelem definované zásady CORS s názvem *AllowSpecificOrigin*. Vyberte zásadu, předejte název, který má `UseCors`:

  [!code-csharp[](cors/sample/CorsExample2/Startup.cs?name=snippet_begin&highlight=5-6,21)]

### <a name="enable-cors-in-mvc"></a>Povolení CORS v aplikaci MVC

Můžete také použít MVC použít konkrétní zásady CORS podle akce nebo kontroleru. Při povolení CORS pomocí MVC, se používají registrovaných služeb CORS. CORS Middleware se nepoužívá.

### <a name="per-action"></a>Za akci

Chcete-li určit zásady CORS pro určité akce, přidejte [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) atribut na akci. Zadejte název zásady.

[!code-csharp[](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnAction&highlight=2)]

### <a name="per-controller"></a>Na kontroler

Chcete-li určit zásady CORS pro konkrétní ovladač, přidejte [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) atribut třídy kontroleru. Zadejte název zásady.

[!code-csharp[](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnController&highlight=2)]

Pořadí priority je:

1. Akce
1. kontroler

### <a name="disable-cors"></a>Zákazu sdílení CORS

Chcete-li zakázat CORS pro kontroler nebo akce, použijte [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) atribut:

[!code-csharp[](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=DisableOnAction&highlight=2)]

## <a name="cors-policy-options"></a>Možnosti zásad CORS

Tato část popisuje různé možnosti, které můžete nastavit zásadu CORS.

* [Nastavte povolené zdroje](#set-the-allowed-origins)
* [Nastavte povolené metody HTTP](#set-the-allowed-http-methods)
* [Nastavit hlavičku povolené žádosti](#set-the-allowed-request-headers)
* [Nastavit hlavičky vystavené odpovědi](#set-the-exposed-response-headers)
* [Přihlašovací údaje v požadavky cross-origin](#credentials-in-cross-origin-requests)
* [Nastavit čas vypršení platnosti předběžné](#set-the-preflight-expiration-time)

Pro některé možnosti, může být užitečné ke čtení [funguje jak CORS](#how-cors-works) první části.

### <a name="set-the-allowed-origins"></a>Nastavte povolené zdroje

Middleware CORS v ASP.NET Core MVC má několik způsobů, jak určit povolené zdroje:

* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithOrigins*>: Umožňuje zadat jednu nebo více adres URL. Adresa URL může obsahovat schéma, název hostitele a port bez jakýchkoli informací o cestě. Například `https://example.com`. Adresa URL musí být zadán bez koncové lomítko (`/`).

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=20-24&highlight=4)]

* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Umožňuje požadavků CORS z všechny původy žádné schéma (`http` nebo `https`).

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=28-32&highlight=4)]

Pečlivě zvažte předtím, než žádosti z původu. Povolení žádostí z jakýkoli původ znamená, že *všechny weby* můžete provádět požadavky cross-origin vaší aplikace.

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> Určení `AllowAnyOrigin` a `AllowCredentials` je nezabezpečené konfigurace a může vést k padělání žádosti více webů. CORS služby vrátí neplatnou odpověď CORS, pokud aplikace je nakonfigurovaná s nimi.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> Určení `AllowAnyOrigin` a `AllowCredentials` je nezabezpečené konfigurace a může vést k padělání žádosti více webů. Zvažte možnost určení přesný seznam původů, pokud klient potřebuje k autorizaci pro přístup k prostředkům serveru.

::: moniker-end

Toto nastavení má vliv [časový limit předběžné požadavky a hlavičky Access-Control-Allow-Origin](#preflight-requests) (popsáno dále v tomto tématu).

* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> – Umožňuje požadavků CORS domén dílčí dané domény. Schéma nemůže být zástupný znak.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=98-104&highlight=4)]

### <a name="set-the-allowed-http-methods"></a>Nastavte povolené metody HTTP

Chcete-li povolit všechny metody HTTP, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=45-50&highlight=5)]

Toto nastavení má vliv [časový limit předběžné požadavky a záhlaví přístupu – ovládací prvek-Allow-Methods](#preflight-requests) (popsáno dále v tomto tématu).

### <a name="set-the-allowed-request-headers"></a>Nastavit hlavičku povolené žádosti

Volá se, aby konkrétní hlavičky se odešle žádost CORS *vytvářet hlavičky žádosti*, volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadat povolené hlavičky:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=54-59&highlight=5)]

Chcete-li povolit všechny hlavičky žádosti, vytvářet volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=63-68&highlight=5)]

Toto nastavení má vliv [časový limit předběžné požadavky a hlavičky Access-Control-Request-Headers](#preflight-requests) (popsáno dále v tomto tématu).

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

Ve výchozím prohlížeči nezveřejňuje všechny hlavičky odpovědí do aplikace. Další informace najdete v tématu [W3C napříč sdílení prostředků různého původu (terminologie): jednoduché hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).

Hlavičky odpovědi, které jsou k dispozici ve výchozím nastavení jsou:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Specifikace CORS volá tyto hlavičky *hlavičky odpovědi jednoduché*. Chcete-li zpřístupnit další hlavičky pro aplikace, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=72-77&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Přihlašovací údaje v požadavky cross-origin

Přihlašovací údaje vyžadují speciální zacházení v požadavku CORS. Ve výchozím nastavení nebude prohlížeč odesílá pověření s žádostí nepůvodního zdroje. Přihlašovací údaje zahrnují soubory cookie a schémat ověřování protokolu HTTP. Odesílá pověření s žádostí nepůvodního zdroje, musíte nastavit klienta `XMLHttpRequest.withCredentials` k `true`.

Pomocí `XMLHttpRequest` přímo:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

V jQuery:

```jQuery
$.ajax({
  type: 'get',
  url: 'https://www.example.com/home',
  xhrFields: {
    withCredentials: true
}
```

Na serveru navíc musíte povolit přihlašovací údaje. Chcete-li povolit přihlašovací údaje mezi zdroji, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=81-86&highlight=5)]

Odpověď HTTP, která zahrnuje `Access-Control-Allow-Credentials` hlavičky, která sděluje prohlížeči, že server umožňuje přihlašovací údaje pro žádosti nepůvodního zdroje.

Pokud prohlížeč odesílá pověření, ale odpověď neobsahuje platný `Access-Control-Allow-Credentials` záhlaví, v prohlížeči nezveřejňuje odpovědi do aplikace a nepůvodního požadavek selže.

Buďte opatrní při povolování pověření nepůvodního zdroje. Web v jiné doméně odeslat do aplikace jménem uživatele bez vědomí uživatele pověření přihlášeného uživatele.

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

* `Access-Control-Request-Method`: Metoda HTTP, který se použije pro aktuálního požadavku.
* `Access-Control-Request-Headers`: Seznam hlaviček požadavků, které aplikace nastaví u aktuálního požadavku. Jak bylo uvedeno dříve, to nezahrnuje hlavičky, které nastaví v prohlížeči, jako například `User-Agent`.

Předběžný požadavek CORS patří `Access-Control-Request-Headers` hlavičky, která označuje hlavičky, které jsou odeslány pomocí aktuálního požadavku na server.

Chcete-li povolit konkrétní hlavičky, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=54-59&highlight=5)]

Chcete-li povolit všechny hlavičky žádosti, vytvářet volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=63-68&highlight=5)]

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

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=90-95&highlight=5)]

## <a name="how-cors-works"></a>Jak funguje CORS

Tato část popisuje, co se stane, že v požadavku CORS na úrovni zprávy HTTP. Je důležité pochopit, jak CORS funguje tak, aby zásady CORS můžete nakonfigurovat správně a ladit, když dojde k neočekávané chování.

Specifikace CORS zavádí několik nové hlavičky protokolu HTTP, které umožňují požadavky cross-origin. Pokud je prohlížeč podporuje CORS, nastaví tyto hlavičky automaticky pro požadavky cross-origin. Vlastní kód jazyka JavaScript není vyžadován k povolení sdílení CORS.

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

## <a name="additional-resources"></a>Další zdroje

* [Prostředků mezi zdroji (CORS) pro sdílení obsahu](https://developer.mozilla.org/docs/Web/HTTP/CORS)
