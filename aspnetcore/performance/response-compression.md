---
title: Kompresi odpovědí v ASP.NET Core
author: guardrex
description: Další informace o kompresi odpovědí a jak používat Middleware pro kompresi odpovědí v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: performance/response-compression
ms.openlocfilehash: a9f72a6816298b11e7b7d30b2b4bd44083baab3a
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099036"
---
# <a name="response-compression-in-aspnet-core"></a>Kompresi odpovědí v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples) ([stažení](xref:index#how-to-download-a-sample))

Šířka pásma sítě je omezená prostředků. Na odezvu aplikace, je obvykle nezmenšit velikost této odpovědi často výrazně zvyšuje. Jedním ze způsobů ke zmenšení velikosti datové části je kompresi odpovědí vaší aplikace.

## <a name="when-to-use-response-compression-middleware"></a>Kdy použít Middleware pro kompresi odpovědí

Použití technologie komprese odpovědi na serveru IIS, Apache nebo Nginx. Výkon middleware pravděpodobně nebude odpovídat moduly serveru. [HTTP.sys server](xref:fundamentals/servers/httpsys) serveru a [Kestrel](xref:fundamentals/servers/kestrel) server aktuálně nenabízí podporu integrovanou komprese.

Middleware pro kompresi odpovědí použijte, když jste:

* Nelze použít následující technologie serverových komprese:
  * [Modul dynamická komprese služby IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate modulu](http://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Server Nginx komprese a dekomprese](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Hostování přímo na:
  * [HTTP.sys server](xref:fundamentals/servers/httpsys) (dříve se označovaly jako WebListener)
  * [Kestrel serveru](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Kompresi odpovědí

Obvykle žádnou odpověď komprimované nativně využívat kompresi odpovědí. Odpovědí se komprimované nativně obvykle patří: Šablony stylů CSS, JavaScript, HTML, XML a JSON. Nativně komprimované prostředky, jako jsou například soubory PNG by neměly komprimovat. Pokud se pokusíte další komprimovat nativně zkomprimovanou odpověď, všechny malé Další velikosti a přenos zkrácení času nutného pravděpodobně overshadowed v době, jakou trvalo zpracování komprese. Nekomprimovat soubory menší než přibližně 150 – 1 000 bajtů (v závislosti na obsahu souboru a efektivity komprese). Režie komprimace malých souborů může vytvořit komprimovaný soubor větší než nekomprimovaného souboru.

Když klient může zpracovat komprimovaného obsahu, klient informuje server její možnosti odesláním `Accept-Encoding` záhlaví s požadavkem. Když server odešle komprimovaného obsahu, musí obsahovat informace `Content-Encoding` záhlaví na tom, jak je zakódován zkomprimovanou odpověď. V následující tabulce jsou uvedeny obsahu kódování označení podporované middlewarem.

::: moniker range=">= aspnetcore-2.2"

| `Accept-Encoding` hodnoty hlavičky | Middleware podporována | Popis |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Ano (výchozí)        | [Formát komprimovaných dat Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Ne                   | [Formát komprimovaných dat DEFLATE](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Ne                   | [W3C XML efektivní výměny](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Ano                  | [Formát souborů GZIP](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Ano                  | "Bez kódování" identifikátor: Odpověď nesmí být zakódován. |
| `pack200-gzip`                  | Ne                   | [Formát přenosu sítě pro archivy Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Ano                  | Žádný k dispozici obsah, kódování není explicitně požadovaný |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| `Accept-Encoding` hodnoty hlavičky | Middleware podporována | Popis |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Ne                   | [Formát komprimovaných dat Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Ne                   | [Formát komprimovaných dat DEFLATE](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Ne                   | [W3C XML efektivní výměny](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Ano (výchozí)        | [Formát souborů GZIP](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Ano                  | "Bez kódování" identifikátor: Odpověď nesmí být zakódován. |
| `pack200-gzip`                  | Ne                   | [Formát přenosu sítě pro archivy Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Ano                  | Žádný k dispozici obsah, kódování není explicitně požadovaný |

::: moniker-end

Další informace najdete v tématu [IANA oficiální kódování seznamu obsahu](http://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

Middleware umožňuje přidat další komprese zprostředkovatelé pro vlastní `Accept-Encoding` hodnoty hlavičky. Další informace najdete v tématu [Vlastní zprostředkovatelé](#custom-providers) níže.

Middleware je schopný reagovat na hodnotu kvality (qvalue, `q`) vážení při odeslání klientem nástroje k určení priority schémat komprese. Další informace najdete v tématu [RFC 7231: Přijmout kódování](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Algoritmy komprese podléhají kompromis mezi komprese rychlost a efektivitu komprese. *Efektivnost definování* v tomto kontextu označuje velikost výstup po kompresi. Nejmenší velikost se dosahuje nejčastěji *optimální* komprese.

Záhlaví účastnící se požaduje, odesílání, ukládání do mezipaměti a přijímání komprimovaného obsahu jsou popsány v následující tabulce.

| Záhlaví             | Role |
| ------------------ | ---- |
| `Accept-Encoding`  | Odeslaných z klienta na server k označení schémata přijatelné klientovi kódování obsahu. |
| `Content-Encoding` | Odeslaná ze serveru klientovi jako oznámení, kódování obsahu v datové části |
| `Content-Length`   | Pokud dojde k kompresi, `Content-Length` záhlaví Odebereme, protože změny obsahu těla při kompresi odpovědi. |
| `Content-MD5`      | Pokud dojde k komprese, `Content-MD5` záhlaví Odebereme, protože došlo ke změně obsah textu a -the-hash již není platný. |
| `Content-Type`     | Určuje typ MIME obsahu. Každou odpověď by měla určit jeho `Content-Type`. Middleware ověří tuto hodnotu k určení, pokud je nutné zkomprimovat odpovědi. Middleware určuje sadu [výchozí typy MIME](#mime-types) , která můžete kódovat, ale můžete nahradit nebo přidat typy MIME. |
| `Vary`             | Při odeslání server s hodnotou `Accept-Encoding` pro klienty a proxy servery, `Vary` hlavička označuje klienta nebo proxy server, který by měl mezipaměti (lišit) odpovědi na základě hodnoty z `Accept-Encoding` záhlaví požadavku. Vrací obsah s výsledkem `Vary: Accept-Encoding` záhlaví je, že i komprimované a jsou v odpovědi na nekomprimované samostatně uložené v mezipaměti. |

Seznamte se s funkcemi Middleware pro kompresi odpovědí s [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples). Ukázka znázorňuje:

* Kompresi odpovědí aplikace pomocí Gzip a poskytovatelé vlastní komprese.
* Jak přidat typ MIME do seznamu výchozích typů MIME pro kompresi.

## <a name="package"></a>Balíček

::: moniker range=">= aspnetcore-2.1"

Aby byly middleware v projektu, přidejte odkaz na [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), což zahrnuje [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) balíčku.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Aby byly middleware v projektu, přidejte odkaz na [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage), což zahrnuje [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) balíčku.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Aby byly middleware v projektu, přidejte odkaz na [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) balíčku.

::: moniker-end

## <a name="configuration"></a>Konfigurace

::: moniker range=">= aspnetcore-2.2"

Následující kód ukazuje, jak povolit Middleware pro kompresi odpovědí pro typy MIME výchozí a komprese poskytovatele ([Brotli](#brotli-compression-provider) a [Gzip](#gzip-compression-provider)):

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Následující kód ukazuje, jak povolit Middleware pro kompresi odpovědí pro typy MIME výchozí a [poskytovatele kompresi Gzip](#gzip-compression-provider):

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

* `app.UseResponseCompression` musí být volána před `app.UseMvc`.
* Použijte nástroj, jako [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), nebo [Postman](https://www.getpostman.com/) nastavit `Accept-Encoding` hlavička požadavku a studovat hlavičky odpovědi, velikost a text.

Odeslat žádost pro ukázkovou aplikaci bez `Accept-Encoding` záhlaví a podívejte se, že odpověď nekomprimované. `Content-Encoding` a `Vary` záhlaví nejsou k dispozici v odpovědi.

![Fiddler okno zobrazuje výsledek požadavku bez hlavičky Accept-Encoding. Odpověď není v komprimovaném tvaru.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

Odeslat žádost pro ukázkovou aplikaci s `Accept-Encoding: br` záhlaví (Brotli komprese) a podívejte se, že je komprimován odpovědi. `Content-Encoding` a `Vary` záhlaví jsou k dispozici v odpovědi.

![Fiddler okno zobrazuje výsledek žádosti s hlavičku Accept-Encoding a hodnotu br. Měnit a kódování obsahu hlaviček jsou přidány do odpovědi. Odpověď je komprimován.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Odeslat žádost pro ukázkovou aplikaci s `Accept-Encoding: gzip` záhlaví a podívejte se, že je komprimován odpovědi. `Content-Encoding` a `Vary` záhlaví jsou k dispozici v odpovědi.

![Fiddler okno zobrazuje výsledek žádosti s hlavičku Accept-Encoding a hodnotu gzip. Měnit a kódování obsahu hlaviček jsou přidány do odpovědi. Odpověď je komprimován.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>Zprostředkovatelé

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Zprostředkovatel Brotli komprese

Použití `BrotliCompressionProvider` kompresi odpovědí s [Formát komprimovaných dat Brotli](https://tools.ietf.org/html/rfc7932).

Pokud žádný poskytovatel komprese jsou explicitně přidány do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* Zprostředkovatel komprese Brotli je přidán ve výchozím nastavení do pole komprese poskytovatelů spolu s [poskytovatele kompresi Gzip](#gzip-compression-provider).
* Výchozí nastavení komprese Brotli komprese při Brotli Formát komprimovaných dat je podporované klientem. Pokud klient není podporován Brotli, komprese výchozí Gzip Pokud klient podporuje kompresi Gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Zprostředkovatel Brotoli komprese musí být přidán, když jsou explicitně přidány všechny zprostředkovatele komprese:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

Nastavit úroveň díky komprese `BrotliCompressionProviderOptions`. Zprostředkovatel komprese Brotli výchozí hodnota je nejvyšší úroveň komprese ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), který nemusí vracet nejúčinnější komprese. V případě potřeby je nejúčinnější komprese, nakonfigurujte middleware pro kompresi optimální.

| Úroveň komprese | Popis |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | Komprese by se měla dokončit co nejrychleji i v případě, že výsledný výstup není komprimována optimálně. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | Bez komprese je třeba provést. |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | Odpovědí by měl být optimálně komprimovaný, i v případě, že komprese trvá déle. |

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

### <a name="gzip-compression-provider"></a>Zprostředkovatel kompresi GZIP

Použití <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> kompresi odpovědí s [formátu Gzip](https://tools.ietf.org/html/rfc1952).

Pokud žádný poskytovatel komprese jsou explicitně přidány do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

::: moniker range=">= aspnetcore-2.2"

* Zprostředkovatel kompresi Gzip je přidat ve výchozím nastavení do pole, poskytovatelů komprese spolu s [Brotli komprese poskytovatele](#brotli-compression-provider).
* Výchozí nastavení komprese Brotli komprese při Brotli Formát komprimovaných dat je podporované klientem. Pokud klient není podporován Brotli, komprese výchozí Gzip Pokud klient podporuje kompresi Gzip.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Zprostředkovatel kompresi Gzip je ve výchozím nastavení do pole komprese poskytovatelů.
* Výchozí hodnota kompresi Gzip, pokud klient podporuje kompresi Gzip.

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Zprostředkovatel kompresi Gzip musí být přidán, když jsou explicitně přidány všechny zprostředkovatele komprese:

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5)]

::: moniker-end

Nastavit úroveň díky komprese <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>. Zprostředkovatel kompresi Gzip výchozí hodnota je nejvyšší úroveň komprese ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), který nemusí vracet nejúčinnější komprese. V případě potřeby je nejúčinnější komprese, nakonfigurujte middleware pro kompresi optimální.

| Úroveň komprese | Popis |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | Komprese by se měla dokončit co nejrychleji i v případě, že výsledný výstup není komprimována optimálně. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | Bez komprese je třeba provést. |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | Odpovědí by měl být optimálně komprimovaný, i v případě, že komprese trvá déle. |

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

### <a name="custom-providers"></a>Vlastní zprostředkovatelé

Vytvořit vlastní komprese implementace s <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>. <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> Reprezentuje obsah, kódování, že tento `ICompressionProvider` vytvoří. Middleware použije tyto informace k výběru poskytovatele podle zadaného v seznamu `Accept-Encoding` záhlaví požadavku.

Pomocí ukázkové aplikace, klient odešle požadavek s `Accept-Encoding: mycustomcompression` záhlaví. Middleware použije implementace vlastní komprese a vrátí odpověď se `Content-Encoding: mycustomcompression` záhlaví. Klient musí být schopen dekomprimovat vlastní kódování v pořadí pro implementaci vlastního komprese pracovat.

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

```csharp
public class CustomCompressionProvider : ICompressionProvider
{
    public string EncodingName => "mycustomcompression";
    public bool SupportsFlush => true;

    public Stream CreateStream(Stream outputStream)
    {
        // Create a custom compression stream wrapper here
        return outputStream;
    }
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=6,12-15)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6,12-15)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

Odeslat žádost pro ukázkovou aplikaci s `Accept-Encoding: mycustomcompression` záhlaví a sledujte hlavičky odpovědi. `Vary` a `Content-Encoding` záhlaví jsou k dispozici v odpovědi. Text odpovědi (není vidět) není komprimována v rámci ukázky. Není k dispozici implementace komprese ve `CustomCompressionProvider` třídy vzorku. Ale tato ukázka vysvětluje, kdy, měli byste implementovat algoritmus komprese.

![Fiddler okno zobrazuje výsledek žádosti s hlavičku Accept-Encoding a hodnotu mycustomcompression. Měnit a kódování obsahu hlaviček jsou přidány do odpovědi.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>typy MIME

Middleware Určuje výchozí sadu typů MIME pro komprese:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Nahradit nebo přidat typy MIME s možnostmi Middleware pro kompresi odpovědí. Všimněte si danému zástupnému znaku MIME typy, jako například `text/*` nejsou podporovány. Ukázková aplikace přidá typ MIME pro `image/svg+xml` a komprimuje a ASP.NET Core slouží banner image (*banner.svg*).

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7-9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>Komprese se zabezpečený protokol

Komprimované odpovědi prostřednictvím zabezpečeného připojení se dá řídit pomocí `EnableForHttps` možnost, která je ve výchozím nastavení zakázané. Pomocí komprese dynamicky generované stránky může vést k problémům zabezpečení, jako [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) a [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) útoky.

## <a name="adding-the-vary-header"></a>Přidání měnit hlavičky

::: moniker range=">= aspnetcore-2.0"

Při kompresi odpovědí na základě `Accept-Encoding` záhlaví, jsou potenciálně více komprimované verze odpovědi a nekomprimované verze. Aby bylo možné, že více verzí existují a by měla být uložena, dát pokyn mezipaměti klienta a serveru proxy `Vary` záhlaví se přidá s `Accept-Encoding` hodnotu. V technologii ASP.NET Core 2.0 nebo novější, přidá middleware `Vary` záhlaví automaticky, pokud je odpověď je komprimován.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Při kompresi odpovědí na základě `Accept-Encoding` záhlaví, jsou potenciálně více komprimované verze odpovědi a nekomprimované verze. Aby bylo možné, že více verzí existují a by měla být uložena, dát pokyn mezipaměti klienta a serveru proxy `Vary` záhlaví se přidá s `Accept-Encoding` hodnotu. V ASP.NET Core 1.x, přidání `Vary` hlavičky odpovědi se provádí ručně:

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Middleware problém při za reverzní proxy server Nginx

Pokud je požadavek směrovány přes proxy server pomocí Nginx, `Accept-Encoding` odebrat záhlaví. Odebrání `Accept-Encoding` záhlaví zabraňuje middleware komprese odpovědi. Další informace najdete v tématu [NGINX: Komprese a dekomprese](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Tento problém je sledován pomocí funkce [zjistit průchozí komprese Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Práce s dynamické komprese služby IIS

Pokud máte aktivní IIS dynamické komprese modul nakonfigurována na úrovni serveru, který chcete zakázat aplikaci zakázat modul s parametrem doplněk *web.config* souboru. Další informace najdete v tématu [moduly IIS zakázání](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Poradce při potížích

Pomocí některého nástroje, například [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), nebo [Postman](https://www.getpostman.com/), což umožní nastavit `Accept-Encoding` hlavička požadavku a studovat hlavičky odpovědi, velikost a text. Ve výchozím nastavení komprimuje Middleware pro kompresi odpovědí odpovědi, které splňovat tyto podmínky:

::: moniker range=">= aspnetcore-2.2"

* `Accept-Encoding` Záhlaví je k dispozici s hodnotou `br`, `gzip`, `*`, nebo vlastní kódování, který odpovídá vlastní komprese poskytovatele, který jste vytvořili. Hodnota nesmí být `identity` nebo mít hodnotu kvality (qvalue, `q`) nastavení 0 (nula).
* Typ MIME (`Content-Type`) musí být nastavena a musí odpovídat typu MIME na nakonfigurované <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* Nesmí obsahovat požadavek `Content-Range` záhlaví.
* Žádost musí používat zabezpečený protokol (http), pokud zabezpečený protokol (https) je nakonfigurován v možnostech Middleware pro kompresi odpovědí. *Všimněte si, nebezpečí [výše popsané](#compression-with-secure-protocol) při povolování zabezpečené komprese obsahu.*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* `Accept-Encoding` Záhlaví je k dispozici s hodnotou `gzip`, `*`, nebo vlastní kódování, který odpovídá vlastní komprese poskytovatele, který jste vytvořili. Hodnota nesmí být `identity` nebo mít hodnotu kvality (qvalue, `q`) nastavení 0 (nula).
* Typ MIME (`Content-Type`) musí být nastavena a musí odpovídat typu MIME na nakonfigurované <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* Nesmí obsahovat požadavek `Content-Range` záhlaví.
* Žádost musí používat zabezpečený protokol (http), pokud zabezpečený protokol (https) je nakonfigurován v možnostech Middleware pro kompresi odpovědí. *Všimněte si, nebezpečí [výše popsané](#compression-with-secure-protocol) při povolování zabezpečené komprese obsahu.*

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla Developer Network: Přijmout kódování](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 části 3.1.2.1: Codings obsahu](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 oddílu 4.2.3: Kódování GZIP](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Verze specifikace formátu souboru GZIP 4.3](http://www.ietf.org/rfc/rfc1952.txt)
