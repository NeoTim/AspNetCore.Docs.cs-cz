---
title: Url Přepisování MiddleWare v ASP.NET Core
author: rick-anderson
description: Přečtěte si o přepisování a přesměrování adres URL pomocí adres URL Přepisování middlewaru v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
uid: fundamentals/url-rewriting
ms.openlocfilehash: 7d63cf381f1d8a19ed4fb789348e36f94304ad63
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666465"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>Url Přepisování MiddleWare v ASP.NET Core

Podle [Mikael Mengistu](https://github.com/mikaelm12)

::: moniker range=">= aspnetcore-3.0"

Tento dokument představuje přepisování adres URL s pokyny, jak používat middleware pro přepisování adres URL v aplikacích ASP.NET Core.

Přepisování adres URL je úprava adres URL požadavků na základě jednoho nebo více předdefinovaných pravidel. Přepisování adres URL vytvoří abstrakce mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly úzce propojeny. Přepisování adres URL je cenné v několika scénářích:

* Přesuňte nebo nahraďte serverové prostředky dočasně nebo trvale a udržujte stabilní lokátory pro tyto prostředky.
* Rozdělení zpracování požadavků na různé aplikace nebo oblasti jedné aplikace.
* Odeberte, přidejte nebo reorganizujete segmenty adres URL u příchozích požadavků.
* Optimalizace veřejných adres URL pro optimalizaci pro vyhledávače (SEO).
* Povolte použití popisných veřejných adres URL, které návštěvníkům pomohou předpovědět obsah vrácený vyžádáním prostředku.
* Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.
* Zabránit hotlinking, kde externí web používá hostovaný statický datový zdroj na jiném webu propojením datového zdroje do vlastního obsahu.

> [!NOTE]
> Přepisování adres URL může snížit výkon aplikace. Je-li to proveditelné, omezte počet a složitost pravidel.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="url-redirect-and-url-rewrite"></a>Přesměrování adresy URL a přepsání adresy URL

Rozdíl ve formulaci mezi *přesměrováním adresy URL* a *přepsáním adresy URL* je jemný, ale má důležité důsledky pro poskytování prostředků klientům. ASP.NET Core URL Přepisování Middleware je schopen splnit potřebu obou.

*Přesměrování adresy URL* zahrnuje operaci na straně klienta, kde je klientovi pokyn k přístupu k prostředku na jiné adrese, než klient původně požadoval. To vyžaduje odezvu na server. Adresa URL přesměrování vrácená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.

Pokud `/resource` je *přesměrován* na `/different-resource`, server odpoví, že `/different-resource` klient by měl získat prostředek na se stavovým kódem označující, že přesměrování je dočasné nebo trvalé.

![Koncový bod služby WebAPI byl dočasně změněn z verze 1 (v1) na verzi 2 (v2) na serveru. Klient provede požadavek na službu na cestě verze 1 /v1/api. Server odešle zpět odpověď 302 (Nalezeno) s novou, dočasnou cestu pro službu ve verzi 2 /v2/api. Klient provede druhý požadavek na službu na adrese URL přesměrování. Server odpoví stavovým kódem 200 (OK).](url-rewriting/_static/url_redirect.png)

Při přesměrování požadavků na jinou adresu URL označte, zda je přesměrování trvalé nebo dočasné zadáním stavového kódu s odpovědí:

* Stavový kód *301 - Přesunout trvale* se používá v případě, že prostředek má novou trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek měly použít novou adresu URL. *Klient může ukládat do mezipaměti a znovu použít odpověď při přijetí stavového kódu 301.*

* Stavový kód *302 - Found* se používá, pokud je přesměrování dočasné nebo obecně může změnit. Stavový kód 302 označuje klientovi, aby adresu URL neukládal a v budoucnu ji používal.

Další informace o stavových kódech naleznete v [tématu RFC 2616: Definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než klient požadoval. Přepsání adresy URL nevyžaduje odezvu na server. Přepsaná adresa URL není vrácena klientovi a nezobrazuje se v adresním řádku prohlížeče.

Pokud `/resource` je *přepsánna* `/different-resource`, server *interně* načte `/different-resource`a vrátí prostředek na .

Přestože klient může být schopen načíst prostředek na přepsané adresy URL, klient není informován, že prostředek existuje na přepsané adrese URL, když to jeho požadavek a obdrží odpověď.

![Koncový bod služby WebAPI byl změněn z verze 1 (v1) na verzi 2 (v2) na serveru. Klient provede požadavek na službu na cestě verze 1 /v1/api. Adresa URL požadavku je přepsána pro přístup ke službě na cestě verze 2 /v2/api. Služba odpoví klientovi se stavovým kódem 200 (OK).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Ukázková aplikace pro přepisování adres URL

Můžete prozkoumat funkce URL Přepisování Middleware s [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/). Aplikace používá pravidla přesměrování a přepisu a zobrazuje přesměrovanou nebo přepsanou adresu URL pro několik scénářů.

## <a name="when-to-use-url-rewriting-middleware"></a>Kdy použít adresu URL Přepisování Middleware

Pomocí adres URL Přepisování Middleware, pokud nemůžete použít následující přístupy:

* [Modul přepisování adres URL se službou IIS v systému Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Apache mod_rewrite modul na Apache Serveru](https://httpd.apache.org/docs/2.4/rewrite/)
* [Přepisování adres URL na Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Middleware použijte také v případě, že je aplikace hostována na [serveru HTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaném WebListener).

Hlavní důvody pro použití technologií přepisování adres URL na serveru ve službách IIS, Apache a Nginx jsou:

* Middleware nepodporuje všechny funkce těchto modulů.

  Některé funkce serverových modulů nefungují s ASP.NET základní projekty, jako je například `IsFile` omezení `IsDirectory` modulu přepisování služby IIS. V těchto scénářích použijte middleware místo.
* Výkon middleware pravděpodobně neodpovídá tomu modulu.

  Benchmarking je jediný způsob, jak s jistotou vědět, který přístup snižuje výkon nejvíce, nebo pokud je snížený výkon zanedbatelný.

## <a name="package"></a>Balíček

Adresa URL Přepisování Middleware je poskytována [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) balíček, který je implicitně součástí ASP.NET aplikace Core.

## <a name="extension-and-options"></a>Rozšíření a možnosti

Vytvořte pravidla pro přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé z pravidel přepsání. Zřetězit více pravidel v pořadí, ve které je chcete zpracovat. Jsou `RewriteOptions` předány do adresy URL Přepisování Middleware, jak <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>je přidán do kanálu požadavku s :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Přesměrovat non-www na www

Tři možnosti umožňují aplikaci`www` přesměrovat `www`nežádosti na :

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; Trvale přesměrovat požadavek `www` na subdoménu,`www`pokud je požadavek non- . Přesměruje pomocí stavového kódu [Stav308PermanentRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect)

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Přesměrujte požadavek `www` na subdoménu, pokud`www`příchozí požadavek není- . Přesměruje pomocí stavového kódu [Status307TemporaryRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) Přetížení umožňuje zadat stavový kód pro odpověď. Pro přiřazení stavového kódu použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy.

### <a name="url-redirect"></a>Přesměrování adresy URL

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků. První parametr obsahuje regulární výraz pro porovnávání na cestě k příchozí adrese URL. Druhý parametr je náhradní řetězec. Třetí parametr, pokud je k dispozici, určuje stavový kód. Pokud nezadáte stavový kód, stavový kód je výchozí *302 - Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

V prohlížeči s povolenými vývojářskými nástroji podejte `/redirect-rule/1234/5678`požadavek na ukázkovou aplikaci s cestou . Regulární výraz odpovídá `redirect-rule/(.*)`cestě požadavku na a `/redirected/1234/5678`cesta je nahrazena . Adresa URL přesměrování je odeslána zpět klientovi se stavovým kódem *302 - Found.* Prohlížeč provede nový požadavek na adresu URL přesměrování, která se zobrazí v adresním řádku prohlížeče. Vzhledem k tomu, že žádná pravidla v ukázkové aplikaci se neshodují na adrese URL přesměrování:

* Druhý požadavek obdrží *odpověď 200 - OK* z aplikace.
* Text odpovědi zobrazuje adresu URL přesměrování.

Při *přesměrování*adresy URL na server se provádí okroucení .

> [!WARNING]
> Při vytváření pravidel přesměrování buďte opatrní. Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování. Je snadné náhodně vytvořit *smyčku nekonečných přesměrování*.

Původní požadavek:`/redirect-rule/1234/5678`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect.png)

Část výrazu obsažená v závorce se nazývá *skupina zachycení*. Tečka (`.`) výrazu znamená, že *odpovídá libovolnému znaku*. Hvězdička (`*`) *označuje, že odpovídá předchozímu znaku nula nebo vícekrát*. Proto poslední dva segmenty cesty adresy `1234/5678`URL , jsou `(.*)`zachyceny skupinou zachycení . Všechny hodnoty, které zadáte `redirect-rule/` v adrese URL požadavku po je zachycen a to to jediné zachycení skupiny.

V náhradním řetězci jsou zachycené skupiny vloženy`$`do řetězce se znakem dolaru ( ) následovaným pořadovým číslem zachycení. První hodnota skupiny zachycení `$1`je získána s , druhá s `$2`a pokračují v pořadí pro skupiny zachycení v regulárním výrazu. V regulárním období pravidla přesměrování v ukázkové aplikaci je pouze jedna zachycená skupina, takže v `$1`náhradním řetězci je pouze jedna vstřikovaná skupina, což je . Po použití pravidla se adresa `/redirected/1234/5678`URL změní na .

### <a name="url-redirect-to-a-secure-endpoint"></a>Přesměrování adresy URL na zabezpečený koncový bod

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS. Pokud není zadán stavový kód, middleware výchozí *302 - Nalezeno*. Pokud port není součástí dodávky:

* Middleware výchozí nastavení `null`.
* Schéma se `https` změní na (protokol HTTPS) a klient přistupuje k prostředku na portu 443.

Následující příklad ukazuje, jak nastavit stavový kód na *301 - Trvale přesunuta* a změnit port na 5001.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu pomocí zabezpečeného protokolu HTTPS na portu 443. Middleware nastaví stavový kód na *301 - Trvale přesunuto*.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware přesměrování HTTPS. Další informace naleznete v tématu [Vynucení https.](xref:security/enforcing-ssl#require-https)

Ukázková aplikace je schopna `AddRedirectToHttps` demonstrovat, jak používat nebo `AddRedirectToHttpsPermanent`. Přidejte metodu `RewriteOptions`rozšíření do . Vytvořte nezabezpečený požadavek na aplikaci na libovolné adrese URL. Zavřete upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěryhodnost certifikátu.

Původní požadavek pomocí `AddRedirectToHttps(301, 5001)`:`http://localhost:5000/secure`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect_to_https.png)

Původní požadavek pomocí `AddRedirectToHttpsPermanent`:`http://localhost:5000/secure`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Přepsání adresy URL

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepisování adres URL. První parametr obsahuje regulární výraz pro porovnávání na příchozí cestě URL. Druhý parametr je náhradní řetězec. Třetí parametr `skipRemainingRules: {true|false}`, označuje middleware, zda mají přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Původní požadavek:`/rewrite-rule/1234/5678`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavek a odpověď](url-rewriting/_static/add_rewrite.png)

Karát`^`( ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.

V předchozím příkladu s pravidlem `redirect-rule/(.*)`přesměrování , není`^`karát ( ) na začátku regulárního výrazu. Proto všechny znaky mohou `redirect-rule/` předcházet v cestě pro úspěšnou shodu.

| Cesta                               | Shoda |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Ano   |
| `/my-cool-redirect-rule/1234/5678` | Ano   |
| `/anotherredirect-rule/1234/5678`  | Ano   |

Pravidlo přepsání `^rewrite-rule/(\d+)/(\d+)`, pouze odpovídá cestám, pokud začínají na `rewrite-rule/`. V následující tabulce si všimněte rozdílu v porovnávání.

| Cesta                              | Shoda |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Ano   |
| `/my-cool-rewrite-rule/1234/5678` | Ne    |
| `/anotherrewrite-rule/1234/5678`  | Ne    |

Následující `^rewrite-rule/` část výrazu existují dvě skupiny `(\d+)/(\d+)`zachycení, . Označení `\d` odpovídají *číslici (číslo).* Znaménko plus (`+`) znamená, že odpovídá jednomu nebo více *předchozímu znaku*. Adresa URL proto musí obsahovat číslo následované lomítkem, za nímž následuje jiné číslo. Tyto skupiny zachycení jsou vloženy `$1` do `$2`přepsané adresy URL jako a . Řetězec nahrazení pravidla přepisu umístí zachycené skupiny do řetězce dotazu. Požadovaná cesta `/rewrite-rule/1234/5678` je přepsána k `/rewritten?var1=1234&var2=5678`získání prostředku na adrese . Pokud je řetězec dotazu k dispozici na původní požadavek, je zachována při přepsání adresy URL.

Neexistuje žádná zpáteční cesta na server získat zdroj. Pokud prostředek existuje, je načten a vrácen klientovi se stavovým kódem *200 - OK.* Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění. Klienti nemohou zjistit, že na serveru došlo k operaci přepsání adresy URL.

> [!NOTE]
> Použijte `skipRemainingRules: true` vždy, když je to možné, protože odpovídající pravidla jsou výpočtově drahá a prodlužují dobu odezvy aplikace. Pro nejrychlejší odezvu aplikace:
>
> * Pořadí přepisovat pravidla z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.
> * Přeskočit zpracování zbývajících pravidel, když dojde ke shodě a žádné další zpracování pravidel je vyžadováno.

### <a name="apache-mod_rewrite"></a>Apache mod_rewrite

Použijte pravidla Apache <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>mod_rewrite s . Ujistěte se, že soubor pravidel je nasazen s aplikací. Další informace a příklady mod_rewrite pravidel naleznete v [tématu Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).

A <xref:System.IO.StreamReader> se používá ke čtení pravidel ze souboru pravidel *ApacheModRewrite.txt:*

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

Ukázková aplikace přesměruje `/apache-mod-rules-redirect/(.\*)` požadavky `/redirected?id=$1`z do . Stavový kód odpovědi je *302 - Nalezeno*.

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

Původní požadavek:`/apache-mod-rules-redirect/1234`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_apache_mod_redirect.png)

Middleware podporuje následující apache mod_rewrite serverové proměnné:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* Protokol IPV6
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

### <a name="iis-url-rewrite-module-rules"></a>Pravidla modulu přepisování adres URL služby IIS

Chcete-li použít stejnou sadu pravidel, která se vztahuje <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>na modul přepisování adres URL služby IIS, použijte . Ujistěte se, že soubor pravidel je nasazen s aplikací. Nenasměrujte middleware na použití souboru *web.config* aplikace při spuštění ve službě Windows Server IIS. Ve službě IIS by tato pravidla měla být uložena mimo soubor *web.config* aplikace, aby nedošlo ke konfliktům s modulem Přepisování služby IIS. Další informace a příklady pravidel modulu přepisování adres URL služby IIS naleznete [v tématu Použití modulu pro přepis adres URL 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkazu na konfiguraci modulu pro přepis adres URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).

A <xref:System.IO.StreamReader> se používá ke čtení pravidel ze souboru pravidel *IISUrlRewrite.xml:*

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

Ukázková aplikace přepíše `/iis-rules-rewrite/(.*)` požadavky `/rewritten?id=$1`z do . Odpověď je odeslána klientovi se stavovým kódem *200 - OK.*

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

Původní požadavek:`/iis-rules-rewrite/1234`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavek a odpověď](url-rewriting/_static/add_iis_url_rewrite.png)

Pokud máte aktivní modul pro přepisování služby IIS s nakonfigurovanými pravidly na úrovni serveru, která by měla nepříznivý vliv na vaši aplikaci, můžete modul pro přepisování služby IIS pro aplikaci zakázat. Další informace naleznete [v tématu Zakázání modulů iis](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Nepodporované funkce

Middleware vydaný s ASP.NET Core 2.x nepodporuje následující funkce modulu přepisování adres URL služby IIS:

* Pravidla pro odchozí provoz
* Proměnné vlastního serveru
* Zástupné znaky
* Adresa LogRewrittenUrl

#### <a name="supported-server-variables"></a>Podporované proměnné serveru

Middleware podporuje následující proměnné serveru serveru IIS URL Rewrite Module:

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
> Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>. Tento přístup může poskytnout větší flexibilitu pro umístění souborů pravidel přepisování. Ujistěte se, že soubory pravidel přepisu jsou nasazeny na server na cestě, kterou zadáte.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Pravidlo založené na metodách

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci vlastní logiky pravidla v metodě. `Add`zpřístupní <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, který <xref:Microsoft.AspNetCore.Http.HttpContext> zpřístupňuje pro použití v metodě. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak je zpracováno další zpracování kanálu. Nastavte hodnotu na <xref:Microsoft.AspNetCore.Rewrite.RuleResult> jedno z polí popsaných v následující tabulce.

| `RewriteContext.Result`              | Akce                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules`(výchozí) | Pokračujte v uplatňování pravidel.                                         |
| `RuleResult.EndResponse`             | Přestaňte používat pravidla a odešlete odpověď.                       |
| `RuleResult.SkipRemainingRules`      | Přestaňte používat pravidla a odešlete kontext do dalšího middlewaru. |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

Ukázková aplikace demonstruje metodu, která přesměruje požadavky na cesty, které končí *.xml*. Pokud je žádost `/file.xml`podána , je `/xmlfiles/file.xml`žádost přesměrována na . Stavový kód je nastaven na *301 - Trvale přesunuto*. Když prohlížeč vytvoří nový požadavek na */xmlfiles/file.xml*, middleware statického souboru zobrazí soubor klientovi ze složky *wwwroot/xmlfiles.* Pro přesměrování explicitně nastavte stavový kód odpovědi. V opačném případě je vrácen stavový kód *200 - OK* a přesměrování se nevyskytuje na straně klienta.

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Tento přístup můžete také přepsat požadavky. Ukázková aplikace ukazuje přepisování cesty pro libovolný požadavek textového souboru, který má být připojen k textovému souboru *file.txt* ze složky *wwwroot.* Middleware statického souboru slouží souboru na základě aktualizované cesty požadavku:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>Pravidlo založené na pravidlech IRule

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve <xref:Microsoft.AspNetCore.Rewrite.IRule> třídě, která implementuje rozhraní. `IRule`poskytuje větší flexibilitu při používání přístupu pravidla založeného na metodách. Vaše třída implementace může obsahovat konstruktor, který umožňuje <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> předat parametry pro metodu.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Hodnoty parametrů v ukázkové aplikaci `extension` pro `newPath` a a jsou kontrolovány tak, aby splňovaly několik podmínek. Musí `extension` obsahovat hodnotu a hodnota musí být *.png*, *.jpg*nebo *.gif*. Pokud `newPath` není platný, <xref:System.ArgumentException> je hozen. Pokud je požadavek na *soubor image.png*podán `/png-images/image.png`, je požadavek přesměrován na . Pokud je požadavek na *image.jpg*, požadavek je přesměrován na `/jpg-images/image.jpg`. Stavový kód je nastaven na *301 - Trvale přesunuta*a `context.Result` je nastavena na zastavení zpracování pravidel a odeslání odpovědi.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Původní požadavek:`/image.png`

![Okno prohlížeče s nástroji pro vývojáře, které sledují požadavky a odpovědi pro soubor image.png](url-rewriting/_static/add_redirect_png_requests.png)

Původní požadavek:`/image.jpg`

![Okno prohlížeče s nástroji pro vývojáře, které sledují požadavky a odpovědi na obrázek.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Příklady regexu

| Cíl | Řetězec regex &<br>Příklad shody | Náhradní řetězec &<br>Příklad výstupu |
| ---- | ------------------------------- | -------------------------------------- |
| Přepsat cestu do řetězce dotazu | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Pruh vlečené lomítko | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Vynutit koncové lomítko | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Vyhněte se přepisování konkrétních požadavků | `^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`<br>Ano:`/resource.htm`<br>Ne:`/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Změna uspořádání segmentů adres URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Nahrazení segmentu adresy URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento dokument představuje přepisování adres URL s pokyny, jak používat middleware pro přepisování adres URL v aplikacích ASP.NET Core.

Přepisování adres URL je úprava adres URL požadavků na základě jednoho nebo více předdefinovaných pravidel. Přepisování adres URL vytvoří abstrakce mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly úzce propojeny. Přepisování adres URL je cenné v několika scénářích:

* Přesuňte nebo nahraďte serverové prostředky dočasně nebo trvale a udržujte stabilní lokátory pro tyto prostředky.
* Rozdělení zpracování požadavků na různé aplikace nebo oblasti jedné aplikace.
* Odeberte, přidejte nebo reorganizujete segmenty adres URL u příchozích požadavků.
* Optimalizace veřejných adres URL pro optimalizaci pro vyhledávače (SEO).
* Povolte použití popisných veřejných adres URL, které návštěvníkům pomohou předpovědět obsah vrácený vyžádáním prostředku.
* Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.
* Zabránit hotlinking, kde externí web používá hostovaný statický datový zdroj na jiném webu propojením datového zdroje do vlastního obsahu.

> [!NOTE]
> Přepisování adres URL může snížit výkon aplikace. Je-li to proveditelné, omezte počet a složitost pravidel.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="url-redirect-and-url-rewrite"></a>Přesměrování adresy URL a přepsání adresy URL

Rozdíl ve formulaci mezi *přesměrováním adresy URL* a *přepsáním adresy URL* je jemný, ale má důležité důsledky pro poskytování prostředků klientům. ASP.NET Core URL Přepisování Middleware je schopen splnit potřebu obou.

*Přesměrování adresy URL* zahrnuje operaci na straně klienta, kde je klientovi pokyn k přístupu k prostředku na jiné adrese, než klient původně požadoval. To vyžaduje odezvu na server. Adresa URL přesměrování vrácená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.

Pokud `/resource` je *přesměrován* na `/different-resource`, server odpoví, že `/different-resource` klient by měl získat prostředek na se stavovým kódem označující, že přesměrování je dočasné nebo trvalé.

![Koncový bod služby WebAPI byl dočasně změněn z verze 1 (v1) na verzi 2 (v2) na serveru. Klient provede požadavek na službu na cestě verze 1 /v1/api. Server odešle zpět odpověď 302 (Nalezeno) s novou, dočasnou cestu pro službu ve verzi 2 /v2/api. Klient provede druhý požadavek na službu na adrese URL přesměrování. Server odpoví stavovým kódem 200 (OK).](url-rewriting/_static/url_redirect.png)

Při přesměrování požadavků na jinou adresu URL označte, zda je přesměrování trvalé nebo dočasné zadáním stavového kódu s odpovědí:

* Stavový kód *301 - Přesunout trvale* se používá v případě, že prostředek má novou trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek měly použít novou adresu URL. *Klient může ukládat do mezipaměti a znovu použít odpověď při přijetí stavového kódu 301.*

* Stavový kód *302 - Found* se používá, pokud je přesměrování dočasné nebo obecně může změnit. Stavový kód 302 označuje klientovi, aby adresu URL neukládal a v budoucnu ji používal.

Další informace o stavových kódech naleznete v [tématu RFC 2616: Definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než klient požadoval. Přepsání adresy URL nevyžaduje odezvu na server. Přepsaná adresa URL není vrácena klientovi a nezobrazuje se v adresním řádku prohlížeče.

Pokud `/resource` je *přepsánna* `/different-resource`, server *interně* načte `/different-resource`a vrátí prostředek na .

Přestože klient může být schopen načíst prostředek na přepsané adresy URL, klient není informován, že prostředek existuje na přepsané adrese URL, když to jeho požadavek a obdrží odpověď.

![Koncový bod služby WebAPI byl změněn z verze 1 (v1) na verzi 2 (v2) na serveru. Klient provede požadavek na službu na cestě verze 1 /v1/api. Adresa URL požadavku je přepsána pro přístup ke službě na cestě verze 2 /v2/api. Služba odpoví klientovi se stavovým kódem 200 (OK).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Ukázková aplikace pro přepisování adres URL

Můžete prozkoumat funkce URL Přepisování Middleware s [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/). Aplikace používá pravidla přesměrování a přepisu a zobrazuje přesměrovanou nebo přepsanou adresu URL pro několik scénářů.

## <a name="when-to-use-url-rewriting-middleware"></a>Kdy použít adresu URL Přepisování Middleware

Pomocí adres URL Přepisování Middleware, pokud nemůžete použít následující přístupy:

* [Modul přepisování adres URL se službou IIS v systému Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Apache mod_rewrite modul na Apache Serveru](https://httpd.apache.org/docs/2.4/rewrite/)
* [Přepisování adres URL na Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Middleware použijte také v případě, že je aplikace hostována na [serveru HTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaném WebListener).

Hlavní důvody pro použití technologií přepisování adres URL na serveru ve službách IIS, Apache a Nginx jsou:

* Middleware nepodporuje všechny funkce těchto modulů.

  Některé funkce serverových modulů nefungují s ASP.NET základní projekty, jako je například `IsFile` omezení `IsDirectory` modulu přepisování služby IIS. V těchto scénářích použijte middleware místo.
* Výkon middleware pravděpodobně neodpovídá tomu modulu.

  Benchmarking je jediný způsob, jak s jistotou vědět, který přístup snižuje výkon nejvíce, nebo pokud je snížený výkon zanedbatelný.

## <a name="package"></a>Balíček

Chcete-li do projektu zahrnout middleware, přidejte odkaz na balíček [balíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) do souboru projektu, který obsahuje balíček [Microsoft.AspNetCore.Rewrite.](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite)

Pokud nepoužíváte `Microsoft.AspNetCore.App` metabalíček, přidejte `Microsoft.AspNetCore.Rewrite` odkaz na projekt balíčku.

## <a name="extension-and-options"></a>Rozšíření a možnosti

Vytvořte pravidla pro přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé z pravidel přepsání. Zřetězit více pravidel v pořadí, ve které je chcete zpracovat. Jsou `RewriteOptions` předány do adresy URL Přepisování Middleware, jak <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>je přidán do kanálu požadavku s :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Přesměrovat non-www na www

Tři možnosti umožňují aplikaci`www` přesměrovat `www`nežádosti na :

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; Trvale přesměrovat požadavek `www` na subdoménu,`www`pokud je požadavek non- . Přesměruje pomocí stavového kódu [Stav308PermanentRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect)

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Přesměrujte požadavek `www` na subdoménu, pokud`www`příchozí požadavek není- . Přesměruje pomocí stavového kódu [Status307TemporaryRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) Přetížení umožňuje zadat stavový kód pro odpověď. Pro přiřazení stavového kódu použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy.

### <a name="url-redirect"></a>Přesměrování adresy URL

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků. První parametr obsahuje regulární výraz pro porovnávání na cestě k příchozí adrese URL. Druhý parametr je náhradní řetězec. Třetí parametr, pokud je k dispozici, určuje stavový kód. Pokud nezadáte stavový kód, stavový kód je výchozí *302 - Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

V prohlížeči s povolenými vývojářskými nástroji podejte `/redirect-rule/1234/5678`požadavek na ukázkovou aplikaci s cestou . Regulární výraz odpovídá `redirect-rule/(.*)`cestě požadavku na a `/redirected/1234/5678`cesta je nahrazena . Adresa URL přesměrování je odeslána zpět klientovi se stavovým kódem *302 - Found.* Prohlížeč provede nový požadavek na adresu URL přesměrování, která se zobrazí v adresním řádku prohlížeče. Vzhledem k tomu, že žádná pravidla v ukázkové aplikaci se neshodují na adrese URL přesměrování:

* Druhý požadavek obdrží *odpověď 200 - OK* z aplikace.
* Text odpovědi zobrazuje adresu URL přesměrování.

Při *přesměrování*adresy URL na server se provádí okroucení .

> [!WARNING]
> Při vytváření pravidel přesměrování buďte opatrní. Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování. Je snadné náhodně vytvořit *smyčku nekonečných přesměrování*.

Původní požadavek:`/redirect-rule/1234/5678`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect.png)

Část výrazu obsažená v závorce se nazývá *skupina zachycení*. Tečka (`.`) výrazu znamená, že *odpovídá libovolnému znaku*. Hvězdička (`*`) *označuje, že odpovídá předchozímu znaku nula nebo vícekrát*. Proto poslední dva segmenty cesty adresy `1234/5678`URL , jsou `(.*)`zachyceny skupinou zachycení . Všechny hodnoty, které zadáte `redirect-rule/` v adrese URL požadavku po je zachycen a to to jediné zachycení skupiny.

V náhradním řetězci jsou zachycené skupiny vloženy`$`do řetězce se znakem dolaru ( ) následovaným pořadovým číslem zachycení. První hodnota skupiny zachycení `$1`je získána s , druhá s `$2`a pokračují v pořadí pro skupiny zachycení v regulárním výrazu. V regulárním období pravidla přesměrování v ukázkové aplikaci je pouze jedna zachycená skupina, takže v `$1`náhradním řetězci je pouze jedna vstřikovaná skupina, což je . Po použití pravidla se adresa `/redirected/1234/5678`URL změní na .

### <a name="url-redirect-to-a-secure-endpoint"></a>Přesměrování adresy URL na zabezpečený koncový bod

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS. Pokud není zadán stavový kód, middleware výchozí *302 - Nalezeno*. Pokud port není součástí dodávky:

* Middleware výchozí nastavení `null`.
* Schéma se `https` změní na (protokol HTTPS) a klient přistupuje k prostředku na portu 443.

Následující příklad ukazuje, jak nastavit stavový kód na *301 - Trvale přesunuta* a změnit port na 5001.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu pomocí zabezpečeného protokolu HTTPS na portu 443. Middleware nastaví stavový kód na *301 - Trvale přesunuto*.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware přesměrování HTTPS. Další informace naleznete v tématu [Vynucení https.](xref:security/enforcing-ssl#require-https)

Ukázková aplikace je schopna `AddRedirectToHttps` demonstrovat, jak používat nebo `AddRedirectToHttpsPermanent`. Přidejte metodu `RewriteOptions`rozšíření do . Vytvořte nezabezpečený požadavek na aplikaci na libovolné adrese URL. Zavřete upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěryhodnost certifikátu.

Původní požadavek pomocí `AddRedirectToHttps(301, 5001)`:`http://localhost:5000/secure`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect_to_https.png)

Původní požadavek pomocí `AddRedirectToHttpsPermanent`:`http://localhost:5000/secure`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Přepsání adresy URL

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepisování adres URL. První parametr obsahuje regulární výraz pro porovnávání na příchozí cestě URL. Druhý parametr je náhradní řetězec. Třetí parametr `skipRemainingRules: {true|false}`, označuje middleware, zda mají přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Původní požadavek:`/rewrite-rule/1234/5678`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavek a odpověď](url-rewriting/_static/add_rewrite.png)

Karát`^`( ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.

V předchozím příkladu s pravidlem `redirect-rule/(.*)`přesměrování , není`^`karát ( ) na začátku regulárního výrazu. Proto všechny znaky mohou `redirect-rule/` předcházet v cestě pro úspěšnou shodu.

| Cesta                               | Shoda |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Ano   |
| `/my-cool-redirect-rule/1234/5678` | Ano   |
| `/anotherredirect-rule/1234/5678`  | Ano   |

Pravidlo přepsání `^rewrite-rule/(\d+)/(\d+)`, pouze odpovídá cestám, pokud začínají na `rewrite-rule/`. V následující tabulce si všimněte rozdílu v porovnávání.

| Cesta                              | Shoda |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Ano   |
| `/my-cool-rewrite-rule/1234/5678` | Ne    |
| `/anotherrewrite-rule/1234/5678`  | Ne    |

Následující `^rewrite-rule/` část výrazu existují dvě skupiny `(\d+)/(\d+)`zachycení, . Označení `\d` odpovídají *číslici (číslo).* Znaménko plus (`+`) znamená, že odpovídá jednomu nebo více *předchozímu znaku*. Adresa URL proto musí obsahovat číslo následované lomítkem, za nímž následuje jiné číslo. Tyto skupiny zachycení jsou vloženy `$1` do `$2`přepsané adresy URL jako a . Řetězec nahrazení pravidla přepisu umístí zachycené skupiny do řetězce dotazu. Požadovaná cesta `/rewrite-rule/1234/5678` je přepsána k `/rewritten?var1=1234&var2=5678`získání prostředku na adrese . Pokud je řetězec dotazu k dispozici na původní požadavek, je zachována při přepsání adresy URL.

Neexistuje žádná zpáteční cesta na server získat zdroj. Pokud prostředek existuje, je načten a vrácen klientovi se stavovým kódem *200 - OK.* Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění. Klienti nemohou zjistit, že na serveru došlo k operaci přepsání adresy URL.

> [!NOTE]
> Použijte `skipRemainingRules: true` vždy, když je to možné, protože odpovídající pravidla jsou výpočtově drahá a prodlužují dobu odezvy aplikace. Pro nejrychlejší odezvu aplikace:
>
> * Pořadí přepisovat pravidla z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.
> * Přeskočit zpracování zbývajících pravidel, když dojde ke shodě a žádné další zpracování pravidel je vyžadováno.

### <a name="apache-mod_rewrite"></a>Apache mod_rewrite

Použijte pravidla Apache <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>mod_rewrite s . Ujistěte se, že soubor pravidel je nasazen s aplikací. Další informace a příklady mod_rewrite pravidel naleznete v [tématu Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).

A <xref:System.IO.StreamReader> se používá ke čtení pravidel ze souboru pravidel *ApacheModRewrite.txt:*

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

Ukázková aplikace přesměruje `/apache-mod-rules-redirect/(.\*)` požadavky `/redirected?id=$1`z do . Stavový kód odpovědi je *302 - Nalezeno*.

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

Původní požadavek:`/apache-mod-rules-redirect/1234`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_apache_mod_redirect.png)

Middleware podporuje následující apache mod_rewrite serverové proměnné:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* Protokol IPV6
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

### <a name="iis-url-rewrite-module-rules"></a>Pravidla modulu přepisování adres URL služby IIS

Chcete-li použít stejnou sadu pravidel, která se vztahuje <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>na modul přepisování adres URL služby IIS, použijte . Ujistěte se, že soubor pravidel je nasazen s aplikací. Nenasměrujte middleware na použití souboru *web.config* aplikace při spuštění ve službě Windows Server IIS. Ve službě IIS by tato pravidla měla být uložena mimo soubor *web.config* aplikace, aby nedošlo ke konfliktům s modulem Přepisování služby IIS. Další informace a příklady pravidel modulu přepisování adres URL služby IIS naleznete [v tématu Použití modulu pro přepis adres URL 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkazu na konfiguraci modulu pro přepis adres URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).

A <xref:System.IO.StreamReader> se používá ke čtení pravidel ze souboru pravidel *IISUrlRewrite.xml:*

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

Ukázková aplikace přepíše `/iis-rules-rewrite/(.*)` požadavky `/rewritten?id=$1`z do . Odpověď je odeslána klientovi se stavovým kódem *200 - OK.*

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

Původní požadavek:`/iis-rules-rewrite/1234`

![Okno prohlížeče s nástroji pro vývojáře sledující požadavek a odpověď](url-rewriting/_static/add_iis_url_rewrite.png)

Pokud máte aktivní modul pro přepisování služby IIS s nakonfigurovanými pravidly na úrovni serveru, která by měla nepříznivý vliv na vaši aplikaci, můžete modul pro přepisování služby IIS pro aplikaci zakázat. Další informace naleznete [v tématu Zakázání modulů iis](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Nepodporované funkce

Middleware vydaný s ASP.NET Core 2.x nepodporuje následující funkce modulu přepisování adres URL služby IIS:

* Pravidla pro odchozí provoz
* Proměnné vlastního serveru
* Zástupné znaky
* Adresa LogRewrittenUrl

#### <a name="supported-server-variables"></a>Podporované proměnné serveru

Middleware podporuje následující proměnné serveru serveru IIS URL Rewrite Module:

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
> Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>. Tento přístup může poskytnout větší flexibilitu pro umístění souborů pravidel přepisování. Ujistěte se, že soubory pravidel přepisu jsou nasazeny na server na cestě, kterou zadáte.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Pravidlo založené na metodách

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci vlastní logiky pravidla v metodě. `Add`zpřístupní <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, který <xref:Microsoft.AspNetCore.Http.HttpContext> zpřístupňuje pro použití v metodě. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak je zpracováno další zpracování kanálu. Nastavte hodnotu na <xref:Microsoft.AspNetCore.Rewrite.RuleResult> jedno z polí popsaných v následující tabulce.

| `RewriteContext.Result`              | Akce                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules`(výchozí) | Pokračujte v uplatňování pravidel.                                         |
| `RuleResult.EndResponse`             | Přestaňte používat pravidla a odešlete odpověď.                       |
| `RuleResult.SkipRemainingRules`      | Přestaňte používat pravidla a odešlete kontext do dalšího middlewaru. |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

Ukázková aplikace demonstruje metodu, která přesměruje požadavky na cesty, které končí *.xml*. Pokud je žádost `/file.xml`podána , je `/xmlfiles/file.xml`žádost přesměrována na . Stavový kód je nastaven na *301 - Trvale přesunuto*. Když prohlížeč vytvoří nový požadavek na */xmlfiles/file.xml*, middleware statického souboru zobrazí soubor klientovi ze složky *wwwroot/xmlfiles.* Pro přesměrování explicitně nastavte stavový kód odpovědi. V opačném případě je vrácen stavový kód *200 - OK* a přesměrování se nevyskytuje na straně klienta.

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Tento přístup můžete také přepsat požadavky. Ukázková aplikace ukazuje přepisování cesty pro libovolný požadavek textového souboru, který má být připojen k textovému souboru *file.txt* ze složky *wwwroot.* Middleware statického souboru slouží souboru na základě aktualizované cesty požadavku:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs*:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>Pravidlo založené na pravidlech IRule

Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve <xref:Microsoft.AspNetCore.Rewrite.IRule> třídě, která implementuje rozhraní. `IRule`poskytuje větší flexibilitu při používání přístupu pravidla založeného na metodách. Vaše třída implementace může obsahovat konstruktor, který umožňuje <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> předat parametry pro metodu.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Hodnoty parametrů v ukázkové aplikaci `extension` pro `newPath` a a jsou kontrolovány tak, aby splňovaly několik podmínek. Musí `extension` obsahovat hodnotu a hodnota musí být *.png*, *.jpg*nebo *.gif*. Pokud `newPath` není platný, <xref:System.ArgumentException> je hozen. Pokud je požadavek na *soubor image.png*podán `/png-images/image.png`, je požadavek přesměrován na . Pokud je požadavek na *image.jpg*, požadavek je přesměrován na `/jpg-images/image.jpg`. Stavový kód je nastaven na *301 - Trvale přesunuta*a `context.Result` je nastavena na zastavení zpracování pravidel a odeslání odpovědi.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Původní požadavek:`/image.png`

![Okno prohlížeče s nástroji pro vývojáře, které sledují požadavky a odpovědi pro soubor image.png](url-rewriting/_static/add_redirect_png_requests.png)

Původní požadavek:`/image.jpg`

![Okno prohlížeče s nástroji pro vývojáře, které sledují požadavky a odpovědi na obrázek.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Příklady regexu

| Cíl | Řetězec regex &<br>Příklad shody | Náhradní řetězec &<br>Příklad výstupu |
| ---- | ------------------------------- | -------------------------------------- |
| Přepsat cestu do řetězce dotazu | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Pruh vlečené lomítko | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Vynutit koncové lomítko | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Vyhněte se přepisování konkrétních požadavků | `^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`<br>Ano:`/resource.htm`<br>Ne:`/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Změna uspořádání segmentů adres URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Nahrazení segmentu adresy URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Regulární výrazy v rozhraní .NET](/dotnet/articles/standard/base-types/regular-expressions)
* [Jazyk regulárních výrazů - stručný přehled](/dotnet/articles/standard/base-types/quick-ref)
* [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)
* [Použití modulu pro přepis adres URL 2.0 (pro službu IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [Odkaz na konfiguraci modulu pro přepis adresy URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [Fórum přepisovacího modulu adresy URL služby IIS](https://forums.iis.net/1152.aspx)
* [Zachování jednoduché struktury adres URL](https://support.google.com/webmasters/answer/76329?hl=en)
* [10 URL Přepisování Tipy a triky](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [Chcete-li lomítko nebo ne lomítko](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
