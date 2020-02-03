---
title: Komprese odezvy v ASP.NET Core
author: guardrex
description: Přečtěte si o kompresi odpovědí a způsobu použití middleware pro kompresi odpovědí v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
uid: performance/response-compression
ms.openlocfilehash: b8a84418a3258e9ac43b4eadd8564c0708590bce
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726960"
---
# <a name="response-compression-in-aspnet-core"></a>Komprese odezvy v ASP.NET Core

Od [Luke Latham](https://github.com/guardrex)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Šířka pásma sítě je omezeného prostředku. Odezvu aplikace obvykle zlepšuje zmenšení velikosti odpovědi, a to často výrazně. Jedním ze způsobů zmenšení velikosti datové části je komprese odpovědí vaší aplikace.

## <a name="when-to-use-response-compression-middleware"></a>Kdy použít middleware pro kompresi odpovědí

Používejte technologie pro kompresi odpovědí na základě serveru ve službě IIS, Apache nebo Nginx. Výkon middleware se pravděpodobně neshoduje s modulem serverových modulů. Server [http. sys](xref:fundamentals/servers/httpsys) Server a server [Kestrel](xref:fundamentals/servers/kestrel) momentálně nenabízí integrovanou podporu komprese.

Používejte middleware pro kompresi odpovědí, pokud jste:

* Nelze použít následující technologie komprese založené na serveru:
  * [Modul dynamické komprese služby IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Modul Apache mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx kompresi a dekomprese](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Hostování přímo na:
  * [Http. sys Server](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten)
  * [Server Kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Komprese odpovědí

Obvykle by jakákoli odpověď, která není nativně komprimovaná, mohla využívat kompresi odpovědi. Odpovědi, které nejsou nativně zkomprimované, obvykle zahrnují: CSS, JavaScript, HTML, XML a JSON. Neměli byste komprimovat nativně komprimované prostředky, jako jsou soubory PNG. Pokusíte-li se dále komprimovat nativně komprimovanou odpověď, jakékoli malé další snížení velikosti a čas přenosu budou pravděpodobně převrženy časem, který trvalo zpracování komprese. Nekomprimuje soubory menší než přibližně 150-1000 bajtů (v závislosti na obsahu souboru a efektivitě komprese). Režie komprimace malých souborů může vytvořit komprimovaný soubor, který je větší než nekomprimovaný soubor.

Když klient může zpracovat komprimovaný obsah, klient musí informovat Server svých schopností odesláním hlavičky `Accept-Encoding` s požadavkem. Když server pošle komprimovaný obsah, musí obsahovat informace v hlavičce `Content-Encoding`, jak je komprimovaná odpověď zakódovaná. V následující tabulce jsou uvedena označení kódování obsahu podporovaná middlewarem.

::: moniker range=">= aspnetcore-2.2"

| `Accept-Encoding` hodnoty hlaviček | Middleware – podporováno | Popis |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Ano (výchozí)        | [Formát komprimovaných dat Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Ne                   | [Formát ZÚŽENé komprese dat](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Ne                   | [Efektivní výměna XML pro W3C](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Ano                  | [Formát souboru gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Ano                  | Identifikátor "bez kódování": odpověď nesmí být kódovaná. |
| `pack200-gzip`                  | Ne                   | [Formát přenosu v síti pro archivy Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Ano                  | Jakékoli dostupné kódování obsahu není explicitně požadováno. |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| `Accept-Encoding` hodnoty hlaviček | Middleware – podporováno | Popis |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Ne                   | [Formát komprimovaných dat Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Ne                   | [Formát ZÚŽENé komprese dat](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Ne                   | [Efektivní výměna XML pro W3C](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Ano (výchozí)        | [Formát souboru gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Ano                  | Identifikátor "bez kódování": odpověď nesmí být kódovaná. |
| `pack200-gzip`                  | Ne                   | [Formát přenosu v síti pro archivy Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Ano                  | Jakékoli dostupné kódování obsahu není explicitně požadováno. |

::: moniker-end

Další informace najdete v [seznamu kódů oficiálního obsahu IANA](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

Middleware umožňuje přidat další zprostředkovatele komprese pro vlastní hodnoty hlaviček `Accept-Encoding`. Další informace najdete v tématu [Vlastní zprostředkovatelé](#custom-providers) níže.

Middleware je schopná chovat hodnoty kvality (qvalue, `q`), když je klient odesílá k určení priorit schémat komprese. Další informace najdete v [dokumentu RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Algoritmy komprese se vztahují na kompromisy mezi rychlostí komprese a efektivitou komprese. *Efektivita* v tomto kontextu odkazuje na velikost výstupu po kompresi. Nejmenší velikost dosáhne *optimální* komprese.

Hlavičky zahrnuté v tématu vyžádání, odeslání, ukládání do mezipaměti a příjem komprimovaného obsahu jsou popsány v následující tabulce.

| Hlavička             | Role |
| ------------------ | ---- |
| `Accept-Encoding`  | Odesílá se z klienta na server k označení schémat kódování obsahu, které jsou přijatelné pro klienta. |
| `Content-Encoding` | Odesílá se ze serveru do klienta, aby označovala kódování obsahu v datové části. |
| `Content-Length`   | Když dojde k komprimaci, `Content-Length` záhlaví je odebráno, protože obsah těla se po kompresi odpovědi změní. |
| `Content-MD5`      | Když dojde k komprimaci, `Content-MD5` záhlaví je odebráno, protože došlo ke změně obsahu textu a hodnota hash již není platná. |
| `Content-Type`     | Určuje typ MIME obsahu. Každá odpověď by měla specifikovat svůj `Content-Type`. Middleware kontroluje tuto hodnotu, aby určila, jestli by měla být komprimovaná odpověď. Middleware určuje sadu [výchozích typů MIME](#mime-types) , které může kódovat, ale můžete nahradit nebo přidat typy MIME. |
| `Vary`             | Když server odešle server s hodnotou `Accept-Encoding` klientům a proxy, označuje `Vary` záhlaví klienta nebo proxy serveru, že by měl ukládat (měnit) odpovědi na základě hodnoty v hlavičce `Accept-Encoding` požadavku. Výsledkem vrácení obsahu s hlavičkou `Vary: Accept-Encoding` je, že komprimované i nekomprimované odpovědi jsou ukládány do mezipaměti samostatně. |

Prozkoumejte funkce middleware pro kompresi odpovědí s [ukázkovou aplikací](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples). Ukázka znázorňuje:

* Komprese odpovědí aplikace pomocí gzip a vlastních zprostředkovatelů komprese.
* Postup přidání typu MIME do výchozího seznamu typů MIME pro kompresi.

## <a name="package"></a>Balíček

::: moniker range=">= aspnetcore-3.0"

Middleware pro kompresi odpovědí poskytuje balíček [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) , který je implicitně zahrnutý v aplikacích ASP.NET Core.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Chcete-li do projektu zahrnout middleware, přidejte odkaz na soubor [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), který obsahuje balíček [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) .

::: moniker-end

## <a name="configuration"></a>Konfigurace

::: moniker range=">= aspnetcore-2.2"

Následující kód ukazuje, jak povolit middleware pro kompresi odpovědí pro výchozí typy MIME a zprostředkovatele komprese ([Brotli](#brotli-compression-provider) a [gzip](#gzip-compression-provider)):

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Následující kód ukazuje, jak povolit middleware pro kompresi odpovědí pro výchozí typy MIME a [zprostředkovatele komprese GZip](#gzip-compression-provider):

::: moniker-end

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

Poznámky:

* `app.UseResponseCompression` musí být volány před jakýmkoli middlewarem, který komprimuje odpovědi. Další informace naleznete v tématu <xref:fundamentals/middleware/index#middleware-order>.
* Pomocí nástroje, jako je například [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)nebo [poster](https://www.getpostman.com/) , nastavte hlavičku `Accept-Encoding` žádosti a prostudujte hlavičky, velikost a text odpovědi.

Odešlete žádost do ukázkové aplikace bez hlavičky `Accept-Encoding` a sledujte, že odpověď je nekomprimovaná. V odpovědi se nevyskytují hlavičky `Content-Encoding` a `Vary`.

![Okno Fiddler znázorňující výsledek požadavku bez hlavičky Accept-Encoding. Odpověď není komprimovaná.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

Odešlete žádost do ukázkové aplikace s hlavičkou `Accept-Encoding: br` (Brotli Compression) a sledujte, že je odpověď komprimovaná. V odpovědi jsou k dispozici hlavičky `Content-Encoding` a `Vary`.

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou br. K odpovědi se přidají záhlaví Vary a kódování obsahu. Odpověď je komprimovaná.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Odešlete žádost do ukázkové aplikace s hlavičkou `Accept-Encoding: gzip` a sledujte, že je odpověď komprimovaná. V odpovědi jsou k dispozici hlavičky `Content-Encoding` a `Vary`.

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou gzip. K odpovědi se přidají záhlaví Vary a kódování obsahu. Odpověď je komprimovaná.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>Poskytovatelé

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Brotli kompresní poskytovatel

Pro komprimaci odpovědí s [formátem komprimovaných dat Brotli](https://tools.ietf.org/html/rfc7932)použijte <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>.

Pokud nejsou do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>explicitně přidány žádní zprostředkovatelé komprese:

* Zprostředkovatel komprese Brotli se ve výchozím nastavení přidá k poli zprostředkovatelů komprese společně se [zprostředkovatelem komprese GZip](#gzip-compression-provider).
* Komprese se nastaví jako výchozí Brotli komprese, když klient podporuje formát komprimovaných dat Brotli. Pokud klient nepodporuje Brotli, komprese je standardně nastavená na gzip, když klient podporuje kompresi gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Zprostředkovatel komprese Brotoli je nutné přidat, když jsou explicitně přidány poskytovatelé komprese:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Nastavte úroveň komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>. Zprostředkovatel komprese Brotli je výchozím nastavením nejrychlejší kompresní úrovně ([CompressionLevel. nejrychlejší](xref:System.IO.Compression.CompressionLevel)), což nemusí způsobovat nejúčinnější kompresi. Pokud je žádoucí co nejúčinnější komprese, nakonfigurujte middleware pro optimální kompresi.

| Úroveň komprese | Popis |
| ----------------- | ----------- |
| [CompressionLevel. nejrychlejší](xref:System.IO.Compression.CompressionLevel) | Komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný výstup není optimálně komprimován. |
| [CompressionLevel. uncompression](xref:System.IO.Compression.CompressionLevel) | Není nutné provádět kompresi. |
| [CompressionLevel. optimální](xref:System.IO.Compression.CompressionLevel) | Odpovědi by se měly optimálně komprimovat, a to i v případě, že je komprese delší dobu trvat. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

::: moniker-end

### <a name="gzip-compression-provider"></a>Zprostředkovatel komprese GZip

Pomocí <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> můžete komprimovat odpovědi ve [formátu souboru gzip](https://tools.ietf.org/html/rfc1952).

Pokud nejsou do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>explicitně přidány žádní zprostředkovatelé komprese:

::: moniker range=">= aspnetcore-2.2"

* Zprostředkovatel komprese GZip je ve výchozím nastavení přidán k poli zprostředkovatelů komprese společně s [poskytovatelem Brotli Compression](#brotli-compression-provider).
* Komprese se nastaví jako výchozí Brotli komprese, když klient podporuje formát komprimovaných dat Brotli. Pokud klient nepodporuje Brotli, komprese je standardně nastavená na gzip, když klient podporuje kompresi gzip.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Zprostředkovatel komprese GZip je ve výchozím nastavení přidán k poli zprostředkovatelů komprese.
* Komprese je výchozím nastavením pro nástroj gzip, pokud klient podporuje kompresi gzip.

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Zprostředkovatel komprese GZip se musí přidat, když se explicitně přidají poskytovatelé komprese:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

Nastavte úroveň komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>. Zprostředkovatel komprese GZip je výchozím nastavením nejrychlejší kompresní úrovně ([CompressionLevel. nejrychlejší](xref:System.IO.Compression.CompressionLevel)), což nemusí způsobovat nejúčinnější kompresi. Pokud je žádoucí co nejúčinnější komprese, nakonfigurujte middleware pro optimální kompresi.

| Úroveň komprese | Popis |
| ----------------- | ----------- |
| [CompressionLevel. nejrychlejší](xref:System.IO.Compression.CompressionLevel) | Komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný výstup není optimálně komprimován. |
| [CompressionLevel. uncompression](xref:System.IO.Compression.CompressionLevel) | Není nutné provádět kompresi. |
| [CompressionLevel. optimální](xref:System.IO.Compression.CompressionLevel) | Odpovědi by se měly optimálně komprimovat, a to i v případě, že je komprese delší dobu trvat. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>Vlastní poskytovatelé

Vytvářejte vlastní implementace komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>. <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> představuje kódování obsahu, které vytváří tato `ICompressionProvider`. Middleware používá tyto informace k výběru poskytovatele na základě seznamu zadaného v hlavičce `Accept-Encoding` žádosti.

Pomocí ukázkové aplikace klient odesílá požadavek s hlavičkou `Accept-Encoding: mycustomcompression`. Middleware používá vlastní kompresní implementaci a vrací odpověď s hlavičkou `Content-Encoding: mycustomcompression`. Aby mohla vlastní implementace komprese fungovat, musí být klient schopný dekomprimovat vlastní kódování.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

Odešlete žádost do ukázkové aplikace s hlavičkou `Accept-Encoding: mycustomcompression` a sledujte hlavičky odpovědi. V odpovědi jsou k dispozici hlavičky `Vary` a `Content-Encoding`. Text odpovědi (nezobrazený) není ukázkou komprimován. Neexistuje žádná implementace komprese v třídě `CustomCompressionProvider` ukázky. Nicméně Ukázka ukazuje, kde byste takový kompresní algoritmus implementovali.

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou mycustomcompression. K odpovědi se přidají záhlaví Vary a kódování obsahu.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>typy MIME

Middleware určuje výchozí sadu typů MIME pro kompresi:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Nahraďte nebo připojovat typy MIME pomocí možností middlewaru pro kompresi odpovědí. Všimněte si, že zástupné typy MIME, například `text/*`, nejsou podporovány. Ukázková aplikace přidá typ MIME pro `image/svg+xml` a komprimuje a obsluhuje obrázek ASP.NET Core banneru (*banner. SVG*).

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>Komprese pomocí zabezpečeného protokolu

Komprimované odpovědi přes zabezpečená připojení se dají řídit pomocí možnosti `EnableForHttps`, která je ve výchozím nastavení zakázaná. Použití komprese s dynamicky generovanými stránkami může vést k problémům se zabezpečením, jako jsou [trestné činy](https://wikipedia.org/wiki/CRIME_(security_exploit)) a útoky za [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) .

## <a name="adding-the-vary-header"></a>Přidávání záhlaví Vary

Při kompresi odpovědí na základě hlavičky `Accept-Encoding` existuje potenciálně více komprimovaných verzí odpovědi a nekomprimovaná verze. Aby bylo možné zadat do mezipamětí klienta a proxy serveru, že existuje více verzí a měl by být uložen, je `Vary` hlavička přidána s hodnotou `Accept-Encoding`. V ASP.NET Core 2,0 nebo novějším, middleware přidá hlavičku `Vary` automaticky, když je odpověď komprimovaná.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Problém middlewaru, když za Nginx reverzní proxy

Pokud je požadavek proxy serverem Nginx, bude odstraněna hlavička `Accept-Encoding`. Odebrání hlavičky `Accept-Encoding` brání middlewaru v komprimaci odpovědi. Další informace naleznete v tématu [Nginx: Compression a decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Tento problém je sledován pomocí [předávací komprese pro Nginx (ASPNET/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Práce s dynamickou kompresí služby IIS

Pokud máte aktivní dynamický kompresní modul služby IIS nakonfigurovaný na úrovni serveru, který chcete pro aplikaci zakázat, zakažte modul s přidáním do souboru *Web. config* . Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Řešení potíží

Použijte nástroj, jako je [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)nebo [post](https://www.getpostman.com/), což vám umožní nastavit hlavičku požadavku `Accept-Encoding` a prozkoumat hlavičky, velikost a text odpovědi. Ve výchozím nastavení middleware pro komprimaci odezvy komprimuje odezvy, které splňují následující podmínky:

::: moniker range=">= aspnetcore-2.2"

* Hlavička `Accept-Encoding` je k dispozici s hodnotou `br`, `gzip`, `*`nebo vlastním kódováním, které odpovídá vlastnímu poskytovateli komprese, který jste navázali. Hodnota nesmí být `identity` nebo mít hodnotu kvality (qvalue, `q`) nastavenou na 0 (nula).
* Je nutné nastavit typ MIME (`Content-Type`) a musí odpovídat typu MIME nakonfigurovanému v <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* Požadavek nesmí obsahovat hlavičku `Content-Range`.
* Požadavek musí používat protokol HTTP (nezabezpečený protokol), pokud není v možnostech middleware pro kompresi odpovědí nakonfigurovaný protokol HTTPS (Secure Protocol). *Při povolování komprese zabezpečeného obsahu si všimněte [výše popsaného](#compression-with-secure-protocol) nebezpečí.*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Hlavička `Accept-Encoding` je k dispozici s hodnotou `gzip`, `*`nebo vlastním kódováním, které se shoduje s vlastním poskytovatelem komprese, který jste navázali. Hodnota nesmí být `identity` nebo mít hodnotu kvality (qvalue, `q`) nastavenou na 0 (nula).
* Je nutné nastavit typ MIME (`Content-Type`) a musí odpovídat typu MIME nakonfigurovanému v <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* Požadavek nesmí obsahovat hlavičku `Content-Range`.
* Požadavek musí používat protokol HTTP (nezabezpečený protokol), pokud není v možnostech middleware pro kompresi odpovědí nakonfigurovaný protokol HTTPS (Secure Protocol). *Při povolování komprese zabezpečeného obsahu si všimněte [výše popsaného](#compression-with-secure-protocol) nebezpečí.*

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Síť pro vývojáře Mozilla: přijmout – kódování](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 část 3.1.2.1: kódování obsahu](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 oddíl 4.2.3: kódování gzip](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Specifikace formátu souboru GZIP verze 4,3](https://www.ietf.org/rfc/rfc1952.txt)
