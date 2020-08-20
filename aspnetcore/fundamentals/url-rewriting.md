---
title: Middleware pro přepis adres URL v ASP.NET Core
author: rick-anderson
description: Přečtěte si informace o přepsání adresy URL a přesměrování pomocí middlewaru pro přepis adres URL v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
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
uid: fundamentals/url-rewriting
ms.openlocfilehash: a1d31428945adade6748185c17d42ef60a61b5dc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631703"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>Middleware pro přepis adres URL v ASP.NET Core

Od [Mikael Mengistu](https://github.com/mikaelm12)

::: moniker range=">= aspnetcore-3.0"

Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.

Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel. Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené. Přepsání adresy URL je v několika scénářích užitečné pro:

* Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.
* Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.
* Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.
* Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).
* Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.
* Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.
* Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.

> [!NOTE]
> Přepsání adresy URL může snížit výkon aplikace. Pokud je to možné, omezte počet a složitost pravidel.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>Přesměrování adresy URL a přepsání adresy URL

Rozdíl mezi přesměrováním *URL* a *přepsáním adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům. Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.

*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně. To vyžaduje zpáteční cestu k serveru. Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.

Pokud `/resource` je *přesměrován* na `/different-resource` , server odpoví, že by měl klient získat prostředek na `/different-resource` stavovém kódu, což značí, že přesměrování je buď dočasné, nebo trvalé.

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru. Klient odešle požadavek na službu ve verzi 1 cesty/v1/API. Server pošle odpověď 302 (nalezeno) nové dočasné cestě ke službě ve verzi 2/v2/API. Klient vytvoří druhý požadavek na službu na adrese URL pro přesměrování. Server odpoví stavovým kódem 200 (OK).](url-rewriting/_static/url_redirect.png)

Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:

* V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* . *Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*

* V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* . Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.

Další informace o stavových kódech najdete v [dokumentu RFC 2616: definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient. Přepsání adresy URL nevyžaduje zpáteční cestu k serveru. Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.

Pokud `/resource` je *přepsáno* na `/different-resource` , server *interně* načte a vrátí prostředek na `/different-resource` .

I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru. Klient odešle požadavek na službu ve verzi 1 cesty/v1/API. Adresa URL požadavku se přepíše pro přístup ke službě ve verzi 2 cesty/v2/API. Služba odpoví klientovi stavovým kódem 200 (OK).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Ukázková aplikace pro přepsání adresy URL

Pomocí [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL. Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.

## <a name="when-to-use-url-rewriting-middleware"></a>Kdy použít middleware přepisu adresy URL

Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:

* [Modul pro přepsání adresy URL ve službě IIS v systému Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Apache mod_rewrite modul na serveru Apache](https://httpd.apache.org/docs/2.4/rewrite/)
* [Přepsání adresy URL na Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Používejte také middleware při hostování aplikace na [ serveruHTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).

Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:

* Middleware nepodporuje všechny funkce těchto modulů.

  Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` `IsDirectory` omezení a modulu IIS Rewrite. V těchto scénářích místo toho použijte middleware.
* Výkon middleware se pravděpodobně neshoduje s modulem.

  Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.

## <a name="package"></a>Balíček

Middleware pro přepis adres URL poskytuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) , který je implicitně zahrnutý v aplikacích ASP.NET Core.

## <a name="extension-and-options"></a>Rozšíření a možnosti

Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání. Řetězení více pravidel v pořadí, ve kterém se mají zpracovat. Rozhraní se `RewriteOptions` předává do middleware přepisování adresy URL při jeho přidání do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Přesměrovat neexistující na webovou

Tři možnosti umožňují, aby aplikace přesměrovala `www` požadavky bez požadavků na `www` :

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Trvale přesměrujte požadavek na `www` subdoménu, pokud je požadavek jiný než `www` . Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Přesměrujte požadavek na `www` subdoménu, pokud je příchozí požadavek jiný než `www` . Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) . Přetížení umožňuje poskytnout stavový kód pro odpověď. Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy pro přiřazení stavového kódu.

### <a name="url-redirect"></a>Přesměrování adresy URL

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků. První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL. Druhým parametrem je náhradní řetězec. Třetí parametr, pokud je k dispozici, určuje stavový kód. Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

V prohlížeči s povolenými vývojářskými nástroji vytvořte žádost o ukázkovou aplikaci s cestou `/redirect-rule/1234/5678` . Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)` a cesta je nahrazena řetězcem `/redirected/1234/5678` . Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* . Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče. Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:

* Druhá žádost obdrží odpověď *200-OK* od aplikace.
* Tělo odpovědi zobrazuje adresu URL pro přesměrování.

Při *přesměrování*adresy URL se na Server provede cyklická výměna.

> [!WARNING]
> Buďte opatrní při vytváření pravidel přesměrování. Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování. Je snadné vytvořit *smyčku nekonečných přesměrování*.

Původní požadavek: `/redirect-rule/1234/5678`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*. Tečka ( `.` ) výrazu znamená, že *odpovídá libovolnému znaku*. Hvězdička ( `*` ) značí, *že se předchozí znak rovná nule nebo vícekrát*. Proto jsou poslední dva segmenty cesty adresy URL `1234/5678` zachyceny skupinou zachycení `(.*)` . Jakákoli hodnota, kterou zadáte v adrese URL požadavku, potom, co `redirect-rule/` je tato jediná skupina zachycení zachycena.

V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru ( `$` ) následovaným pořadovým číslem záznamu. První hodnota skupiny zachycení je získána s `$1` , druhým s `$2` a pokračuje v pořadí pro skupiny zachycení ve vašem regulárním výrazu. Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1` . Po použití pravidla se adresa URL bude nacházet `/redirected/1234/5678` .

### <a name="url-redirect-to-a-secure-endpoint"></a>Přesměrování adresy URL na zabezpečený koncový bod

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS. Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*. Pokud port není dodán:

* Ve výchozím nastavení se používá middleware `null` .
* Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.

Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu s protokolem Secure https na portu 443. Middleware nastaví stavový kód na *301 – trvale přesunuto*.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS. Další informace najdete v tématu věnovaném [vykonání protokolu HTTPS](xref:security/enforcing-ssl#require-https) .

Ukázková aplikace dokáže demonstrovat, jak používat `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent` . Přidejte metodu rozšíření do `RewriteOptions` . Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL. Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.

Původní požadavek s použitím `AddRedirectToHttps(301, 5001)` : `http://localhost:5000/secure`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

Původní požadavek s použitím `AddRedirectToHttpsPermanent` : `http://localhost:5000/secure`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Přepsání adresy URL

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL. První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL. Druhým parametrem je náhradní řetězec. Třetí parametr, `skipRemainingRules: {true|false}` označuje middleware bez ohledu na to, zda má přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Původní požadavek: `/rewrite-rule/1234/5678`

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

Kosočtverce ( `^` ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.

V předchozím příkladu s pravidlem přesměrování `redirect-rule/(.*)` není `^` na začátku regulárního výrazu k dispozici žádná kosočtverce (). Proto jakékoli znaky mohou předcházet `redirect-rule/` v cestě k úspěšné shodě.

| Cesta                               | Shoda |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Yes   |
| `/my-cool-redirect-rule/1234/5678` | Yes   |
| `/anotherredirect-rule/1234/5678`  | Yes   |

Pravidlo přepsaného zápisu `^rewrite-rule/(\d+)/(\d+)` odpovídá pouze cestám, pokud začínají na `rewrite-rule/` . V následující tabulce si všimněte rozdílu v porovnání.

| Cesta                              | Shoda |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Ano   |
| `/my-cool-rewrite-rule/1234/5678` | No    |
| `/anotherrewrite-rule/1234/5678`  | No    |

Po `^rewrite-rule/` části výrazu jsou k dispozici dvě skupiny zachycení, `(\d+)/(\d+)` . `\d`Značí *shodu s číslicí (číslem)*. Znaménko plus ( `+` ) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*. Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo. Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2` . Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu. Požadovaná cesta k `/rewrite-rule/1234/5678` je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678` . Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.

K získání prostředku neexistuje žádná Přenosová cesta k serveru. Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* . Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění. Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.

> [!NOTE]
> `skipRemainingRules: true`Kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace. Pro nejrychlejší aplikační odpověď:
>
> * Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.
> * Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.

### <a name="apache-mod_rewrite"></a>Mod_rewrite Apache

Použijte pravidla mod_rewrite Apache s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> . Ujistěte se, že je soubor pravidel nasazený spolu s aplikací. Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).

<xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *ApacheModRewrite.txt* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1` . Stavový kód odpovědi je *302 – Nalezeno*.

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

Původní požadavek: `/apache-mod-rules-redirect/1234`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

Middleware podporuje následující proměnné serveru Apache mod_rewrite:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* PROTOKOLŮ
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* ČAS
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>Pravidla pro přepsání adresy URL služby IIS

Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> . Ujistěte se, že je soubor pravidel nasazený spolu s aplikací. Nesměrujte middleware na použití *web.config* souboru aplikace při spuštění na Windows serveru IIS. V případě služby IIS by tato pravidla měla být uložena mimo soubor *web.config* aplikace, aby nedocházelo ke konfliktům s modulem PŘEPISU služby IIS. Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.

<xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *IISUrlRewrite.xml* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do `/rewritten?id=$1` . Odpověď se pošle klientovi s kódem stavu *200-OK* .

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

Původní požadavek: `/iis-rules-rewrite/1234`

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite. Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Nepodporované funkce

Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:

* Pravidla pro odchozí provoz
* Vlastní proměnné serveru
* Zástupné znaky
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Podporované proměnné serveru

Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> . Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu. Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Pravidlo založené na metodách

Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky vlastního pravidla v metodě. `Add` zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> , který zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> metodu pro použití v metodě. [RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu. Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.

| Výsledek kontextu přepsání               | Akce                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules` výchozí | Pokračovat v používání pravidel.                                         |
| `RuleResult.EndResponse`             | Zastavení použití pravidel a odeslání odpovědi.                       |
| `RuleResult.SkipRemainingRules`      | Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru. |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*. Pokud je žádost určena pro `/file.xml` , je požadavek přesměrován na `/xmlfiles/file.xml` . Stavový kód je nastaven na *301 – trvale přesunuto*. Když prohlížeč vytvoří nový požadavek na */xmlfiles/file.xml*, soubor middleware statických souborů obsluhuje soubor do klienta ze složky *wwwroot/xmlfiles* . Pro přesměrování explicitně nastavte stavový kód odpovědi. V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Tento přístup může také přezapisovat požadavky. Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužila *file.txt* textový soubor ze složky *wwwroot* . Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>Pravidlo založené na IRule

Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> rozhraní. `IRule` poskytuje větší flexibilitu při použití pravidla založeného na metodách. Vaše třída implementace může obsahovat konstruktor, který umožňuje předat parametry pro <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metodu.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou zkontrolovány, aby splňovaly několik podmínek. `extension`Musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif*. Pokud `newPath` není platná, <xref:System.ArgumentException> je vyvolána výjimka. Pokud se pro *image.png*požadavek provede, požadavek se přesměruje na `/png-images/image.png` . Pokud se pro *image.jpg*požadavek provede, požadavek se přesměruje na `/jpg-images/image.jpg` . Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Původní požadavek: `/image.png`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.png](url-rewriting/_static/add_redirect_png_requests.png)

Původní požadavek: `/image.jpg`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Příklady Regex

| Cíl | & řetězce regulárního výrazu<br>Příklad shody | & řetězce nahrazení<br>Příklad výstupu |
| ---- | ------------------------------- | -------------------------------------- |
| Přepište cestu do řetězce dotazu. | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Koncové lomítko pruhu | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Vymáhat koncové lomítko | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Vyhnout se přepisu konkrétních požadavků | `^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`<br>Ano: `/resource.htm`<br>Ne: `/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Změna uspořádání segmentů adresy URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Nahradit segment adresy URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.

Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel. Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené. Přepsání adresy URL je v několika scénářích užitečné pro:

* Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.
* Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.
* Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.
* Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).
* Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.
* Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.
* Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.

> [!NOTE]
> Přepsání adresy URL může snížit výkon aplikace. Pokud je to možné, omezte počet a složitost pravidel.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>Přesměrování adresy URL a přepsání adresy URL

Rozdíl mezi přesměrováním *URL* a *přepsáním adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům. Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.

*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně. To vyžaduje zpáteční cestu k serveru. Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.

Pokud `/resource` je *přesměrován* na `/different-resource` , server odpoví, že by měl klient získat prostředek na `/different-resource` stavovém kódu, což značí, že přesměrování je buď dočasné, nebo trvalé.

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru. Klient odešle požadavek na službu ve verzi 1 cesty/v1/API. Server pošle odpověď 302 (nalezeno) nové dočasné cestě ke službě ve verzi 2/v2/API. Klient vytvoří druhý požadavek na službu na adrese URL pro přesměrování. Server odpoví stavovým kódem 200 (OK).](url-rewriting/_static/url_redirect.png)

Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:

* V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* . *Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*

* V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* . Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.

Další informace o stavových kódech najdete v [dokumentu RFC 2616: definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient. Přepsání adresy URL nevyžaduje zpáteční cestu k serveru. Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.

Pokud `/resource` je *přepsáno* na `/different-resource` , server *interně* načte a vrátí prostředek na `/different-resource` .

I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru. Klient odešle požadavek na službu ve verzi 1 cesty/v1/API. Adresa URL požadavku se přepíše pro přístup ke službě ve verzi 2 cesty/v2/API. Služba odpoví klientovi stavovým kódem 200 (OK).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Ukázková aplikace pro přepsání adresy URL

Pomocí [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL. Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.

## <a name="when-to-use-url-rewriting-middleware"></a>Kdy použít middleware přepisu adresy URL

Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:

* [Modul pro přepsání adresy URL ve službě IIS v systému Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Apache mod_rewrite modul na serveru Apache](https://httpd.apache.org/docs/2.4/rewrite/)
* [Přepsání adresy URL na Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Používejte také middleware při hostování aplikace na [ serveruHTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).

Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:

* Middleware nepodporuje všechny funkce těchto modulů.

  Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` `IsDirectory` omezení a modulu IIS Rewrite. V těchto scénářích místo toho použijte middleware.
* Výkon middleware se pravděpodobně neshoduje s modulem.

  Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.

## <a name="package"></a>Balíček

Chcete-li do projektu zahrnout middleware, přidejte odkaz na balíček do souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v souboru projektu, který obsahuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) .

Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte do balíčku odkaz na projekt `Microsoft.AspNetCore.Rewrite` .

## <a name="extension-and-options"></a>Rozšíření a možnosti

Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání. Řetězení více pravidel v pořadí, ve kterém se mají zpracovat. Rozhraní se `RewriteOptions` předává do middleware přepisování adresy URL při jeho přidání do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Přesměrovat neexistující na webovou

Tři možnosti umožňují, aby aplikace přesměrovala `www` požadavky bez požadavků na `www` :

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Trvale přesměrujte požadavek na `www` subdoménu, pokud je požadavek jiný než `www` . Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Přesměrujte požadavek na `www` subdoménu, pokud je příchozí požadavek jiný než `www` . Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) . Přetížení umožňuje poskytnout stavový kód pro odpověď. Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy pro přiřazení stavového kódu.

### <a name="url-redirect"></a>Přesměrování adresy URL

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků. První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL. Druhým parametrem je náhradní řetězec. Třetí parametr, pokud je k dispozici, určuje stavový kód. Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

V prohlížeči s povolenými vývojářskými nástroji vytvořte žádost o ukázkovou aplikaci s cestou `/redirect-rule/1234/5678` . Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)` a cesta je nahrazena řetězcem `/redirected/1234/5678` . Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* . Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče. Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:

* Druhá žádost obdrží odpověď *200-OK* od aplikace.
* Tělo odpovědi zobrazuje adresu URL pro přesměrování.

Při *přesměrování*adresy URL se na Server provede cyklická výměna.

> [!WARNING]
> Buďte opatrní při vytváření pravidel přesměrování. Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování. Je snadné vytvořit *smyčku nekonečných přesměrování*.

Původní požadavek: `/redirect-rule/1234/5678`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*. Tečka ( `.` ) výrazu znamená, že *odpovídá libovolnému znaku*. Hvězdička ( `*` ) značí, *že se předchozí znak rovná nule nebo vícekrát*. Proto jsou poslední dva segmenty cesty adresy URL `1234/5678` zachyceny skupinou zachycení `(.*)` . Jakákoli hodnota, kterou zadáte v adrese URL požadavku, potom, co `redirect-rule/` je tato jediná skupina zachycení zachycena.

V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru ( `$` ) následovaným pořadovým číslem záznamu. První hodnota skupiny zachycení je získána s `$1` , druhým s `$2` a pokračuje v pořadí pro skupiny zachycení ve vašem regulárním výrazu. Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1` . Po použití pravidla se adresa URL bude nacházet `/redirected/1234/5678` .

### <a name="url-redirect-to-a-secure-endpoint"></a>Přesměrování adresy URL na zabezpečený koncový bod

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS. Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*. Pokud port není dodán:

* Ve výchozím nastavení se používá middleware `null` .
* Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.

Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu s protokolem Secure https na portu 443. Middleware nastaví stavový kód na *301 – trvale přesunuto*.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS. Další informace najdete v tématu věnovaném [vykonání protokolu HTTPS](xref:security/enforcing-ssl#require-https) .

Ukázková aplikace dokáže demonstrovat, jak používat `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent` . Přidejte metodu rozšíření do `RewriteOptions` . Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL. Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.

Původní požadavek s použitím `AddRedirectToHttps(301, 5001)` : `http://localhost:5000/secure`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

Původní požadavek s použitím `AddRedirectToHttpsPermanent` : `http://localhost:5000/secure`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Přepsání adresy URL

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL. První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL. Druhým parametrem je náhradní řetězec. Třetí parametr, `skipRemainingRules: {true|false}` označuje middleware bez ohledu na to, zda má přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Původní požadavek: `/rewrite-rule/1234/5678`

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

Kosočtverce ( `^` ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.

V předchozím příkladu s pravidlem přesměrování `redirect-rule/(.*)` není `^` na začátku regulárního výrazu k dispozici žádná kosočtverce (). Proto jakékoli znaky mohou předcházet `redirect-rule/` v cestě k úspěšné shodě.

| Cesta                               | Shoda |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Yes   |
| `/my-cool-redirect-rule/1234/5678` | Yes   |
| `/anotherredirect-rule/1234/5678`  | Yes   |

Pravidlo přepsaného zápisu `^rewrite-rule/(\d+)/(\d+)` odpovídá pouze cestám, pokud začínají na `rewrite-rule/` . V následující tabulce si všimněte rozdílu v porovnání.

| Cesta                              | Shoda |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Ano   |
| `/my-cool-rewrite-rule/1234/5678` | No    |
| `/anotherrewrite-rule/1234/5678`  | No    |

Po `^rewrite-rule/` části výrazu jsou k dispozici dvě skupiny zachycení, `(\d+)/(\d+)` . `\d`Značí *shodu s číslicí (číslem)*. Znaménko plus ( `+` ) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*. Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo. Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2` . Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu. Požadovaná cesta k `/rewrite-rule/1234/5678` je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678` . Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.

K získání prostředku neexistuje žádná Přenosová cesta k serveru. Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* . Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění. Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.

> [!NOTE]
> `skipRemainingRules: true`Kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace. Pro nejrychlejší aplikační odpověď:
>
> * Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.
> * Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.

### <a name="apache-mod_rewrite"></a>Mod_rewrite Apache

Použijte pravidla mod_rewrite Apache s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> . Ujistěte se, že je soubor pravidel nasazený spolu s aplikací. Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).

<xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *ApacheModRewrite.txt* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1` . Stavový kód odpovědi je *302 – Nalezeno*.

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

Původní požadavek: `/apache-mod-rules-redirect/1234`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

Middleware podporuje následující proměnné serveru Apache mod_rewrite:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* PROTOKOLŮ
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* ČAS
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>Pravidla pro přepsání adresy URL služby IIS

Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> . Ujistěte se, že je soubor pravidel nasazený spolu s aplikací. Nesměrujte middleware na použití *web.config* souboru aplikace při spuštění na Windows serveru IIS. V případě služby IIS by tato pravidla měla být uložena mimo soubor *web.config* aplikace, aby nedocházelo ke konfliktům s modulem PŘEPISU služby IIS. Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.

<xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *IISUrlRewrite.xml* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do `/rewritten?id=$1` . Odpověď se pošle klientovi s kódem stavu *200-OK* .

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

Původní požadavek: `/iis-rules-rewrite/1234`

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite. Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Nepodporované funkce

Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:

* Pravidla pro odchozí provoz
* Vlastní proměnné serveru
* Zástupné znaky
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Podporované proměnné serveru

Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> . Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu. Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Pravidlo založené na metodách

Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky vlastního pravidla v metodě. `Add` zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> , který zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> metodu pro použití v metodě. [RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu. Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.

| Výsledek kontextu přepsání               | Akce                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules` výchozí | Pokračovat v používání pravidel.                                         |
| `RuleResult.EndResponse`             | Zastavení použití pravidel a odeslání odpovědi.                       |
| `RuleResult.SkipRemainingRules`      | Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru. |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*. Pokud je žádost určena pro `/file.xml` , je požadavek přesměrován na `/xmlfiles/file.xml` . Stavový kód je nastaven na *301 – trvale přesunuto*. Když prohlížeč vytvoří nový požadavek na */xmlfiles/file.xml*, soubor middleware statických souborů obsluhuje soubor do klienta ze složky *wwwroot/xmlfiles* . Pro přesměrování explicitně nastavte stavový kód odpovědi. V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Tento přístup může také přezapisovat požadavky. Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužila *file.txt* textový soubor ze složky *wwwroot* . Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>Pravidlo založené na IRule

Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> rozhraní. `IRule` poskytuje větší flexibilitu při použití pravidla založeného na metodách. Vaše třída implementace může obsahovat konstruktor, který umožňuje předat parametry pro <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metodu.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou zkontrolovány, aby splňovaly několik podmínek. `extension`Musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif*. Pokud `newPath` není platná, <xref:System.ArgumentException> je vyvolána výjimka. Pokud se pro *image.png*požadavek provede, požadavek se přesměruje na `/png-images/image.png` . Pokud se pro *image.jpg*požadavek provede, požadavek se přesměruje na `/jpg-images/image.jpg` . Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Původní požadavek: `/image.png`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.png](url-rewriting/_static/add_redirect_png_requests.png)

Původní požadavek: `/image.jpg`

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Příklady Regex

| Cíl | & řetězce regulárního výrazu<br>Příklad shody | & řetězce nahrazení<br>Příklad výstupu |
| ---- | ------------------------------- | -------------------------------------- |
| Přepište cestu do řetězce dotazu. | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Koncové lomítko pruhu | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Vymáhat koncové lomítko | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Vyhnout se přepisu konkrétních požadavků | `^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`<br>Ano: `/resource.htm`<br>Ne: `/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Změna uspořádání segmentů adresy URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Nahradit segment adresy URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a>Další zdroje informací

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Regulární výrazy v rozhraní .NET](/dotnet/articles/standard/base-types/regular-expressions)
* [Jazyk regulárních výrazů – stručná referenční dokumentace](/dotnet/articles/standard/base-types/quick-ref)
* [Mod_rewrite Apache](https://httpd.apache.org/docs/2.4/rewrite/)
* [Použití modulu URL pro přepis 2,0 (pro IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [Odkaz na konfiguraci modulu pro přepis adresy URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [Fórum modulu pro přepsání adresy URL služby IIS](https://forums.iis.net/1152.aspx)
* [Zachování jednoduché struktury URL](https://support.google.com/webmasters/answer/76329?hl=en)
* [10 tipů a triky pro přepis adres URL](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [Na lomítko nebo ne na lomítko](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
