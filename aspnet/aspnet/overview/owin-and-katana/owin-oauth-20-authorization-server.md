---
uid: aspnet/overview/owin-and-katana/owin-oauth-20-authorization-server
title: Autorizační Server OWIN OAuth 2.0 | Dokumentace Microsoftu
author: hongyes
description: Tento kurz vám pomůže o tom, jak implementovat serveru autorizace OAuth 2.0 pomocí middlewaru OWIN OAuth. To je pokročilá kurzu této pouze Nastav...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 20acee16-c70c-41e9-b38f-92bfcf9a4c1c
msc.legacyurl: /aspnet/overview/owin-and-katana/owin-oauth-20-authorization-server
msc.type: authoredcontent
ms.openlocfilehash: b8451d2d9e346bd5e2f51ba45e48030a5221b549
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667645"
---
# <a name="owin-oauth-20-authorization-server"></a>Autorizační server OWIN OAuth 2.0

> Tento kurz vám pomůže o tom, jak implementovat serveru autorizace OAuth 2.0 pomocí middlewaru OWIN OAuth. Toto je rozšířený výukový program, který popisuje pouze kroky pro vytvoření autorizační Server OWIN OAuth 2.0. Toto není podrobný kurz. [Stáhněte si ukázkový kód](https://code.msdn.microsoft.com/OWIN-OAuth-20-Authorization-ba2b8783/file/114932/1/AuthorizationServer.zip).
>
> > [!NOTE]
> > Tato osnovy by neměl určena pro použití pro vytvoření aplikace pro zabezpečené produkční. Tento kurz je určen k poskytování pouze přehledu o tom, jak implementovat serveru autorizace OAuth 2.0 pomocí middlewaru OWIN OAuth.
>
>
> ## <a name="software-versions"></a>Verze softwaru
>
> | **Uvedené v tomto kurzu** | **Funguje taky s** |
> | --- | --- |
> | Windows 8.1 | Windows 10, Windows 8, Windows 7 |
> | [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
> | .NET 4.7.2 |  |
>
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Pokud máte otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je uvést v [Katana projektu na Githubu](https://github.com/aspnet/AspNetKatana/). Pro dotazy a připomínky týkající se tohoto kurzu, samotný naleznete v části poznámky v dolní části stránky.


[OAuth 2.0 framework](http://tools.ietf.org/html/rfc6749) umožňuje aplikaci třetí strany k získání omezený přístup ke službě HTTP. Nemusíte používat přihlašovací údaje vlastníka prostředku pro přístup k chráněnému prostředku, klient získá přístupový token (což je řetězec představující konkrétní obor, dobu života a dalších atributů přístup). Přístupové tokeny jsou vystavené klientům třetích stran autorizačního serveru se souhlasem vlastníka prostředku.

V tomto kurzu budou zahrnovat:

- Jak vytvořit autorizační server pro podporu ověřování čtyři typy udělení a obnovovacích tokenů:
    - Udělení autorizačního kódu
    - Implicitní Grant
    - Udělení pověření heslo vlastníka prostředku
    - Udělení pověření klienta
- Vytváří se prostředek serveru, který je chráněn přístupový token.
- Vytvoření klientů OAuth 2.0.

<a id="prerequisites"></a>
## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) jak je uvedeno v **verze softwaru** v horní části stránky.
- Znalost OWIN. Zobrazit [Začínáme se službou projektu Katana](https://msdn.microsoft.com/magazine/dn451439.aspx) a [co je nového v OWIN a Katana](index.md).
- Znalost [OAuth](http://tools.ietf.org/html/rfc6749) terminologii, včetně [role](http://tools.ietf.org/html/rfc6749#section-1.1), [Protocol Flow](http://tools.ietf.org/html/rfc6749#section-1.2), a [udělení autorizace](http://tools.ietf.org/html/rfc6749#section-1.3). [OAuth 2.0 ÚVOD](http://tools.ietf.org/html/rfc6749#section-1) poskytuje vhodným úvodem.

## <a name="create-an-authorization-server"></a>Vytvoření autorizačního serveru

V tomto kurzu jsme se zhruba načrtnout použití [OWIN](https://msdn.microsoft.com/magazine/dn451439.aspx) a architektura ASP.NET MVC Vytvoření autorizačního serveru. Věříme, že brzy zajištění stahování pro úplnou vzorovou jako tento kurz neobsahuje každého kroku. Nejprve vytvořte prázdnou webovou aplikaci s názvem *AuthorizationServer* a nainstalujte následující balíčky:

- Microsoft.AspNet.Mvc
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security.OAuth
- Microsoft.Owin.Security.Cookies
- Microsoft.Owin.Security.Google (nebo jakékoli jiné přihlášení prostřednictvím sociální sítě balíček například Microsoft.Owin.Security.Facebook)

Přidat [třída OWIN Startup](owin-startup-class-detection.md) v kořenové složce projektu s názvem *spuštění*.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample1.cs?highlight=8)]

Vytvoření *aplikace\_Start* složky. Vyberte *aplikace\_Start* složky a použijte klávesu Shift + Alt + A přidání stažené verze *AuthorizationServer\App\_Start\Startup.Auth.cs* souboru.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample2.cs)]

Výše uvedený kód umožňuje přihlášení aplikace/externí soubory cookie a ověřování Google, které jsou používány samotný autorizační server pro správu účtů.

`UseOAuthAuthorizationServer` – Metoda rozšíření je pro nastavení serveru ověřování. Možnosti instalace jsou:

- `AuthorizeEndpointPath`: Cesta požadavku, kam klientské aplikace přesměrují uživatelského agenta aby bylo možné získat uživatele souhlas k vydání tokenu nebo kódu. Musí začínat úvodním lomítkem, například "`/Authorize`".
- `TokenEndpointPath`: Klientské aplikace cestu požadavku se získat přístupový token přímo komunikovat. Musí začínat úvodním lomítkem, jako je "/ Token". Pokud klient je vystaven [klienta\_tajný kód](http://tools.ietf.org/html/rfc6749#appendix-A.2), musí být poskytnut do tohoto koncového bodu.
- `ApplicationCanDisplayErrors`: Nastavte na `true` Pokud webová aplikace chce generovat vlastní chybové stránky pro chyby ověření klienta `/Authorize` koncového bodu. Je to potřeba jenom pro případy, kdy prohlížeč není přesměrován zpět do klientské aplikace, třeba když `client_id` nebo `redirect_uri` , nejsou správné. `/Authorize` Koncový bod by měl očekávat "oauth. Chyba","oauth. Popis chyby"a"oauth. Vlastnosti ErrorUri"jsou přidány do prostředí OWIN.

    > [!NOTE]
    > Pokud není true, bude autorizační server vrátit výchozí chybovou stránku s podrobnostmi o chybě.
- `AllowInsecureHttp`: True pro povolení požadavků ověřování a tokenů doručení na adresy HTTP URI a povolit příchozí `redirect_uri` povolit parametry požadavku a adresy HTTP URI.

    > [!WARNING]
    > Zabezpečení – Toto je pouze pro vývoj.
- `Provider`: Objekt Poskytnutý aplikací ke zpracování událostí vyvolaných middlewarem serveru ověřování. Aplikace může toto rozhraní implementovat plně nebo může vytvořit instanci `OAuthAuthorizationServerProvider` a přiřadit delegáty, které jsou nezbytné pro toky OAuth. Tento server podporuje.
- `AuthorizationCodeProvider`: Vytvoří jednorázový autorizační kód, který vrátí do klientské aplikace. OAuth server zabezpečit aplikace **musí** zadat instanci pro `AuthorizationCodeProvider` kde token vytvořený `OnCreate/OnCreateAsync` je událost považována za platný pouze pro jedno volání do `OnReceive/OnReceiveAsync`.
- `RefreshTokenProvider`: Vytvoří token obnovení, který může být použit k vytvoření nového tokenu přístupu v případě potřeby. Pokud není k dispozici server ověřování nevrátí tokeny obnovení z `/Token` koncového bodu.

## <a name="account-management"></a>Správa účtů

OAuth nemá péče, kdy nebo jak spravovat vaše informace o uživatelském účtu. Má [ASP.NET Identity](../../../identity/index.md) tedy za jejich vývoj odpovídá. V tomto kurzu budeme zjednodušit správu kód účtu a ujistěte se, že tento uživatel se může přihlásit pomocí souboru cookie middleware OWIN. Tady je zjednodušené ukázkový kód pro `AccountController`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample3.cs)]

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample4.cs?highlight=1)]

`ValidateClientRedirectUri` slouží k ověření klienta se její adresa URL pro přesměrování registrovaný. `ValidateClientAuthentication` zkontroluje základní schéma záhlaví a text formuláře k získání přihlašovacích údajů klienta.

Přihlašovací stránka je zobrazena níže:

![](owin-oauth-20-authorization-server/_static/image1.png)

Projděte si IETF OAuth 2 [udělení autorizačního kódu](http://tools.ietf.org/html/rfc6749#section-4.1) části nyní.

**Zprostředkovatel** (v následující tabulce) je [OAuthAuthorizationServerOptions](https://msdn.microsoft.com/library/microsoft.owin.security.oauth.oauthauthorizationserveroptions(v=vs.111).aspx). Zprostředkovatel, který je typu `OAuthAuthorizationServerProvider`, která obsahuje všechny události serveru OAuth.

| Kroky toku z část udělení autorizačního kódu | Stažení ukázkové provádí tyto kroky: |
| --- | --- |
|  |  |
| (A) klienta spouští tok přesměrováním vlastník prostředku uživatelského agenta pro koncový bod autorizace. Klient zahrne identifikátoru klienta, požadovaný obor, místní a identifikátor URI přesměrování, na které pošle autorizační server uživatelského agenta, zpět po přístup je udělen (nebo byl odepřen). | Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint |
|  |  |
| (B) autorizační server ověří vlastníka prostředku (prostřednictvím uživatelského agenta) a zjistí, zda vlastník prostředku udělí nebo zamítne žádost o přístup klienta. | **&lt;If user grants access&gt;** Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint AuthorizationCodeProvider.CreateAsync |
|  |  |
| (C) za předpokladu, že vlastník prostředku udělí přístup, přesměruje autorizační server uživatelského agenta zpět na klienta pomocí přesměrovacího identifikátoru URI zadaný starší (v žádosti nebo při registraci klienta). ... |  |
|  |  |
| (D) si klient vyžádá přístupový token z koncového bodu tokenu autorizačního serveru zahrnutím autorizační kód přijatý v předchozím kroku. Když se odešle požadavek, klient se ověří pomocí autorizačního serveru. Klient obsahuje přesměrovací identifikátor URI používá k získání autorizačního kódu pro ověřování. | Provider.MatchEndpoint Provider.ValidateClientAuthentication AuthorizationCodeProvider.ReceiveAsync Provider.ValidateTokenRequest Provider.GrantAuthorizationCode Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |

Ukázková implementace pro `AuthorizationCodeProvider.CreateAsync` a `ReceiveAsync` řídit vytvoření a ověření autorizační kód je uveden níže.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample5.cs)]

Výše uvedený kód používá k ukládání-the-ticket kódu a identity a obnovit identitu po obdržení kódu ConcurrentDictionary v paměti. V reálné aplikaci byste nahradit odpovídajícími do trvalého úložiště dat. Koncový bod autorizace je vlastník prostředku udělit přístup ke klientovi. Obvykle musí uživatelské rozhraní, aby uživatel mohl klikněte na tlačítko a ověřte oprávnění. Middleware OWIN OAuth umožňuje kódu aplikace ke zpracování koncový bod autorizace. V naší ukázkové aplikaci používáme kontroler MVC volá `OAuthController` aby to zvládnul. Tady je ukázková implementace:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample6.cs?highlight=15)]

`Authorize` Akce nejprve zkontroluje, pokud je uživatel přihlášen k autorizačnímu serveru. Pokud ne, že ověřovací middleware vyzve volajícího k ověření pomocí souboru cookie "Aplikace" a přesměruje na přihlašovací stránku. (Viz výše uvedený zvýrazněný kód.) Pokud má uživatel přihlášen, zobrazí se pak ověřit zobrazení, jak je znázorněno níže:

![](owin-oauth-20-authorization-server/_static/image2.png)

Pokud **udělení** výběru tlačítka `Authorize` akce vytvoří nové identity "Nosiče" a přihlaste se. Aktivuje autorizační server pro generování nosný token a jejich odesílání zpět do klienta s datovou část JSON.

### <a name="implicit-grant"></a>Implicitní Grant

Najdete IETF OAuth 2 [implicitní Grant](http://tools.ietf.org/html/rfc6749#section-4.2) části nyní.

 [Implicitní Grant](http://tools.ietf.org/html/rfc6749#section-4.2) tok je znázorněno na obrázku 4 je tok a které mapování OWIN OAuth následuje middlewaru.

| Kroky toku z část implicitní udělení | Stažení ukázkové provádí tyto kroky: |
| --- | --- |
|  |  |
| (A) klienta spouští tok přesměrováním vlastník prostředku uživatelského agenta pro koncový bod autorizace. Klient zahrne identifikátoru klienta, požadovaný obor, místní a identifikátor URI přesměrování, na které pošle autorizační server uživatelského agenta, zpět po přístup je udělen (nebo byl odepřen). | Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint |
|  |  |
| (B) autorizační server ověří vlastníka prostředku (prostřednictvím uživatelského agenta) a zjistí, zda vlastník prostředku udělí nebo zamítne žádost o přístup klienta. | **&lt;If user grants access&gt;** Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint AuthorizationCodeProvider.CreateAsync |
|  |  |
| (C) za předpokladu, že vlastník prostředku udělí přístup, přesměruje autorizační server uživatelského agenta zpět na klienta pomocí přesměrovacího identifikátoru URI zadaný starší (v žádosti nebo při registraci klienta). ... |  |
|  |  |
| (D) si klient vyžádá přístupový token z koncového bodu tokenu autorizačního serveru zahrnutím autorizační kód přijatý v předchozím kroku. Když se odešle požadavek, klient se ověří pomocí autorizačního serveru. Klient obsahuje přesměrovací identifikátor URI používá k získání autorizačního kódu pro ověřování. |  |

Protože jsme již implementováno koncový bod autorizace (`OAuthController.Authorize` akce) pro udělení autorizačního kódu umožňuje automaticky také implicitní tok. Poznámka: `Provider.ValidateClientRedirectUri` slouží k ověření ID klienta s její adresu URL přesměrování, které chrání implicitní grant tok v odesílání přístup token škodlivý klientů ([útoku Man-in-the-middle](https://www.owasp.org/index.php/Man-in-the-middle_attack)).

### <a name="resource-owner-password-credentials-grant"></a>Udělení pověření heslo vlastníka prostředku

Najdete IETF OAuth 2 [udělení přihlašovacích údajů heslo vlastníka prostředku](http://tools.ietf.org/html/rfc6749#section-4.3) části nyní.

 [Udělení přihlašovacích údajů heslo vlastníka prostředku](http://tools.ietf.org/html/rfc6749#section-4.3) tok z obrázku 5 je tok a řídí se middleware mapování, která OWIN OAuth.

| Kroky toku z část udělení přihlašovacích údajů heslo vlastníka prostředku | Stažení ukázkové provádí tyto kroky: |
| --- | --- |
|  |  |
| (A) vlastník prostředku poskytne klientovi své uživatelské jméno a heslo. |  |
|  |  |
| (B) si klient vyžádá přístupový token z koncového bodu tokenu autorizačního serveru zahrnutím přihlašovacích údajů přijatých od vlastníka prostředku. Když se odešle požadavek, klient se ověří pomocí autorizačního serveru. | Provider.MatchEndpoint Provider.ValidateClientAuthentication Provider.ValidateTokenRequest Provider.GrantResourceOwnerCredentials Provider.TokenEndpoint AccessToken Provider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (C) autorizační server ověří klienta a ověří přihlašovací údaje vlastníka prostředku a pokud je platný, vystaví přístupový token. |  |

Tady je ukázková implementace pro `Provider.GrantResourceOwnerCredentials`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample7.cs)]

> [!NOTE]
> Výše uvedený kód je určený k objasnění v této části kurzu a nesmí být použita v zabezpečení nebo produkční aplikace. Nekontroluje pověření vlastníky prostředků. Předpokládá, že se každý přihlašovacích údajů je platný a vytvoří novou identitu pro něj. Nové identity se použije k vygenerování přístupového tokenu a obnovovací token. Nahraďte kód vlastní kód pro správu zabezpečení účtu.


### <a name="client-credentials-grant"></a>Udělení pověření klienta

Najdete IETF OAuth 2 [udělení klientských přihlašovacích údajů](http://tools.ietf.org/html/rfc6749#section-4.4) části nyní.

 [Udělení klientských přihlašovacích údajů](http://tools.ietf.org/html/rfc6749#section-4.4) tok je znázorněno na obrázku 6 je tok a řídí se middleware mapování, která OWIN OAuth.

| Kroky toku z část udělování přihlašovacích údajů klienta | Stažení ukázkové provádí tyto kroky: |
| --- | --- |
|  |  |
| (A) klient ověřuje pomocí autorizačního serveru a vyžádá přístupový token z koncového bodu tokenu. | Provider.MatchEndpoint Provider.ValidateClientAuthentication Provider.ValidateTokenRequest Provider.GrantClientCredentials Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (B) autorizační server ověří klienta a pokud je platný, vystaví přístupový token. |  |

Tady je ukázková implementace pro `Provider.GrantClientCredentials`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample8.cs)]

> [!NOTE]
> Výše uvedený kód je určený k objasnění v této části kurzu a nesmí být použita v zabezpečení nebo produkční aplikace. Nahraďte kód vlastní kód pro správu klientů.


### <a name="refresh-token"></a>Aktualizovat Token

Najdete IETF OAuth 2 [aktualizovat Token](http://tools.ietf.org/html/rfc6749#section-1.5) části nyní.

 [Aktualizovat Token](http://tools.ietf.org/html/rfc6749#section-1.5) tok je znázorněno na obrázku 2 je tok a které mapování OWIN OAuth následuje middlewaru.

| Kroky toku z část udělování přihlašovacích údajů klienta | Stažení ukázkové provádí tyto kroky: |
| --- | --- |
|  |  |
| (G) si klient vyžádá nový přístupový token díky ověřování pomocí autorizačního serveru a nabízí ten samý token obnovení. Požadavky na ověřování klienta jsou založeny na typu klienta a server zásad autorizace. | Provider.MatchEndpoint Provider.ValidateClientAuthentication RefreshTokenProvider.ReceiveAsync Provider.ValidateTokenRequest Provider.GrantRefreshToken Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (H) autorizační server ověří klienta a ověří obnovovací token a pokud je platný, vystaví nový přístupový token (a volitelně nového tokenu obnovení). |  |

Tady je ukázková implementace pro `Provider.GrantRefreshToken`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample9.cs)]

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample10.cs)]

## <a name="create-a-resource-server-which-is-protected-by-access-token"></a>Vytvoření prostředků serveru, který je chráněn přístupový Token

Vytvoření projektu prázdnou webovou aplikaci a nainstalujte následující balíčky v projektu:

- Microsoft.AspNet.WebApi.Owin
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security.OAuth

Vytvořte třídu, spuštění a konfigurovat ověřování a webové rozhraní API. Zobrazit *AuthorizationServer\ResourceServer\Startup.cs* ve vzorku ke stažení.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample11.cs)]

Zobrazit *AuthorizationServer\ResourceServer\App\_Start\Startup.Auth.cs* ve vzorku ke stažení.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample12.cs)]

Zobrazit *AuthorizationServer\ResourceServer\App\_Start\Startup.WebApi.cs* ve vzorku ke stažení.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample13.cs)]

- `UseCors` metoda umožňuje CORS pro všechny domény.
- `UseOAuthBearerAuthentication` metoda umožňuje middlewaru ověřování pomocí tokenu nosiče OAuth, který bude přijímat a ověřovat nosný token z autorizační hlavičky v požadavku.
- `Config.SuppressDefaultHostAuthenticaiton` potlačí výchozí hostiteli ověřený objekt zabezpečení z aplikace, proto všechny požadavky budou anonymní po tomto volání.
- `HostAuthenticationFilter` Umožňuje ověřování jenom pro zadaný typ ověřování. V takovém případě je typ ověřování nosného tokenu.

Aby bylo možné předvést ověřenou identitu, vytvoříme objektu ApiController výstup deklarací identity aktuálního uživatele.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample14.cs)]

Pokud autorizační server a server prostředků nejsou ve stejném počítači, OAuth middleware použije jiný počítač klíče k šifrování a dešifrování nosný token přístupu. Aby bylo možné sdílet stejný soukromý klíč mezi oba projekty, můžeme přidat stejnou `machinekey` nastavení v obou *web.config* soubory.

[!code-xml[Main](owin-oauth-20-authorization-server/samples/sample15.xml?highlight=8-10)]

## <a name="create-oauth-20-clients"></a>Vytvoření klientů OAuth 2.0

 Používáme [DotNetOpenAuth.OAuth2.Client](http://www.nuget.org/packages/DotNetOpenAuth.OAuth2.Client) balíček NuGet pro zjednodušení kódu klienta.

### <a name="authorization-code-grant-client"></a>Autorizační kód udělení klienta

 Tento klient je aplikace MVC. Aktivuje toku přidělení kódu autorizace k získání tokenu přístupu z back-endu. Má jednu stránku, jak je znázorněno níže:

![](owin-oauth-20-authorization-server/_static/image3.png)

- **Authorize** tlačítko přesměruje autorizační server na upozornění pro vlastníka prostředku k udělení přístupu pro tohoto klienta prohlížeče.
- **Aktualizovat** tlačítko bude získat nový přístupový token a aktualizaci tokenu pomocí aktuální obnovovací token.
- **Rozhraní API prostředku s ochranou přístupu** tlačítko bude volat server prostředků k získání aktuálního uživatele deklarace dat a zobrazit na stránce.

Tady je ukázkový kód `HomeController` klienta.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample16.cs)]

`DotNetOpenAuth` ve výchozím nastavení vyžaduje protokol SSL. Vzhledem k tomu, že naše Ukázka používá HTTP, budete muset přidat následující nastavení v konfiguračním souboru:

[!code-xml[Main](owin-oauth-20-authorization-server/samples/sample17.xml?highlight=4-6)]

> [!WARNING]
> Zabezpečení – nikdy zakázat SSL v produkční aplikace. Přihlašovací údaje jsou nyní sítí odesílány ve formátu prostého textu. Výše uvedený kód je jenom pro místní ukázky ladění a prozkoumávání.


### <a name="implicit-grant-client"></a>Implicitní Grant klienta

Tento klient je pomocí kódu jazyka JavaScript:

1. Otevřete nové okno a přesměrovat na koncový bod authorize serveru ověřování.
2. Získání tokenu přístupu z adresy URL fragmenty při přesměrování zpět.

Tento proces je znázorněn na následujícím obrázku:

![](owin-oauth-20-authorization-server/_static/image4.png)

Klient by měl mít dvě stránky: jeden pro domovskou stránku a druhou pro zpětné volání. Toto je ukázka součástí kódu jazyka JavaScript *Index.cshtml* souboru:

[!code-cshtml[Main](owin-oauth-20-authorization-server/samples/sample18.cshtml)]

Tady je funkce zpětného volání v kódu pro zpracování *SignIn.cshtml* souboru:

[!code-cshtml[Main](owin-oauth-20-authorization-server/samples/sample19.cshtml)]

> [!NOTE]
> Osvědčeným postupem je přesunout na externí soubor jazyka JavaScript a Nevkládat se značkami Razor. Pro zjednodušení této ukázku je kombinovat.


### <a name="resource-owner-password-credentials-grant-client"></a>Heslo vlastníka prostředku přihlašovacích údajů klienta udělení

Ukázka tohoto klienta jsme pomocí konzolové aplikace. Zde je kód:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample20.cs)]

### <a name="client-credentials-grant-client"></a>Klient udělení přihlašovacích údajů klienta

Podobně jako u udělení přihlašovacích údajů heslo vlastníka prostředku, tady je kód aplikace konzoly:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample21.cs)]
