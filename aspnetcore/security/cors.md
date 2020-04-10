---
title: Povolit požadavky na příčný původ (CORS) v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak cors jako standard pro povolení nebo odmítnutí požadavků napříč původem v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 601e26e1990a86ad60aa50c8c93ffa490ff6b708
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007181"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Povolit požadavky na příčný původ (CORS) v ASP.NET jádru

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirk Larkin](https://twitter.com/serpent5)

Tento článek ukazuje, jak povolit CORS v aplikaci ASP.NET Core.

Zabezpečení prohlížeče zabraňuje webové stránce podávat žádosti do jiné domény, než je doména, která webovou stránku obsluhovala. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původu brání škodlivému webu ve čtení citlivých dat z jiného webu. Někdy můžete chtít povolit jiným webům, aby do vaší aplikace pokládaly žádosti o více původů. Další informace naleznete v [článku Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Sdílení prostředků napříč původem](https://www.w3.org/TR/cors/) (CORS):

* Je standard W3C, který umožňuje serveru uvolnit zásady stejného původu.
* **Není** bezpečnostní funkce, CORS uvolňuje zabezpečení. Rozhraní API není bezpečnější povolením CORS. Další informace naleznete v tématu [Jak cors funguje](#how-cors).
* Umožňuje serveru explicitně povolit některé požadavky napříč původy při odmítnutí jiných.
* Je bezpečnější a flexibilnější než předchozí techniky, jako je [Například JSONP](/dotnet/framework/wcf/samples/jsonp).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="same-origin"></a>Stejný původ

Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Tyto dvě adresy URL mají stejný původ:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Tyto adresy URL mají jiný původ než předchozí dvě adresy URL:

* `https://example.net`&ndash; Jiná doména
* `https://www.example.com/foo.html`&ndash; Různé subdomény
* `http://example.com/foo.html`&ndash; Jiný režim
* `https://example.com:9000/foo.html`&ndash; Jiný port

## <a name="enable-cors"></a>Povolení CORS

Existují tři způsoby, jak povolit CORS:

* V middlewaru pomocí [pojmenované zásady](#np) nebo [výchozí zásady](#dp).
* Použití [směrování koncového bodu](#ecors).
* S atributem [[EnableCors].](#attr)

Použití atributu [[EnableCors]](#attr) s pojmenovanou zásadou poskytuje nejlepší ovládací prvek v omezení koncových bodů, které podporují CORS.

Každý přístup je podrobně popsán v následujících částech.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS s názvem politiky a middleware

CORS Middleware zpracovává požadavky napříč původy. Následující kód použije zásady CORS pro všechny koncové body aplikace se zadaným původem:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

Předcházející kód:

* Nastaví název `_myAllowSpecificOrigins`zásady na . Název zásady je libovolný.
* Volá <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodu rozšíření a `_myAllowSpecificOrigins` určuje zásady CORS. `UseCors`dodává middleware CORS.
* Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [lambda výrazem](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambda má <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> předmět. [Možnosti konfigurace](#cors-policy-options) `WithOrigins`, například , jsou popsány dále v tomto článku.
* Povolí `_myAllowSpecificOrigins` zásady CORS pro všechny koncové body řadiče. Viz [směrování koncového bodu](#ecors) použít zásadu CORS pro konkrétní koncové body.

Při směrování koncových bodů ***musí*** být middleware CORS `UseRouting` nakonfigurován tak, aby se spouštěl mezi voláními do a `UseEndpoints`.

Pokyny k testování kódu podobnépředchozímu kódu naleznete v [tématu Test CORS.](#testc)

Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody přidá služby CORS do kontejneru služeb aplikace:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Další informace naleznete v [tématu možnosti zásad CORS](#cpo) v tomto dokumentu.

Metody <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> mohou být zřetězené, jak je znázorněno v následujícím kódu:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Poznámka: Zadaná adresa URL **nesmí** `/`obsahovat koncové lomítko ( ). Pokud adresa URL `/`končí pomocí `false` , vrátí porovnání a není vrácena žádná hlavička.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS s výchozími zásadami a middlewarem

Následující zvýrazněný kód umožňuje výchozí zásady CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Předchozí kód použije výchozí zásadu CORS pro všechny koncové body řadiče.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Povolení corsu s směrováním koncových bodů

Povolení cors na základě koncového `RequireCors` bodu pomocí aktuálně ***nepodporuje*** [automatické požadavky kontroly před výstupem](#apf). Další informace naleznete v [tomto problému GitHub](https://github.com/dotnet/aspnetcore/issues/20709) a [Testování CORS s směrování montovny koncového bodu a [HttpOptions]](#tcer).

S směrováním koncových bodů může být CORS povoleno <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> na základě koncového bodu pomocí sady metod rozšíření:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,41,44)]

V předchozím kódu:

* `app.UseCors`umožňuje middleware CORS. Vzhledem k tomu, že `app.UseCors()` výchozí zásady nebyly nakonfigurovány, sám neumožňuje CORS.
* Koncové `/echo` body a řadič umožňují požadavky na příčný původ pomocí zadané zásady.
* Koncové `/echo2` body a Razor Pages ***neumožňují*** požadavky na příčný původ, protože nebyla zadána žádná výchozí zásada.

Atribut [[DisableCors]](#dc) ***nezakáže*** cors, který byl `RequireCors`povolen směrováním koncových bodů s .

Pokyny k testování kódu podobnépředchozímu tématu [Test CORS s směrováním koncových bodů a [HttpOptions]](#tcer) naleznete v tématu Test CORS s směrováním koncových bodů a [HttpOptions].

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Povolení CORS s atributy

Povolení CORS s atributem [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) a použití pojmenované zásady pouze na ty koncové body, které vyžadují CORS, poskytuje nejlepší ovládací prvek.

Atribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) poskytuje alternativu k použití CORS globálně. Atribut `[EnableCors]` umožňuje CORS pro vybrané koncové body, nikoli všechny koncové body:

* `[EnableCors]`určuje výchozí zásadu.
* `[EnableCors("{Policy String}")]`určuje pojmenovanou zásadu.

Atribut `[EnableCors]` lze použít na:

* Holicí strojek stránka`PageModel`
* Řadič
* Metoda akce řadiče

Různé zásady lze použít pro řadiče, modely `[EnableCors]` stránek nebo metody akce s atributem. Pokud `[EnableCors]` je atribut použit na řadič, model stránky nebo metodu akce a CORS je povolena v middlewaru, jsou použity ***obě*** zásady. ***Doporučujeme proti kombinování zásad. Použijte*** ***atribut nebo middleware, ne obojí ve stejné aplikaci.*** `[EnableCors]`

Následující kód platí pro každou metodu jinou zásadu:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Následující kód vytvoří dvě zásady CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Pro nejlepší kontrolu omezení požadavků CORS:

* Použití `[EnableCors("MyPolicy")]` s pojmenovanou zásadou.
* Nedefinujte výchozí zásadu.
* Nepoužívejte [směrování koncového bodu](#ecors).

Kód v další části splňuje předchozí seznam.

Pokyny k testování kódu podobnépředchozímu kódu naleznete v [tématu Test CORS.](#testc)

<a name="dc"></a>

### <a name="disable-cors"></a>Zakázat CORS

Atribut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***nezakáže*** cors, který byl povolen [směrováním koncových bodů](#ecors).

Následující kód definuje zásady `"MyPolicy"`CORS :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Následující kód zakáže CORS pro `GetValues2` akci:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Předcházející kód:

* Neumožňuje CORS s [směrováním koncových bodů](#ecors).
* Nedefinuje výchozí [zásady CORS](#dp).
* Používá [[EnableCors("MyPolicy")]](#attr) k `"MyPolicy"` povolení zásady CORS pro řadič.
* Zakáže CORS `GetValues2` pro metodu.

Pokyny k testování předchozího kódu naleznete v [tématu Test CORS.](#testc)

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Možnosti zásad CORS

Tato část popisuje různé možnosti, které lze nastavit v zásadách CORS:

* [Nastavení povolených počátků](#set-the-allowed-origins)
* [Nastavení povolených metod HTTP](#set-the-allowed-http-methods)
* [Nastavení záhlaví povolených požadavků](#set-the-allowed-request-headers)
* [Nastavení exponovaných hlaviček odpovědí](#set-the-exposed-response-headers)
* [Pověření v požadavcích na příčové počátky](#credentials-in-cross-origin-requests)
* [Nastavení doby vypršení platnosti kontroly před výstupem](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je přivolána . `Startup.ConfigureServices` U některých možností může být užitečné nejprve si přečíst část [Jak cors funguje.](#how-cors)

## <a name="set-the-allowed-origins"></a>Nastavení povolených počátků

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Umožňuje CORS požadavky ze všech původů`http` `https`s jakýmkoli schématem ( nebo ). `AllowAnyOrigin`je nejistá, protože *jakýkoli web* může do aplikace provádět požadavky napříč původy.

> [!NOTE]
> Zadání `AllowAnyOrigin` a `AllowCredentials` je nezabezpečená konfigurace a může mít za následek padělání požadavků mezi lokalitami. Služba CORS vrátí neplatnou odpověď CORS, když je aplikace nakonfigurována s oběma způsoby.

`AllowAnyOrigin`ovlivňuje požadavky kontroly `Access-Control-Allow-Origin` před výstupem a záhlaví. Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásady jako funkci, která umožňuje počátkům tak, aby odpovídala nakonfigurované doméně se zástupnými kartami při vyhodnocování, pokud je povolen původ.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Nastavení povolených metod HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Umožňuje libovolnou metodu HTTP:
* Ovlivňuje požadavky kontroly `Access-Control-Allow-Methods` před výstupem a záhlaví. Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Nastavení záhlaví povolených požadavků

Chcete-li povolit odeslání určitých hlaviček v požadavku CORS, [nazývaném hlavičky žádostí o autora](https://xhr.spec.whatwg.org/#request), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Chcete-li povolit všechny <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [hlavičky žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), volejte :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`ovlivňuje požadavky kontroly před výstupem a hlavičku [Hlavičky access-control-request-headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)

Zásady CORS Middleware odpovídají určitým `WithHeaders` záhlavím určeným v písmenu a) je možné pouze v případě, že hlavičky odeslané přesně `Access-Control-Request-Headers` odpovídají záhlavím uvedeným v . `WithHeaders`

Zvažte například aplikaci nakonfigurovanou takto:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

Cors Middleware odmítne požadavek kontroly před `Content-Language` výstupem s následující hlavičkou požadavku, `WithHeaders`protože ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) není uveden v :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Aplikace vrátí odpověď *200 OK,* ale neodešle záhlaví CORS zpět. Proto prohlížeč nepokouší požadavek křížového původu.

### <a name="set-the-exposed-response-headers"></a>Nastavení exponovaných hlaviček odpovědí

Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace. Další informace naleznete v tématu [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).

Záhlaví odpovědí, která jsou ve výchozím nastavení k dispozici, jsou:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědí*. Chcete-li aplikaci zpřístupnit další <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>záhlaví, zavolejte :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Pověření v požadavcích na příčové počátky

Pověření vyžadují zvláštní zpracování v požadavku CORS. Ve výchozím nastavení prohlížeč neodesílá přihlašovací údaje s požadavkem na příčný původ. Pověření zahrnují soubory cookie a schémata ověřování HTTP. Chcete-li odeslat pověření s požadavkem `XMLHttpRequest.withCredentials` na `true`příčný původ, musí klient nastavit .

Použití `XMLHttpRequest` přímo:

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

Použití rozhraní [API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Server musí povolit pověření. Chcete-li povolit pověření <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>pro více původů, volejte :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` záhlaví, které prohlížeči říká, že server povoluje pověření pro požadavek na příčný původ.

Pokud prohlížeč odešle pověření, ale odpověď neobsahuje platné `Access-Control-Allow-Credentials` záhlaví, prohlížeč nezveřejňuje odpověď na aplikaci a požadavek na příčný původ se nezdaří.

Povolení pověření pro více původ je bezpečnostní riziko. Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Specifikace CORS také uvádí, že `"*"` nastavení původu (všechny počátky) je neplatná, pokud `Access-Control-Allow-Credentials` je záhlaví k dispozici.

<a name="pref"></a>

## <a name="preflight-requests"></a>Požadavky kontroly před výstupem

U některých požadavků CORS prohlížeč odešle další [požadavky options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) před provedením skutečné žádosti. Tento požadavek se nazývá [požadavek kontroly před výstupem](https://developer.mozilla.org/docs/Glossary/Preflight_request). Prohlížeč může přeskočit požadavek kontroly před výstupem, pokud jsou splněny ***všechny*** následující podmínky:

* Metoda požadavku je GET, HEAD nebo POST.
* Aplikace nenastavuje záhlaví požadavků `Accept`kromě `Accept-Language` `Content-Language`, `Content-Type`, `Last-Event-ID`, nebo .
* Záhlaví, `Content-Type` pokud je nastaveno, má jednu z následujících hodnot:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Pravidlo pro hlavičky požadavku nastavené pro požadavek klienta se `setRequestHeader` vztahuje `XMLHttpRequest` na záhlaví, která aplikace nastaví voláním objektu. Specifikace CORS volá tyto hlavičky [záhlaví hlavičky požadavku autora](https://www.w3.org/TR/cors/#author-request-headers). Pravidlo se nevztahuje na záhlaví, která může `User-Agent`prohlížeč `Host`nastavit, například , nebo `Content-Length`.

Následuje příklad odpovědi podobné žádosti před výstupem z tlačítka **[Put test]** v části [Test CORS](#testc) tohoto dokumentu.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

Požadavek kontroly před výstupem používá metodu [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) Může obsahovat následující záhlaví:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Metoda HTTP, která bude použita pro skutečný požadavek.
* [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Seznam hlaviček požadavků, které aplikace nastaví na skutečný požadavek. Jak již bylo uvedeno dříve, nezahrnuje záhlaví, která `User-Agent`prohlížeč nastaví, například .
* [Metody access-control-allow-allow-](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Pokud je požadavek kontroly před `200 OK` výstupem zamítnut, aplikace vrátí odpověď, ale nenastaví záhlaví CORS. Proto prohlížeč nepokouší požadavek křížového původu. Příklad zamítnuté žádosti o předběžné kontroly naleznete v části [Test CORS](#testc) tohoto dokumentu.

Pomocí nástrojů F12 aplikace konzoly zobrazuje chybu podobnou jedné z následujících, v závislosti na prohlížeči:

* Firefox: Žádost o cross-origin blokována: Stejné zásady `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`původu zakáže čtení vzdáleného prostředku na adrese . (Důvod: Žádost CORS nebyla úspěšná). [Další informace](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Na bázi chromu: Přístuphttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5k načtení na ' od původu 'https://cors3.azurewebsites.net' byl zablokován zásadami CORS: Odpověď na požadavek kontroly před výstupem neprojde kontrolou řízení přístupu: Na požadovaném prostředku není k dispozici žádná hlavička "Access-Control-Allow-Origin". Pokud neprůhledná odpověď slouží vašim potřebám, nastavte režim požadavku na "no-cors" pro načtení prostředku se zakázaným CORS.

Chcete-li povolit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>určitá záhlaví, volejte :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Chcete-li povolit všechny <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [hlavičky žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), volejte :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Prohlížeče nejsou konzistentní v tom, `Access-Control-Request-Headers`jak nastavují . Pokud:

* Záhlaví jsou nastavena na cokoli jiného než`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>se nazývá: Zahrnout `Accept` `Content-Type`alespoň `Origin`, a , plus všechny vlastní záhlaví, které chcete podporovat.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Kód automatického požadavku kontroly před výstupem

Při použití zásady CORS buď:

* Globálně voláním `app.UseCors` `Startup.Configure`v .
* Použití `[EnableCors]` atributu.

ASP.NET Core reaguje na požadavek možnosti kontroly před výstupem.

Povolení CORS na základě koncového `RequireCors` bodu pomocí aktuálně ***nepodporuje*** automatické požadavky kontroly před výstupem.

[Test CORS](#testc) části tohoto dokumentu ukazuje toto chování.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions] atribut pro požadavky kontroly před výstupem

Pokud je cors povoleno s příslušnou zásadou, ASP.NET Core obecně reaguje na požadavky cors kontroly před výstupem automaticky. V některých scénářích to nemusí být tento případ. Například pomocí [CORS s směrováním koncového bodu](#ecors).

Následující kód používá atribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) k vytvoření koncových bodů pro požadavky OPTIONS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Pokyny k testování předchozího kódu naleznete v [tématu Test CORS s směrováním koncových bodů a [HttpOptions].](#tcer)

### <a name="set-the-preflight-expiration-time"></a>Nastavení doby vypršení platnosti kontroly před výstupem

Záhlaví `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek kontroly před výstupem uložena do mezipaměti. Chcete-li nastavit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>toto záhlaví, volejte :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Jak CORS funguje

Tato část popisuje, co se děje v požadavku [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.

* CORS **není** funkce zabezpečení. CORS je standard W3C, který umožňuje serveru uvolnit zásady stejného původu.
  * Škodlivý objekt actor může například použít [skriptování mezi webovými servery (XSS)](xref:security/cross-site-scripting) proti vašemu webu a provést požadavek na více webů na jejich web s povoleným cors, aby ukradl informace.
* Rozhraní API není bezpečnější povolením CORS.
  * Je na klientovi (prohlížeči), aby vynucoval CORS. Server provede požadavek a vrátí odpověď, je klient, který vrátí chybu a blokuje odpověď. Například některý z následujících nástrojů zobrazí odpověď serveru:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [Httpklient rozhraní .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Webový prohlížeč zadáním adresy URL do adresního řádku.
* Je to způsob, jak server umožňuje prohlížečům spustit požadavek [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro křížový původ, který by jinak byl zakázán.
  * Prohlížeče bez CORS nemohou dělat požadavky na příčný původ. Před CORS byl [jsonp](https://www.w3schools.com/js/js_json_jsonp.asp) použit k obcházení tohoto omezení. JSONP nepoužívá XHR, používá `<script>` značku pro příjem odpovědi. Skripty mohou být načteny cross-origin.

[Specifikace CORS](https://www.w3.org/TR/cors/) zavedla několik nových hlaviček HTTP, které umožňují požadavky na příčný původ. Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky automaticky pro požadavky křížového původu. Vlastní kód JavaScriptu není nutné povolit CORS.

Tlačítko [PUT test](https://cors3.azurewebsites.net/test) na nasazeném [vzorku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

Následuje příklad požadavku na příčný původ [Values](https://cors3.azurewebsites.net/) z testovacího `https://cors1.azurewebsites.net/api/values`tlačítka Hodnoty na . Záhlaví: `Origin`

* Poskytuje doménu webu, který je podání žádosti.
* Je vyžadována a musí se lišit od hostitele.

**Obecná záhlaví**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Záhlaví odpovědí**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**Záhlaví požadavku**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

V `OPTIONS` požadavcích server nastaví **Response headers** `Access-Control-Allow-Origin: {allowed origin}` hlavičku záhlaví odpovědí v odpovědi. Například nasazený [ukázkový](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)požadavek tlačítka `OPTIONS` [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) obsahuje následující záhlaví:

**Obecná záhlaví**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Záhlaví odpovědí**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**Záhlaví požadavku**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

V předchozích **hlavičkách odpovědí**server v odpovědi nastaví hlavičku [Access-Control-Allow-Origin.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) Hodnota `https://cors1.azurewebsites.net` tohoto záhlaví odpovídá `Origin` záhlaví z požadavku.

Pokud <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> je volána, `Access-Control-Allow-Origin: *`, hodnota zástupný znak, je vrácena. `AllowAnyOrigin`umožňuje jakýkoli původ.

Pokud odpověď neobsahuje `Access-Control-Allow-Origin` záhlaví, požadavek mezi původem se nezdaří. Konkrétně prohlížeč zakáže požadavek. I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupní odpověď klientské aplikaci.

<a name="options"></a>

### <a name="display-options-requests"></a>Zobrazit požadavky na možnosti zobrazení

Ve výchozím nastavení prohlížeče Chrome a Edge nezobrazují požadavky OPTIONS na kartě sítě nástrojů F12. Zobrazení požadavků OPTIONS v těchto prohlížečích:

* `chrome://flags/#out-of-blink-cors` nebo `edge://flags/#out-of-blink-cors`
* zneškodnit příznak.
* restart (restartovat).

Firefox ve výchozím nastavení zobrazuje požadavky OPTIONS.

## <a name="cors-in-iis"></a>CORS ve S.r.o.

Při nasazování do služby IIS musí být cors spuštěn před ověřováním systému Windows, pokud server není nakonfigurován tak, aby umožňoval anonymní přístup. Pro podporu tohoto scénáře je třeba nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.

<a name="testc"></a>

## <a name="test-cors"></a>Test CORS

[Ukázkové stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) má kód pro testování CORS. Podívejte [se, jak stáhnout](xref:index#how-to-download-a-sample). Ukázka je projekt ROZHRANÍ API s přidanou stránkou Razor:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`by měl být použit pouze pro testování ukázkové aplikace podobné [ukázkový kód ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).

Následující `ValuesController` poskytuje koncové body pro testování:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) je poskytována [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet balíček a zobrazí informace o trase.

Otestujte předchozí ukázkový kód pomocí jednoho z následujících přístupů:

* Použijte nasazenou ukázkovou [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)aplikaci na adrese . Není třeba stáhnout ukázku.
* Spusťte `dotnet run` ukázku pomocí `https://localhost:5001`výchozí adresy URL aplikace .
* Spusťte ukázku z Visual Studia s portem nastaveným `https://localhost:44398`na 44398 pro adresu URL aplikace .

Použití prohlížeče s nástroji F12:

* Vyberte tlačítko **Hodnoty** a zkontrolujte záhlaví na kartě **Síť.**
* Vyberte **tlačítko PUT test.** Viz [Požadavky na možnosti zobrazení](#options) pro pokyny k zobrazení žádosti možnosti. **Test PUT** vytvoří dva požadavky, požadavek kontroly před výstupem OPTIONS a požadavek PUT.
* Vyberte **`GetValues2 [DisableCors]`** tlačítko pro aktivaci neúspěšného požadavku CORS. Jak je uvedeno v dokumentu, odpověď vrátí 200 úspěch, ale požadavek CORS není podána. Chcete-li zobrazit chybu CORS, vyberte kartu **Konzola.** V závislosti na prohlížeči se zobrazí chyba podobná následující:

     Přístup k `'https://cors1.azurewebsites.net/api/values/GetValues2'` načtení `'https://cors3.azurewebsites.net'` z původu byl zablokován zásadami CORS: V požadovaném prostředku není k dispozici žádná hlavička "Access-Control-Allow-Origin". Pokud neprůhledná odpověď slouží vašim potřebám, nastavte režim požadavku na "no-cors" pro načtení prostředku se zakázaným CORS.
     
Koncové body s podporou CORS lze testovat pomocí nástroje, například [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)nebo [Postman](https://www.getpostman.com/). Při použití nástroje se počátek požadavku `Origin` určeného hlavičkou musí lišit od hostitele, který požadavek obdržel. Pokud požadavek není *cross-origin* na základě `Origin` hodnoty záhlaví:

* Není třeba, aby CORS Middleware zpracoval žádost.
* Hlavičky CORS nejsou vráceny v odpovědi.

Následující příkaz `curl` používá k vydání požadavku OPTIONS s informacemi:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Testování CORS s směrováním koncového bodu a [HttpOptions]

Povolení cors na základě koncového `RequireCors` bodu pomocí aktuálně ***nepodporuje*** [automatické požadavky kontroly před výstupem](#apf). Zvažte následující kód, který používá [směrování koncového bodu k povolení CORS](#ecors):

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Následující `TodoItems1Controller` obsahuje koncové body pro testování:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Otestujte předchozí kód ze [zkušební stránky](https://cors1.azurewebsites.net/test?number=1) nasazené [ukázky](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).

Tlačítka **Delete [EnableCors]** a **GET [EnableCors]** jsou `[EnableCors]` úspěšná, protože koncové body mají požadavky kontroly před výstupem a reagují na ně. Ostatní koncové body se nezdaří. Tlačítko **GET** se nezdaří, protože [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) odešle:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Následující `TodoItems2Controller` poskytuje podobné koncové body, ale obsahuje explicitní kód reagovat na požadavky options:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Otestujte předchozí kód ze [zkušební stránky](https://cors1.azurewebsites.net/test?number=2) nasazené ukázky. V rozevíracím seznamu **Kontroly kontroly před** výstupem vyberte **kontrola před výstupem** a pak **nastavit ovladač**. Všechna volání CORS `TodoItems2Controller` koncových bodů úspěšné.

## <a name="additional-resources"></a>Další zdroje

* [Sdílení prostředků mezi zdroji (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Začínáme s modulem IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento článek ukazuje, jak povolit CORS v aplikaci ASP.NET Core.

Zabezpečení prohlížeče zabraňuje webové stránce podávat žádosti do jiné domény, než je doména, která webovou stránku obsluhovala. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původu brání škodlivému webu ve čtení citlivých dat z jiného webu. Někdy můžete povolit jiným webům, aby do vaší aplikace vykládaly žádosti o více původů. Další informace naleznete v [článku Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Sdílení prostředků napříč původem](https://www.w3.org/TR/cors/) (CORS):

* Je standard W3C, který umožňuje serveru uvolnit zásady stejného původu.
* **Není** bezpečnostní funkce, CORS uvolňuje zabezpečení. Rozhraní API není bezpečnější povolením CORS. Další informace naleznete v tématu [Jak cors funguje](#how-cors).
* Umožňuje serveru explicitně povolit některé požadavky napříč původy při odmítnutí jiných.
* Je bezpečnější a flexibilnější než předchozí techniky, jako je [Například JSONP](/dotnet/framework/wcf/samples/jsonp).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="same-origin"></a>Stejný původ

Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Tyto dvě adresy URL mají stejný původ:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Tyto adresy URL mají jiný původ než předchozí dvě adresy URL:

* `https://example.net`&ndash; Jiná doména
* `https://www.example.com/foo.html`&ndash; Různé subdomény
* `http://example.com/foo.html`&ndash; Jiný režim
* `https://example.com:9000/foo.html`&ndash; Jiný port

Aplikace Internet Explorer nebere v úvahu port při porovnávání počátků.

## <a name="cors-with-named-policy-and-middleware"></a>CORS s názvem politiky a middleware

CORS Middleware zpracovává požadavky napříč původy. Následující kód umožňuje CORS pro celou aplikaci se zadaným původem:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Předcházející kód:

* Nastaví název zásady na "myAllowSpecificOrigins".\_ Název zásady je libovolný.
* Volá <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodu rozšíření, která umožňuje CORS.
* Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [lambda výrazem](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambda má <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> předmět. [Možnosti konfigurace](#cors-policy-options) `WithOrigins`, například , jsou popsány dále v tomto článku.

Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody přidá služby CORS do kontejneru služeb aplikace:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Další informace naleznete v [tématu možnosti zásad CORS](#cpo) v tomto dokumentu .

Metoda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> může řetězmetody, jak je znázorněno v následujícím kódu:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Poznámka: Adresa URL **nesmí** obsahovat`/`koncové lomítko ( ). Pokud adresa URL `/`končí pomocí `false` , vrátí porovnání a není vrácena žádná hlavička.

Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím CORS Middleware:
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
Poznámka: `UseCors` musí být `UseMvc`volána před .

Viz [Povolení CORS v Razor Stránky, ovladače a metody akce](#ecors) použít zásady CORS na stránce/kontroleru/akce úrovni.

Pokyny k testování kódu podobnépředchozímu kódu naleznete v [tématu Test CORS.](#test)

## <a name="enable-cors-with-attributes"></a>Povolení CORS s atributy

[ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) atribut poskytuje alternativu k použití CORS globálně. Atribut `[EnableCors]` umožňuje CORS pro vybrané koncové body, nikoli pro všechny koncové body.

Slouží `[EnableCors]` k určení výchozí `[EnableCors("{Policy String}")]` zásady a k určení zásady.

Atribut `[EnableCors]` lze použít na:

* Holicí strojek stránka`PageModel`
* Řadič
* Metoda akce řadiče

Můžete použít různé zásady pro řadič/stránku `[EnableCors]` model/akce s atributem. Pokud `[EnableCors]` je atribut použit pro řadiče/stránku model/akční metoda a CORS je povolena v middleware, jsou použity ***obě*** zásady. Doporučujeme ***nekombinovat*** zásady. Použijte `[EnableCors]` atribut nebo middleware, ***ne obojí**. Při `[EnableCors]`použití **nedefinujte** výchozí zásadu.

Následující kód platí pro každou metodu jinou zásadu:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Následující kód vytvoří výchozí zásadu CORS `"AnotherPolicy"`a zásadu s názvem :

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Zakázat CORS

Atribut [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) zakáže cors pro řadič/stránku model/akce.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Možnosti zásad CORS

Tato část popisuje různé možnosti, které lze nastavit v zásadách CORS:

* [Nastavení povolených počátků](#set-the-allowed-origins)
* [Nastavení povolených metod HTTP](#set-the-allowed-http-methods)
* [Nastavení záhlaví povolených požadavků](#set-the-allowed-request-headers)
* [Nastavení exponovaných hlaviček odpovědí](#set-the-exposed-response-headers)
* [Pověření v požadavcích na příčové počátky](#credentials-in-cross-origin-requests)
* [Nastavení doby vypršení platnosti kontroly před výstupem](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je přivolána . `Startup.ConfigureServices` U některých možností může být užitečné nejprve si přečíst část [Jak cors funguje.](#how-cors)

## <a name="set-the-allowed-origins"></a>Nastavení povolených počátků

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Umožňuje CORS požadavky ze všech původů`http` `https`s jakýmkoli schématem ( nebo ). `AllowAnyOrigin`je nejistá, protože *jakýkoli web* může do aplikace provádět požadavky napříč původy.

> [!NOTE]
> Zadání `AllowAnyOrigin` a `AllowCredentials` je nezabezpečená konfigurace a může mít za následek padělání požadavků mezi lokalitami. Pro zabezpečenou aplikaci zadejte přesný seznam původů, pokud klient musí autorizovat sám pro přístup k prostředkům serveru.

`AllowAnyOrigin`ovlivňuje požadavky kontroly `Access-Control-Allow-Origin` před výstupem a záhlaví. Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásady jako funkci, která umožňuje počátkům tak, aby odpovídala nakonfigurované doméně se zástupnými kartami při vyhodnocování, pokud je povolen původ.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Nastavení povolených metod HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Umožňuje libovolnou metodu HTTP:
* Ovlivňuje požadavky kontroly `Access-Control-Allow-Methods` před výstupem a záhlaví. Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Nastavení záhlaví povolených požadavků

Chcete-li povolit odeslání určitých hlaviček v požadavku CORS, *nazývaném hlavičky žádostí o autora*, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Chcete-li povolit všechny <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>hlavičky žádostí o autora, volejte :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Toto nastavení ovlivní požadavky `Access-Control-Request-Headers` kontroly před výstupem a záhlaví. Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)

CORS Middleware vždy umožňuje čtyři `Access-Control-Request-Headers` hlavičky v a) které mají být odeslány bez ohledu na hodnoty nakonfigurované v CorsPolicy.Headers. Tento seznam záhlaví obsahuje:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Zvažte například aplikaci nakonfigurovanou takto:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Cors Middleware úspěšně reaguje na požadavek kontroly před `Content-Language` výstupem s následující hlavičkou požadavku, protože je vždy na seznamu povolených:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Nastavení exponovaných hlaviček odpovědí

Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace. Další informace naleznete v tématu [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).

Záhlaví odpovědí, která jsou ve výchozím nastavení k dispozici, jsou:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědí*. Chcete-li aplikaci zpřístupnit další <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>záhlaví, zavolejte :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Pověření v požadavcích na příčové počátky

Pověření vyžadují zvláštní zpracování v požadavku CORS. Ve výchozím nastavení prohlížeč neodesílá přihlašovací údaje s požadavkem na příčný původ. Pověření zahrnují soubory cookie a schémata ověřování HTTP. Chcete-li odeslat pověření s požadavkem `XMLHttpRequest.withCredentials` na `true`příčný původ, musí klient nastavit .

Použití `XMLHttpRequest` přímo:

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

Použití rozhraní [API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Server musí povolit pověření. Chcete-li povolit pověření <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>pro více původů, volejte :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` záhlaví, které prohlížeči říká, že server povoluje pověření pro požadavek na příčný původ.

Pokud prohlížeč odešle pověření, ale odpověď neobsahuje platné `Access-Control-Allow-Credentials` záhlaví, prohlížeč nezveřejňuje odpověď na aplikaci a požadavek na příčný původ se nezdaří.

Povolení pověření pro více původ je bezpečnostní riziko. Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Specifikace CORS také uvádí, že `"*"` nastavení původu (všechny počátky) je neplatná, pokud `Access-Control-Allow-Credentials` je záhlaví k dispozici.

### <a name="preflight-requests"></a>Požadavky kontroly před výstupem

U některých požadavků CORS prohlížeč odešle další požadavek před provedením skutečné žádosti. Tento požadavek se nazývá *požadavek kontroly před výstupem*. Prohlížeč může přeskočit požadavek kontroly před výstupem, pokud jsou splněny následující podmínky:

* Metoda požadavku je GET, HEAD nebo POST.
* Aplikace nenastavuje záhlaví požadavků `Accept`kromě `Accept-Language` `Content-Language`, `Content-Type`, `Last-Event-ID`, nebo .
* Záhlaví, `Content-Type` pokud je nastaveno, má jednu z následujících hodnot:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Pravidlo pro hlavičky požadavku nastavené pro požadavek klienta se `setRequestHeader` vztahuje `XMLHttpRequest` na záhlaví, která aplikace nastaví voláním objektu. Specifikace CORS volá tyto hlavičky *záhlaví hlavičky požadavku autora*. Pravidlo se nevztahuje na záhlaví, která může `User-Agent`prohlížeč `Host`nastavit, například , nebo `Content-Length`.

Následuje příklad požadavku kontroly před výstupem:

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

Předletový požadavek používá metodu HTTP OPTIONS. Obsahuje dvě speciální hlavičky:

* `Access-Control-Request-Method`: Metoda HTTP, která bude použita pro skutečný požadavek.
* `Access-Control-Request-Headers`: Seznam hlaviček požadavků, které aplikace nastaví na skutečný požadavek. Jak již bylo uvedeno dříve, nezahrnuje záhlaví, která `User-Agent`prohlížeč nastaví, například .

<!-- I think this needs to be removed, was put here accidently -->

Pokud je cors povoleno s příslušnou zásadou, ASP.NET Core obecně automaticky reaguje na požadavky cors kontroly před výstupem. Požadavky na kontroly před výstupem naleznete v [atributu [HttpOptions].](#pro)

Požadavek kontroly před výstupem CORS může obsahovat hlavičku, `Access-Control-Request-Headers` která označuje serveru záhlaví, která jsou odeslána se skutečným požadavkem.

Chcete-li povolit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>určitá záhlaví, volejte :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Chcete-li povolit všechny <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>hlavičky žádostí o autora, volejte :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Prohlížeče nejsou zcela konzistentní v tom, `Access-Control-Request-Headers`jak nastavit . Pokud nastavíte záhlaví na `"*"` cokoli <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>jiného než (nebo `Content-Type`použít `Origin`), měli byste zahrnout alespoň `Accept`, a , plus všechny vlastní záhlaví, které chcete podporovat.

Následuje příklad odpovědi na požadavek kontroly před výstupem (za předpokladu, že server tento požadavek povolí):

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

Odpověď obsahuje `Access-Control-Allow-Methods` záhlaví, které obsahuje seznam povolených metod a volitelně `Access-Control-Allow-Headers` záhlaví, které obsahuje povolené hlavičky. Pokud je požadavek kontroly před výstupem úspěšný, prohlížeč odešle skutečný požadavek.

Pokud je požadavek kontroly před výstupem zamítnut, aplikace vrátí odpověď *200 OK,* ale neodešle záhlaví CORS zpět. Proto prohlížeč nepokouší požadavek křížového původu.

### <a name="set-the-preflight-expiration-time"></a>Nastavení doby vypršení platnosti kontroly před výstupem

Záhlaví `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek kontroly před výstupem uložena do mezipaměti. Chcete-li nastavit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>toto záhlaví, volejte :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Jak CORS funguje

Tato část popisuje, co se děje v požadavku [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.

* CORS **není** funkce zabezpečení. CORS je standard W3C, který umožňuje serveru uvolnit zásady stejného původu.
  * Škodlivý objekt actor může například použít [použít zabránit skriptování mezi webovými servery (XSS)](xref:security/cross-site-scripting) proti vašemu webu a provést požadavek na více webů na jejich webu s povoleným cors, aby ukradl informace.
* Vaše rozhraní API není bezpečnější povolením CORS.
  * Je na klientovi (prohlížeči), aby vynucoval CORS. Server provede požadavek a vrátí odpověď, je klient, který vrátí chybu a blokuje odpověď. Například některý z následujících nástrojů zobrazí odpověď serveru:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [Httpklient rozhraní .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Webový prohlížeč zadáním adresy URL do adresního řádku.
* Je to způsob, jak server umožňuje prohlížečům spustit požadavek [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro křížový původ, který by jinak byl zakázán.
  * Prohlížeče (bez CORS) nemohou dělat požadavky na příčný původ. Před CORS byl [jsonp](https://www.w3schools.com/js/js_json_jsonp.asp) použit k obcházení tohoto omezení. JSONP nepoužívá XHR, používá `<script>` značku pro příjem odpovědi. Skripty mohou být načteny cross-origin.

[Specifikace CORS](https://www.w3.org/TR/cors/) zavedla několik nových hlaviček HTTP, které umožňují požadavky na příčný původ. Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky automaticky pro požadavky křížového původu. Vlastní kód JavaScriptu není nutné povolit CORS.

Následuje příklad požadavku na příčný původ. Záhlaví `Origin` poskytuje doménu webu, který podává požadavek. Záhlaví `Origin` je povinné a musí se lišit od hostitele.

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

Pokud server umožňuje požadavek, `Access-Control-Allow-Origin` nastaví záhlaví v odpovědi. Hodnota tohoto záhlaví se `Origin` shoduje s hlavičkou z požadavku `"*"`nebo je zástupnou hodnotou , což znamená, že je povolen libovolný původ:

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

Pokud odpověď neobsahuje `Access-Control-Allow-Origin` záhlaví, požadavek mezi původem se nezdaří. Konkrétně prohlížeč zakáže požadavek. I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupní odpověď klientské aplikaci.

<a name="test"></a>

## <a name="test-cors"></a>Test CORS

Chcete-li otestovat CORS:

1. [Vytvořte projekt rozhraní API](xref:tutorials/first-web-api). Případně si můžete [stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Povolte CORS pomocí jednoho z přístupů v tomto dokumentu. Příklad:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`by měl být použit pouze pro testování ukázkové aplikace podobné [ukázkový kód ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Vytvořte projekt webové aplikace (Razor Pages nebo MVC). Ukázka používá Razor Pages. Webovou aplikaci můžete vytvořit ve stejném řešení jako projekt rozhraní API.
1. Do souboru *Index.cshtml* přidejte následující zvýrazněný kód:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresou URL nasazené aplikace.
1. Nasaďte projekt rozhraní API. Například [nasazení do Azure](xref:host-and-deploy/azure-apps/index).
1. Spusťte aplikaci Razor Pages nebo MVC z plochy a klikněte na tlačítko **Test.** Pomocí nástrojů F12 zkontrolujte chybové zprávy.
1. Odeberte původ `WithOrigins` localhost z aplikace a nasadit aplikaci. Případně spusťte klientskou aplikaci s jiným portem. Například spustit z Visual Studio.
1. Test ujte pomocí klientské aplikace. Selhání CORS vrátí chybu, ale chybová zpráva není k dispozici pro JavaScript. K zobrazení chyby použijte kartu konzoly v nástrojích F12. V závislosti na prohlížeči se zobrazí chyba (v konzole nástrojů F12) podobná následující:

   * Použití microsoft edge:

     **SEC7120: [CORS] `https://localhost:44375` Původ nebyl `https://localhost:44375` našel v hlavičce odpovědi Access-Control-Allow-Origin pro prostředek křížového původu na adrese`https://webapi.azurewebsites.net/api/values/1`**

   * Používání Chromu:

     **Přístup k XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` na počátku byl zablokován zásadami CORS: Na požadovaném prostředku není k dispozici žádná hlavička "Access-Control-Allow-Origin".**
     
Koncové body s podporou CORS lze testovat pomocí nástroje, například [Fiddler](https://www.telerik.com/fiddler) nebo [Postman](https://www.getpostman.com/). Při použití nástroje se počátek požadavku `Origin` určeného hlavičkou musí lišit od hostitele, který požadavek obdržel. Pokud požadavek není *cross-origin* na základě `Origin` hodnoty záhlaví:

* Není třeba, aby CORS Middleware zpracoval žádost.
* Hlavičky CORS nejsou vráceny v odpovědi.

## <a name="cors-in-iis"></a>CORS ve S.r.o.

Při nasazování do služby IIS musí být cors spuštěn před ověřováním systému Windows, pokud server není nakonfigurován tak, aby umožňoval anonymní přístup. Pro podporu tohoto scénáře je třeba nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.

## <a name="additional-resources"></a>Další zdroje

* [Sdílení prostředků mezi zdroji (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Začínáme s modulem IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
