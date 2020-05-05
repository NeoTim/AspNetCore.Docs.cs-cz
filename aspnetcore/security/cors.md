---
title: Povolit žádosti mezi zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak CORS jako standard pro povolení nebo odmítnutí žádostí o více zdrojů v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: 6f523a21fe8119c2e4ca4f751ac5b6abc686404b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773808"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Povolit žádosti mezi zdroji (CORS) v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)

Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality. V některých případech můžete chtít umožnit jiným webům, aby vaši aplikaci mohli vytvářet žádosti o více zdrojů. Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):

* Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.
* Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit. Rozhraní API není bezpečnější díky povolení CORS. Další informace najdete v tématu [jak CORS funguje](#how-cors).
* Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.
* Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([Jak stáhnout](xref:index#how-to-download-a-sample))

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

## <a name="enable-cors"></a>Povolení CORS

Existují tři způsoby, jak povolit CORS:

* V middlewaru pomocí [pojmenované zásady](#np) nebo [výchozí zásady](#dp).
* Použití [Směrování koncového bodu](#ecors).
* S atributem [[EnableCors]](#attr) .

Použití atributu [[EnableCors]](#attr) s pojmenovanou zásadou poskytuje ovládací prvek nejlepší v omezení koncových bodů, které podporují CORS.

Každý přístup je podrobně popsaný v následujících částech.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS s pojmenovanými zásadami a middlewarem

Middleware CORS zpracovává požadavky mezi zdroji. Následující kód aplikuje zásadu CORS na všechny koncové body aplikace se zadanými zdroji:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

Předcházející kód:

* Nastaví název zásady na `_myAllowSpecificOrigins`. Název zásady je libovolný.
* Zavolá <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodu rozšíření a určí zásadu `_myAllowSpecificOrigins` CORS. `UseCors`Přidá middleware CORS. Volání `UseCors` musí být umístěno po `UseRouting`, ale před. `UseAuthorization` Další informace najdete v tématu [pořadí middlewaru](xref:fundamentals/middleware/index#middleware-order).
* Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambda převezme <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objekt. [Možnosti konfigurace](#cors-policy-options), například `WithOrigins`, jsou popsány dále v tomto článku.
* Povolí zásadu `_myAllowSpecificOrigins` CORS pro všechny koncové body řadiče. Pokud chcete použít zásadu CORS na konkrétní koncové body, podívejte se na téma [Směrování koncového bodu](#ecors) .

Při směrování koncových bodů ***musí*** být MIDDLEWARe CORS nakonfigurované tak, aby se `UseRouting` spustilo mezi voláními a `UseEndpoints`.

Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#testc) .

Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody přidá služby CORS do kontejneru služby aplikace:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Metody mohou být zřetězeny, jak je znázorněno v následujícím kódu:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Poznámka: Zadaná adresa URL nesmí **obsahovat koncové** lomítko (`/`). Pokud adresa URL končí `/`, porovnávání se vrátí `false` a nevrátí se žádné záhlaví.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS s výchozími zásadami a middlewarem

Následující zvýrazněný kód umožňuje výchozí zásadu CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Předchozí kód aplikuje výchozí zásadu CORS na všechny koncové body řadiče.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Povolení CORS s směrováním koncových bodů

Povolení CORS na základě jednotlivých koncových bodů pomocí `RequireCors` v současné ***době*** nepodporuje [Automatické požadavky na kontrolu před výstupem](#apf). Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/aspnetcore/issues/20709) a [testování CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer).

S směrováním koncových bodů je možné CORS povolit na základě jednotlivých koncových bodů <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> pomocí sady rozšiřujících metod:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

V předchozím kódu:

* `app.UseCors`povoluje middleware CORS. Vzhledem k tomu, že výchozí zásady nejsou `app.UseCors()` nakonfigurované, nepovolí CORS samotné.
* Koncové `/echo` body řadiče a umožňují žádosti mezi zdroji pomocí zadaných zásad.
* Koncové `/echo2` body Razor stránky a ***nepovolují požadavky*** mezi zdroji, protože nebyly zadány žádné výchozí zásady.

Atribut [[DisableCors]](#dc) ***nevypne CORS*** , která byla povolená směrováním koncových bodů `RequireCors`s.

Pokyny k testování kódu podobného předchozímu najdete v tématu [test CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer) .

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Povolení CORS s atributy

Povolení CORS s atributem [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) a použití pojmenované zásady pouze na koncové body, které vyžadují CORS, poskytuje ovládací prvek nejlepší.

Atribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) poskytuje alternativu k použití CORS globálně. `[EnableCors]` Atribut umožňuje CORS pro vybrané koncové body místo všech koncových bodů:

* `[EnableCors]`Určuje výchozí zásadu.
* `[EnableCors("{Policy String}")]`Určuje pojmenovanou zásadu.

`[EnableCors]` Atribut lze použít pro:

* RazorPage`PageModel`
* Kontrolér
* Metoda akce kontroleru

U řadičů, modelů stránek nebo metod akcí lze použít různé zásady s `[EnableCors]` atributem. Pokud je `[EnableCors]` atribut použit na řadič, model stránky nebo metodu akce a CORS je v middleware povolená, uplatní se ***obě*** zásady. Doporučujeme ***před kombinováním zásad. Použijte*** atribut ***nebo middleware, nikoli oba ve stejné aplikaci.*** `[EnableCors]`

Následující kód používá pro každou metodu jinou zásadu:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Následující kód vytvoří dvě zásady CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Pro ovládací prvek nejlepší pro omezení požadavků CORS:

* Použijte `[EnableCors("MyPolicy")]` s pojmenovanou zásadou.
* Nedefinujte výchozí zásady.
* Nepoužívejte [Směrování koncových bodů](#ecors).

Kód v další části splňuje předchozí seznam.

Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#testc) .

<a name="dc"></a>

### <a name="disable-cors"></a>Zakázání CORS

Atribut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***zakáže rozhraní*** CORS, které bylo povoleno [směrováním koncových bodů](#ecors).

Následující kód definuje zásadu `"MyPolicy"`CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Následující kód zakáže CORS pro `GetValues2` akci:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Předcházející kód:

* Nepovoluje CORS s [směrováním koncových bodů](#ecors).
* Nedefinuje [výchozí zásadu CORS](#dp).
* Pro povolení zásad `"MyPolicy"` CORS pro kontroler používá [[EnableCors ("MyPolicy")]](#attr) .
* Zakáže CORS pro `GetValues2` metodu.

Pokyny k testování předchozího kódu najdete v části [test CORS](#testc) .

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

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem`http` ( `https`nebo). `AllowAnyOrigin`je nezabezpečené, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.

> [!NOTE]
> Určení `AllowAnyOrigin` a `AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby. Služba CORS vrátí neplatnou odpověď CORS, pokud je aplikace nakonfigurovaná pomocí obou metod.

`AllowAnyOrigin`má vliv na požadavky na `Access-Control-Allow-Origin` kontrolu a hlavičku. Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásady na funkci, která umožňuje, aby se při vyhodnocování, jestli je původ povolený, shodovaly s konfigurovanou doménou se zástupnými znaky.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Nastavení povolených metod HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Umožňuje jakoukoli metodu HTTP:
* Má vliv na požadavky na `Access-Control-Allow-Methods` kontrolu a hlavičku. Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Nastavení povolených hlaviček žádosti

Pokud chcete povolit odeslání konkrétních hlaviček v žádosti CORS s názvem [záhlaví žádosti o autora](https://xhr.spec.whatwg.org/#request), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Pokud chcete povolení všech [hlaviček žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`má vliv na žádosti o předběžné kontroly a hlavičku [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) . Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .

Zásada middlewaru CORS odpovídající konkrétním hlavičkám, které `WithHeaders` určuje, je možná jenom v případě, `Access-Control-Request-Headers` že se záhlaví poslala přesně `WithHeaders`a odpovídají hlavičkám uvedeným v.

Představte si například aplikaci nakonfigurovanou takto:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

Middleware CORS odmítá požadavek na předběžné kontroly s následující hlavičkou `Content-Language` požadavku, protože ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) `WithHeaders`není uvedená v tomto seznamu:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky. Proto prohlížeč nezkouší požadavek mezi zdroji.

### <a name="set-the-exposed-response-headers"></a>Nastavení hlaviček vystavené odpovědi

Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace. Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): jednoduchá hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).

Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*. Pokud chcete, aby byla aplikace k dispozici ostatním <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>hlavičkám, zavolejte:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
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

Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Server musí přihlašovací údaje umožňovat. Pokud chcete povolení přihlašovacích údajů mezi zdroji <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>, zavolejte:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` hlavičku, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.

Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou `Access-Control-Allow-Credentials` hlavičku, prohlížeč nezveřejňuje odpověď na aplikaci a žádost o více zdrojů se nezdařila.

Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko. Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Specifikace CORS také uvádí, že pokud je `"*"` `Access-Control-Allow-Credentials` hlavička k dispozici, nastavení původu na (všechny zdroje) je neplatné.

<a name="pref"></a>

## <a name="preflight-requests"></a>Požadavky na kontrolu před výstupem

U některých požadavků CORS prohlížeč před provedením samotné žádosti pošle žádost o další [Možnosti](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) . Tento požadavek se nazývá [žádost o kontrolu před výstupem](https://developer.mozilla.org/docs/Glossary/Preflight_request). Prohlížeč může přeskočit požadavek na kontrolu před výstupem, pokud jsou splněné ***všechny*** následující podmínky:

* Metoda Request je GET, HEAD nebo POST.
* Aplikace nenastaví záhlaví `Accept`žádostí s výjimkou, `Accept-Language`, `Content-Language` `Content-Type`, nebo. `Last-Event-ID`
* `Content-Type` Záhlaví, pokud je nastaveno, má jednu z následujících hodnot:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada aplikací zavolá `setRequestHeader` na `XMLHttpRequest` objekt. Specifikace CORS volá [záhlaví požadavku autora](https://www.w3.org/TR/cors/#author-request-headers)záhlaví. Pravidlo se nevztahuje na hlavičky, které může prohlížeč nastavit, například `User-Agent`, `Host`nebo. `Content-Length`

Následuje příklad odpovědi, která se podobá žádosti o kontrolu před výstupem vytvořeným tlačítkem **[PUT test]** v části [test CORS](#testc) tohoto dokumentu.

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

Požadavek na kontrolu před výstupem používá metodu [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) . Může obsahovat následující hlavičky:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): metoda HTTP, která bude použita pro skutečný požadavek.
* [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku. Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent`.
* [Přístup-řízení-povolení-metody](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí `200 OK` odpověď, ale nenastaví hlavičky CORS. Proto prohlížeč nezkouší požadavek mezi zdroji. Příklad Zamítnuté žádosti o kontrolu před výstupem najdete v části [test CORS](#testc) tohoto dokumentu.

Pomocí nástrojů F12 zobrazuje aplikace konzoly chybu podobnou jedné z následujících v závislosti na prohlížeči:

* Firefox: požadavek mezi zdroji je zablokován: stejné zásady původu nepovolují čtení vzdáleného prostředku v `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`. (Důvod: požadavek CORS se nezdařil) [Další informace](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Chromová: přístup k načtení v 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' od původu 'https://cors3.azurewebsites.net' byl zablokován zásadou CORS: odpověď na požadavek na kontrolu před výstupem neprojde kontrolou řízení přístupu: v požadovaném prostředku není k dispozici hlavička ' Access-Control-Allow-Origin '. Pokud neprůhledná odpověď vyhovuje vašim potřebám, nastavte režim žádosti na No-CORS, aby bylo možné načíst prostředek s zakázáním CORS.

Pro povolení konkrétních hlaviček zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Pokud chcete povolení všech [hlaviček žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Prohlížeče nejsou konzistentní v tom, jak `Access-Control-Request-Headers`jsou nastavené. Pokud buď:

* Záhlaví jsou nastavena na jinou hodnotu než`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>se nazývá: uveďte aspoň `Accept`, `Content-Type`a a `Origin`a všechny vlastní hlavičky, které chcete podporovat.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Automatický kód žádosti o kontrolu

Když se aplikuje zásada CORS, proveďte jednu z těchto akcí:

* Globálně voláním `app.UseCors` v `Startup.Configure`.
* Pomocí `[EnableCors]` atributu.

ASP.NET Core reaguje na požadavek na předběžné možnosti.

Povolení CORS na základě jednotlivých koncových bodů pomocí `RequireCors` v současné ***době nepodporuje automatické*** požadavky na kontrolu před výstupem.

V části [test CORS](#testc) tohoto dokumentu se toto chování demonstruje.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions] atribut pro žádosti o kontrolu před výstupem

Když je v rámci příslušné zásady povolená CORS, ASP.NET Core obecně reaguje na požadavky na předběžné kontroly CORS automaticky. V některých scénářích to nemusí být případ. Například použití [CORS se směrováním koncových bodů](#ecors).

Následující kód používá atribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) k vytvoření koncových bodů pro žádosti o možnosti:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Pokyny k testování předchozího kódu najdete v článku [test CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer) .

### <a name="set-the-preflight-expiration-time"></a>Nastavit čas vypršení platnosti předběžné kontroly

Záhlaví `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti. Chcete-li nastavit tuto hlavičku <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>, zavolejte:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Jak CORS funguje

Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.

* CORS není **funkce** zabezpečení. CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.
  * Škodlivý objekt actor může například použít [skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s POVOLENým CORS, aby mohl ukrást informace.
* Rozhraní API není bezpečnější díky povolení CORS.
  * Pro vymáhání CORS je to až klient (prohlížeč). Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď. Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Webový prohlížeč zadáním adresy URL do panelu Adresa.
* To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.
  * Prohlížeče bez CORS nemůžou provádět žádosti mezi zdroji. Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) . JSONP nepoužívá XHR, používá `<script>` značku k přijetí odpovědi. Skripty mohou být načteny mezi zdroji.

[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji. Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky. Pro povolení CORS není nutný vlastní kód JavaScriptu.

[Tlačítko Vložit test](https://cors3.azurewebsites.net/test) na nasazené [ukázce](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

Následuje příklad žádosti o více zdrojů z tlačítka Test [hodnot](https://cors3.azurewebsites.net/) na `https://cors1.azurewebsites.net/api/values`. `Origin` Hlavička:

* Poskytuje doménu lokality, ze které se vytváří požadavek.
* Je vyžadováno a musí se lišit od hostitele.

**Obecné hlavičky**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Hlavičky odpovědi**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**Hlavičky požadavku**

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

V `OPTIONS` části požadavky Server nastaví hlavičku **hlaviček** `Access-Control-Allow-Origin: {allowed origin}` odpovědi v odpovědi. Například nasazená [Ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [DELETE [EnableCors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` obsahuje následující hlavičky:

**Obecné hlavičky**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Hlavičky odpovědi**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**Hlavičky požadavku**

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

V předchozích **hlavičkách odpovědi**Server nastaví hlavičku [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) v odpovědi. `https://cors1.azurewebsites.net` Hodnota této hlavičky se shoduje s `Origin` hlavičkou z požadavku.

Pokud <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> je volána, `Access-Control-Allow-Origin: *`je vrácena hodnota zástupného znaku. `AllowAnyOrigin`umožňuje jakýkoli původ.

Pokud odpověď nezahrnuje `Access-Control-Allow-Origin` hlavičku, požadavek na více zdrojů se nezdařil. Konkrétně prohlížeč požadavek nepovoluje. I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.

<a name="options"></a>

### <a name="display-options-requests"></a>Zobrazit žádosti o možnosti

Ve výchozím nastavení prohlížeče Chrome a Edge nezobrazují žádosti o možnosti na kartě síť nástrojů F12. Chcete-li zobrazit žádosti o možnosti v těchto prohlížečích:

* `chrome://flags/#out-of-blink-cors` nebo `edge://flags/#out-of-blink-cors`
* zakažte příznak.
* restart (restartovat).

Firefox ve výchozím nastavení zobrazuje požadavky na možnosti.

## <a name="cors-in-iis"></a>CORS ve službě IIS

Při nasazování do služby IIS musí CORS běžet před ověřováním systému Windows, pokud server není nakonfigurovaný tak, aby povoloval anonymní přístup. Pro podporu tohoto scénáře je nutné nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.

<a name="testc"></a>

## <a name="test-cors"></a>Test CORS

[Ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) obsahuje kód pro testování CORS. Viz [Jak stáhnout](xref:index#how-to-download-a-sample). Ukázka je projekt rozhraní API se Razor stránkami přidanými:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`by se mělo používat jenom pro testování ukázkové aplikace, podobně jako u [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).

Následující příklad `ValuesController` poskytuje koncové body pro testování:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) je k dispozici v balíčku NuGet [Rick. Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) a zobrazí informace o trasách.

Otestujte předchozí vzorový kód pomocí jednoho z následujících přístupů:

* Použijte nasazenou ukázkovou aplikaci [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)na adrese. Není nutné stahovat ukázku.
* Spusťte ukázku s `dotnet run` použitím výchozí adresy URL `https://localhost:5001`.
* Spusťte ukázku ze sady Visual Studio s portem nastaveným na 44398 pro adresu URL `https://localhost:44398`.

Použití prohlížeče s nástroji F12:

* Vyberte tlačítko **hodnoty** a zkontrolujte hlavičky na kartě **síť** .
* Vyberte tlačítko **Vložit test** . Pokyny k zobrazení žádosti o možnosti najdete v tématu [Možnosti zobrazení žádosti](#options) . **Test Put** vytvoří dvě požadavky, požadavek na kontrolu před výstupem a požadavek PUT.
* Kliknutím na **`GetValues2 [DisableCors]`** tlačítko aktivujte neúspěšnou žádost CORS. Jak je uvedeno v dokumentu, odpověď vrátí 200 úspěch, ale požadavek CORS se neprovádí. Kliknutím na kartu **Konzola** zobrazíte chybu CORS. V závislosti na prohlížeči se zobrazí chybová zpráva podobná následující:

     Zásada CORS zablokovala přístup k načtení `'https://cors1.azurewebsites.net/api/values/GetValues2'` od původu `'https://cors3.azurewebsites.net'` : v požadovaném prostředku není k dispozici hlavička Access-Control-Allow-Origin. Pokud neprůhledná odpověď vyhovuje vašim potřebám, nastavte režim žádosti na No-CORS, aby bylo možné načíst prostředek s zakázáním CORS.
     
Koncové body s podporou CORS je možné testovat pomocí nástroje, jako je například [kudrlinkou](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)nebo [post](https://www.getpostman.com/). Při použití nástroje se musí původ požadavku určeného `Origin` hlavičkou lišit od hostitele, který požadavek přijal. Pokud požadavek není *mezi zdroji* založen na hodnotě `Origin` hlavičky:

* Pro zpracování žádosti není nutné middleware CORS.
* V odpovědi nejsou vraceny hlavičky CORS.

Následující příkaz slouží `curl` k vystavení žádosti o možnosti s informacemi:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Testování CORS pomocí směrování koncových bodů a [HttpOptions]

Povolení CORS na základě jednotlivých koncových bodů pomocí `RequireCors` v současné ***době*** nepodporuje [Automatické požadavky na kontrolu před výstupem](#apf). Vezměte v úvahu následující kód, který pomocí [Směrování koncových bodů povoluje CORS](#ecors):

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Následující příklad `TodoItems1Controller` poskytuje koncové body pro testování:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Otestujte předchozí kód ze [stránky test](https://cors1.azurewebsites.net/test?number=1) v nasazené [ukázce](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).

Tlačítka **DELETE [EnableCors]** a **Get [EnableCors]** jsou úspěšná, protože koncové body mají `[EnableCors]` a reagují na požadavky na kontrolu před výstupem. Ostatní koncové body selžou. Tlačítko **získat** se nepovedlo, protože [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) pošle:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Následující `TodoItems2Controller` příkaz poskytuje podobné koncové body, ale obsahuje explicitní kód pro reakci na požadavky Options:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Otestujte předchozí kód ze [stránky test](https://cors1.azurewebsites.net/test?number=2) v nasazené ukázce. V rozevíracím seznamu **řadič** vyberte položku **Kontrola před výstupem** a pak **nastavte kontroler**. Všechna volání CORS do koncových `TodoItems2Controller` bodů jsou úspěšná.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Sdílení prostředků mezi zdroji (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Začínáme s modulem IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality. V některých případech můžete chtít, aby ostatní weby ve vaší aplikaci provedly žádosti o více zdrojů. Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

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

* `https://example.net`&ndash; Jiná doména
* `https://www.example.com/foo.html`&ndash; Jiná subdoména
* `http://example.com/foo.html`&ndash; Jiné schéma
* `https://example.com:9000/foo.html`&ndash; Jiný port

Internet Explorer při porovnávání zdrojů nebere v úvahu port.

## <a name="cors-with-named-policy-and-middleware"></a>CORS s pojmenovanými zásadami a middlewarem

Middleware CORS zpracovává požadavky mezi zdroji. Následující kód umožňuje CORS pro celou aplikaci se zadaným počátkem:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Předcházející kód:

* Nastaví název zásady na "\_myAllowSpecificOrigins". Název zásady je libovolný.
* Volá metodu <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> rozšíření, která umožňuje CORS.
* Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambda převezme <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objekt. [Možnosti konfigurace](#cors-policy-options), například `WithOrigins`, jsou popsány dále v tomto článku.

Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody přidá služby CORS do kontejneru služby aplikace:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Metoda může řetězit metody, jak je znázorněno v následujícím kódu:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Poznámka: adresa URL nesmí **obsahovat koncové** lomítko (`/`). Pokud adresa URL končí `/`, porovnávání se vrátí `false` a nevrátí se žádné záhlaví.

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

Viz [Povolení CORS v Razor rámci stránek, řadičů a metod akcí](#ecors) pro aplikování zásad CORS na úrovni stránky, řadiče nebo akce.

Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#test) .

## <a name="enable-cors-with-attributes"></a>Povolení CORS s atributy

Atribut [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) představuje alternativu k použití CORS globálně. `[EnableCors]` Atribut povoluje CORS pro vybrané koncové body místo všech koncových bodů.

Slouží `[EnableCors]` k zadání výchozích zásad a `[EnableCors("{Policy String}")]` k určení zásad.

`[EnableCors]` Atribut lze použít pro:

* RazorPage`PageModel`
* Kontrolér
* Metoda akce kontroleru

S `[EnableCors]` atributem lze použít různé zásady pro kontroler/Page-model/Action. Pokud je `[EnableCors]` atribut použit pro řadiče/stránky modelu/akce a v middlewaru je povoleno CORS, jsou ***obě*** zásady aplikovány. ***Doporučujeme, abyste nekombinují zásady*** . Použijte `[EnableCors]` atribut nebo middleware, ***ne obojí**. Při použití `[EnableCors]`nástroje **nedefinujte výchozí** zásady.

Následující kód používá pro každou metodu jinou zásadu:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Následující kód vytvoří výchozí zásadu CORS a zásadu s názvem `"AnotherPolicy"`:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Zakázání CORS

Atribut [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) zakáže CORS pro kontroler/Page-model/Action.

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

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem`http` ( `https`nebo). `AllowAnyOrigin`je nezabezpečené, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.

> [!NOTE]
> Určení `AllowAnyOrigin` a `AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby. V případě zabezpečené aplikace zadejte přesný seznam zdrojů, pokud je klient musí autorizovat pro přístup k prostředkům serveru.

`AllowAnyOrigin`má vliv na požadavky na `Access-Control-Allow-Origin` kontrolu a hlavičku. Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásady na funkci, která umožňuje, aby se při vyhodnocování, jestli je původ povolený, shodovaly s konfigurovanou doménou se zástupnými znaky.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

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

### <a name="set-the-exposed-response-headers"></a>Nastavení hlaviček vystavené odpovědi

Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace. Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): jednoduchá hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).

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

Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):

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

Specifikace CORS také uvádí, že pokud je `"*"` `Access-Control-Allow-Credentials` hlavička k dispozici, nastavení původu na (všechny zdroje) je neplatné.

### <a name="preflight-requests"></a>Požadavky na kontrolu před výstupem

U některých požadavků CORS prohlížeč před provedením samotného požadavku pošle další požadavek. Tento požadavek se nazývá *žádost o kontrolu před výstupem*. Prohlížeč může požadavek na předběžné kontroly přeskočit, pokud jsou splněné následující podmínky:

* Metoda Request je GET, HEAD nebo POST.
* Aplikace nenastaví záhlaví `Accept`žádostí s výjimkou, `Accept-Language`, `Content-Language` `Content-Type`, nebo. `Last-Event-ID`
* `Content-Type` Záhlaví, pokud je nastaveno, má jednu z následujících hodnot:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada aplikací zavolá `setRequestHeader` na `XMLHttpRequest` objekt. Specifikace CORS volá *záhlaví požadavku autora*záhlaví. Pravidlo se nevztahuje na hlavičky, které může prohlížeč nastavit, například `User-Agent`, `Host`nebo. `Content-Length`

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

<!-- I think this needs to be removed, was put here accidently -->

Když je v rámci příslušné zásady povolená CORS, ASP.NET Core všeobecně automaticky reagovat na požadavky CORS v rámci kontroly. V [případě požadavků na kontrolu před výstupem se podívejte na atribut [HttpOptions]](#pro).

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

Odpověď obsahuje `Access-Control-Allow-Methods` hlavičku, která obsahuje seznam povolených metod a volitelně `Access-Control-Allow-Headers` záhlaví, ve kterém jsou uvedeny povolené hlavičky. Pokud je žádost o kontrolu před výstupem úspěšná, prohlížeč pošle skutečný požadavek.

Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky. Proto prohlížeč nezkouší požadavek mezi zdroji.

### <a name="set-the-preflight-expiration-time"></a>Nastavit čas vypršení platnosti předběžné kontroly

Záhlaví `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti. Chcete-li nastavit tuto hlavičku <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>, zavolejte:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Jak CORS funguje

Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.

* CORS není **funkce** zabezpečení. CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.
  * Škodlivý objekt actor by například mohl použít možnost [zabránit skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s povoleným CORS, aby mohl ukrást informace.
* Vaše rozhraní API není bezpečnější díky povolení CORS.
  * Pro vymáhání CORS je to až klient (prohlížeč). Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď. Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Webový prohlížeč zadáním adresy URL do panelu Adresa.
* To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.
  * Prohlížeče (bez CORS) nemůžou provádět žádosti mezi zdroji. Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) . JSONP nepoužívá XHR, používá `<script>` značku k přijetí odpovědi. Skripty mohou být načteny mezi zdroji.

[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji. Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky. Pro povolení CORS není nutný vlastní kód JavaScriptu.

Následuje příklad žádosti o více zdrojů. `Origin` Hlavička poskytuje doménu lokality, ze které se vytváří požadavek. `Origin` Hlavička je povinná a musí se lišit od hostitele.

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

Pokud server tuto žádost povoluje, nastaví `Access-Control-Allow-Origin` hlavičku v odpovědi. Hodnota této hlavičky se shoduje s `Origin` hlavičkou z požadavku nebo se jedná o zástupnou `"*"`hodnotu, což znamená, že je povolen libovolný původ:

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

1. [Vytvořte projekt API](xref:tutorials/first-web-api). Alternativně si můžete [Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Povolte CORS pomocí jednoho z přístupů v tomto dokumentu. Příklad:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`by se mělo používat jenom pro testování ukázkové aplikace, podobně jako u [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Vytvořte projekt webové aplikace (Razor stránky nebo MVC). Ukázka používá Razor stránky. Webovou aplikaci můžete vytvořit ve stejném řešení jako projekt rozhraní API.
1. Do souboru *index. cshtml* přidejte následující zvýrazněný kód:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresu URL nasazené aplikace.
1. Nasaďte projekt API. Nasaďte například [do Azure](xref:host-and-deploy/azure-apps/index).
1. Spusťte Razor stránky nebo aplikaci MVC z plochy a klikněte na tlačítko **test** . Pomocí nástrojů F12 zkontrolujte chybové zprávy.
1. Odeberte původ localhost z `WithOrigins` a nasaďte aplikaci. Případně spusťte klientskou aplikaci s jiným portem. Například spusťte ze sady Visual Studio.
1. Otestujte pomocí klientské aplikace. Chyby CORS vracejí chybu, ale chybová zpráva není k dispozici pro JavaScript. K zobrazení chyby použijte kartu konzola v nástrojích F12. V závislosti na prohlížeči se zobrazí chyba (v konzole nástrojů F12), která je podobná následující:

   * Používání Microsoft Edge:

     **SEC7120: [CORS] původ `https://localhost:44375` nebyl nalezen `https://localhost:44375` v hlavičce odpovědi Access-Control-Allow-Origin pro prostředek pro více zdrojů.`https://webapi.azurewebsites.net/api/values/1`**

   * Použití Chrome:

     **Zásada CORS zablokovala přístup k XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` od původu `https://localhost:44375` : v požadovaném prostředku není k dispozici hlavička Access-Control-Allow-Origin.**
     
Koncové body s podporou CORS se dají testovat pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [post](https://www.getpostman.com/). Při použití nástroje se musí původ požadavku určeného `Origin` hlavičkou lišit od hostitele, který požadavek přijal. Pokud požadavek není *mezi zdroji* založen na hodnotě `Origin` hlavičky:

* Pro zpracování žádosti není nutné middleware CORS.
* V odpovědi nejsou vraceny hlavičky CORS.

## <a name="cors-in-iis"></a>CORS ve službě IIS

Při nasazování do služby IIS musí CORS běžet před ověřováním systému Windows, pokud server není nakonfigurovaný tak, aby povoloval anonymní přístup. Pro podporu tohoto scénáře je nutné nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Sdílení prostředků mezi zdroji (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Začínáme s modulem IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
