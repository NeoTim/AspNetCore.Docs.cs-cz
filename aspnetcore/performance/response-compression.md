---
title: Komprese odezvy v ASP.NET Core
author: rick-anderson
description: Přečtěte si o kompresi odpovědí a způsobu použití middleware pro kompresi odpovědí v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/response-compression
ms.openlocfilehash: 1dd931d0ee654b888814df8a0d0675d32b5c3a20
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020961"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="37a89-103">Komprese odezvy v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37a89-103">Response compression in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="37a89-104">Šířka pásma sítě je omezeného prostředku.</span><span class="sxs-lookup"><span data-stu-id="37a89-104">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="37a89-105">Zmenšení velikosti odpovědi obvykle zvyšuje rychlost odezvy aplikace, často výrazně.</span><span class="sxs-lookup"><span data-stu-id="37a89-105">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="37a89-106">Jedním ze způsobů, jak omezit velikost datových částí, je komprimace reakcí aplikace.</span><span class="sxs-lookup"><span data-stu-id="37a89-106">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="37a89-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37a89-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="37a89-108">Kdy použít middleware pro kompresi odpovědí</span><span class="sxs-lookup"><span data-stu-id="37a89-108">When to use Response Compression Middleware</span></span>

<span data-ttu-id="37a89-109">Používejte technologie pro kompresi odpovědí na základě serveru ve službě IIS, Apache nebo Nginx.</span><span class="sxs-lookup"><span data-stu-id="37a89-109">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="37a89-110">Výkon middleware se pravděpodobně neshoduje s modulem serverových modulů.</span><span class="sxs-lookup"><span data-stu-id="37a89-110">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="37a89-111">Server [HTTP.sys](xref:fundamentals/servers/httpsys) Server a server [Kestrel](xref:fundamentals/servers/kestrel) momentálně nenabízí integrovanou podporu komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="37a89-112">Používejte middleware pro kompresi odpovědí, pokud jste:</span><span class="sxs-lookup"><span data-stu-id="37a89-112">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="37a89-113">Nelze použít následující technologie komprese založené na serveru:</span><span class="sxs-lookup"><span data-stu-id="37a89-113">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="37a89-114">Modul dynamické komprese služby IIS</span><span class="sxs-lookup"><span data-stu-id="37a89-114">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="37a89-115">Modul Apache mod_deflate</span><span class="sxs-lookup"><span data-stu-id="37a89-115">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="37a89-116">Nginx kompresi a dekomprese</span><span class="sxs-lookup"><span data-stu-id="37a89-116">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="37a89-117">Hostování přímo na:</span><span class="sxs-lookup"><span data-stu-id="37a89-117">Hosting directly on:</span></span>
  * <span data-ttu-id="37a89-118">[ServerHTTP.sys](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten)</span><span class="sxs-lookup"><span data-stu-id="37a89-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="37a89-119">Server Kestrel</span><span class="sxs-lookup"><span data-stu-id="37a89-119">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="37a89-120">Komprese odpovědí</span><span class="sxs-lookup"><span data-stu-id="37a89-120">Response compression</span></span>

<span data-ttu-id="37a89-121">Obvykle by jakákoli odpověď, která není nativně komprimovaná, mohla využívat kompresi odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-121">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="37a89-122">Odpovědi, které nejsou nativně zkomprimované, obvykle zahrnují: CSS, JavaScript, HTML, XML a JSON.</span><span class="sxs-lookup"><span data-stu-id="37a89-122">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="37a89-123">Neměli byste komprimovat nativně komprimované prostředky, jako jsou soubory PNG.</span><span class="sxs-lookup"><span data-stu-id="37a89-123">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="37a89-124">Pokusíte-li se dále komprimovat nativně komprimovanou odpověď, jakékoli malé další snížení velikosti a čas přenosu budou pravděpodobně převrženy časem, který trvalo zpracování komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-124">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="37a89-125">Nekomprimuje soubory menší než přibližně 150-1000 bajtů (v závislosti na obsahu souboru a efektivitě komprese).</span><span class="sxs-lookup"><span data-stu-id="37a89-125">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="37a89-126">Režie komprimace malých souborů může vytvořit komprimovaný soubor, který je větší než nekomprimovaný soubor.</span><span class="sxs-lookup"><span data-stu-id="37a89-126">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="37a89-127">Když klient může zpracovat komprimovaný obsah, klient musí informovat Server svých schopností odesláním `Accept-Encoding` hlavičky s požadavkem.</span><span class="sxs-lookup"><span data-stu-id="37a89-127">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="37a89-128">Když server pošle komprimovaný obsah, musí obsahovat informace v `Content-Encoding` záhlaví způsobu, jakým je komprimovaná odpověď zakódovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-128">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="37a89-129">V následující tabulce jsou uvedena označení kódování obsahu podporovaná middlewarem.</span><span class="sxs-lookup"><span data-stu-id="37a89-129">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="37a89-130">`Accept-Encoding`hodnoty hlaviček</span><span class="sxs-lookup"><span data-stu-id="37a89-130">`Accept-Encoding` header values</span></span> | <span data-ttu-id="37a89-131">Middleware – podporováno</span><span class="sxs-lookup"><span data-stu-id="37a89-131">Middleware Supported</span></span> | <span data-ttu-id="37a89-132">Popis</span><span class="sxs-lookup"><span data-stu-id="37a89-132">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="37a89-133">Ano (výchozí)</span><span class="sxs-lookup"><span data-stu-id="37a89-133">Yes (default)</span></span>        | [<span data-ttu-id="37a89-134">Formát komprimovaných dat Brotli</span><span class="sxs-lookup"><span data-stu-id="37a89-134">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="37a89-135">Ne</span><span class="sxs-lookup"><span data-stu-id="37a89-135">No</span></span>                   | [<span data-ttu-id="37a89-136">Formát ZÚŽENé komprese dat</span><span class="sxs-lookup"><span data-stu-id="37a89-136">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="37a89-137">Ne</span><span class="sxs-lookup"><span data-stu-id="37a89-137">No</span></span>                   | [<span data-ttu-id="37a89-138">Efektivní výměna XML pro W3C</span><span class="sxs-lookup"><span data-stu-id="37a89-138">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="37a89-139">Ano</span><span class="sxs-lookup"><span data-stu-id="37a89-139">Yes</span></span>                  | [<span data-ttu-id="37a89-140">Formát souboru gzip</span><span class="sxs-lookup"><span data-stu-id="37a89-140">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="37a89-141">Ano</span><span class="sxs-lookup"><span data-stu-id="37a89-141">Yes</span></span>                  | <span data-ttu-id="37a89-142">Identifikátor "bez kódování": odpověď nesmí být kódovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-142">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="37a89-143">Ne</span><span class="sxs-lookup"><span data-stu-id="37a89-143">No</span></span>                   | [<span data-ttu-id="37a89-144">Formát přenosu v síti pro archivy Java</span><span class="sxs-lookup"><span data-stu-id="37a89-144">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="37a89-145">Ano</span><span class="sxs-lookup"><span data-stu-id="37a89-145">Yes</span></span>                  | <span data-ttu-id="37a89-146">Jakékoli dostupné kódování obsahu není explicitně požadováno.</span><span class="sxs-lookup"><span data-stu-id="37a89-146">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="37a89-147">Další informace najdete v [seznamu kódů oficiálního obsahu IANA](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span><span class="sxs-lookup"><span data-stu-id="37a89-147">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="37a89-148">Middleware umožňuje přidat další zprostředkovatele komprese pro vlastní `Accept-Encoding` hodnoty hlaviček.</span><span class="sxs-lookup"><span data-stu-id="37a89-148">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="37a89-149">Další informace najdete v tématu [Vlastní zprostředkovatelé](#custom-providers) níže.</span><span class="sxs-lookup"><span data-stu-id="37a89-149">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="37a89-150">Middleware je schopná chovat hodnoty kvality (qvalue `q` ), když je klient odesílá k určení priorit schémat komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-150">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="37a89-151">Další informace najdete v [dokumentu RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span><span class="sxs-lookup"><span data-stu-id="37a89-151">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="37a89-152">Algoritmy komprese se vztahují na kompromisy mezi rychlostí komprese a efektivitou komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-152">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="37a89-153">*Efektivita* v tomto kontextu odkazuje na velikost výstupu po kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-153">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="37a89-154">Nejmenší velikost dosáhne *optimální* komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-154">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="37a89-155">Hlavičky zahrnuté v tématu vyžádání, odeslání, ukládání do mezipaměti a příjem komprimovaného obsahu jsou popsány v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="37a89-155">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="37a89-156">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="37a89-156">Header</span></span>             | <span data-ttu-id="37a89-157">Role</span><span class="sxs-lookup"><span data-stu-id="37a89-157">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="37a89-158">Odesílá se z klienta na server k označení schémat kódování obsahu, které jsou přijatelné pro klienta.</span><span class="sxs-lookup"><span data-stu-id="37a89-158">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="37a89-159">Odesílá se ze serveru do klienta, aby označovala kódování obsahu v datové části.</span><span class="sxs-lookup"><span data-stu-id="37a89-159">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="37a89-160">Když dojde k komprimaci, `Content-Length` Hlavička se odebere, protože obsah těla se změní, když je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-160">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="37a89-161">Když dojde k komprimaci, `Content-MD5` Hlavička se odebere, protože se změnil obsah těla a hodnota hash již není platná.</span><span class="sxs-lookup"><span data-stu-id="37a89-161">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="37a89-162">Určuje typ MIME obsahu.</span><span class="sxs-lookup"><span data-stu-id="37a89-162">Specifies the MIME type of the content.</span></span> <span data-ttu-id="37a89-163">Každá odpověď by měla specifikovat svůj `Content-Type` .</span><span class="sxs-lookup"><span data-stu-id="37a89-163">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="37a89-164">Middleware kontroluje tuto hodnotu, aby určila, jestli by měla být komprimovaná odpověď.</span><span class="sxs-lookup"><span data-stu-id="37a89-164">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="37a89-165">Middleware určuje sadu [výchozích typů MIME](#mime-types) , které může kódovat, ale můžete nahradit nebo přidat typy MIME.</span><span class="sxs-lookup"><span data-stu-id="37a89-165">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="37a89-166">Při odeslání serverem s hodnotou `Accept-Encoding` pro klienty a proxy servery `Vary` indikuje hlavička klientovi nebo proxy serveru, že by měl ukládat (Vary) odpovědi na základě hodnoty `Accept-Encoding` záhlaví požadavku.</span><span class="sxs-lookup"><span data-stu-id="37a89-166">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="37a89-167">Výsledkem vrácení obsahu s `Vary: Accept-Encoding` hlavičkou je, že komprimované i nekomprimované odpovědi jsou ukládány do mezipaměti samostatně.</span><span class="sxs-lookup"><span data-stu-id="37a89-167">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="37a89-168">Prozkoumejte funkce middleware pro kompresi odpovědí s [ukázkovou aplikací](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span><span class="sxs-lookup"><span data-stu-id="37a89-168">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="37a89-169">Ukázka znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="37a89-169">The sample illustrates:</span></span>

* <span data-ttu-id="37a89-170">Komprese odpovědí aplikace pomocí gzip a vlastních zprostředkovatelů komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-170">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="37a89-171">Postup přidání typu MIME do výchozího seznamu typů MIME pro kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-171">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="37a89-172">Balíček</span><span class="sxs-lookup"><span data-stu-id="37a89-172">Package</span></span>

<span data-ttu-id="37a89-173">Middleware pro kompresi odpovědí poskytuje balíček [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) , který je implicitně zahrnutý v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="37a89-173">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="37a89-174">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="37a89-174">Configuration</span></span>

<span data-ttu-id="37a89-175">Následující kód ukazuje, jak povolit middleware pro kompresi odpovědí pro výchozí typy MIME a zprostředkovatele komprese ([Brotli](#brotli-compression-provider) a [gzip](#gzip-compression-provider)):</span><span class="sxs-lookup"><span data-stu-id="37a89-175">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="37a89-176">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="37a89-176">Notes:</span></span>

* <span data-ttu-id="37a89-177">`app.UseResponseCompression`musí být volána před jakýmkoli middlewarem, který komprimuje odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-177">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="37a89-178">Další informace naleznete v tématu <xref:fundamentals/middleware/index#middleware-order>.</span><span class="sxs-lookup"><span data-stu-id="37a89-178">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="37a89-179">Pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)nebo [post](https://www.getpostman.com/) , můžete nastavit `Accept-Encoding` hlavičku požadavku a prozkoumat hlavičky, velikost a text odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-179">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="37a89-180">Odešlete žádost do ukázkové aplikace bez `Accept-Encoding` hlavičky a sledujte, že odpověď je nekomprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-180">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="37a89-181">`Content-Encoding` `Vary` V odpovědi nejsou k dispozici hlavičky a.</span><span class="sxs-lookup"><span data-stu-id="37a89-181">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Okno Fiddler znázorňující výsledek požadavku bez hlavičky Accept-Encoding.](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="37a89-184">Odešlete žádost do ukázkové aplikace s `Accept-Encoding: br` hlavičkou (Brotli Compression) a sledujte, že je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-184">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="37a89-185">V `Content-Encoding` `Vary` odpovědi jsou k dispozici hlavičky a.</span><span class="sxs-lookup"><span data-stu-id="37a89-185">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou br.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="37a89-189">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="37a89-189">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="37a89-190">Brotli kompresní poskytovatel</span><span class="sxs-lookup"><span data-stu-id="37a89-190">Brotli Compression Provider</span></span>

<span data-ttu-id="37a89-191">Použijte <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> pro komprimaci odpovědí s [formátem komprimovaných dat Brotli](https://tools.ietf.org/html/rfc7932).</span><span class="sxs-lookup"><span data-stu-id="37a89-191">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="37a89-192">Pokud nejsou explicitně přidány žádní zprostředkovatelé komprese do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :</span><span class="sxs-lookup"><span data-stu-id="37a89-192">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="37a89-193">Zprostředkovatel komprese Brotli se ve výchozím nastavení přidá k poli zprostředkovatelů komprese společně se [zprostředkovatelem komprese GZip](#gzip-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="37a89-193">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="37a89-194">Komprese se nastaví jako výchozí Brotli komprese, když klient podporuje formát komprimovaných dat Brotli.</span><span class="sxs-lookup"><span data-stu-id="37a89-194">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="37a89-195">Pokud klient nepodporuje Brotli, komprese je standardně nastavená na gzip, když klient podporuje kompresi gzip.</span><span class="sxs-lookup"><span data-stu-id="37a89-195">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="37a89-196">Zprostředkovatel komprese Brotli je nutné přidat, když jsou explicitně přidány poskytovatelé komprese:</span><span class="sxs-lookup"><span data-stu-id="37a89-196">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="37a89-197">Nastavte úroveň komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> .</span><span class="sxs-lookup"><span data-stu-id="37a89-197">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="37a89-198">Zprostředkovatel komprese Brotli je výchozím nastavením nejrychlejší kompresní úrovně ([CompressionLevel. nejrychlejší](xref:System.IO.Compression.CompressionLevel)), což nemusí způsobovat nejúčinnější kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-198">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="37a89-199">Pokud je žádoucí co nejúčinnější komprese, nakonfigurujte middleware pro optimální kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-199">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="37a89-200">Úroveň komprese</span><span class="sxs-lookup"><span data-stu-id="37a89-200">Compression Level</span></span> | <span data-ttu-id="37a89-201">Popis</span><span class="sxs-lookup"><span data-stu-id="37a89-201">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="37a89-202">CompressionLevel. nejrychlejší</span><span class="sxs-lookup"><span data-stu-id="37a89-202">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-203">Komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný výstup není optimálně komprimován.</span><span class="sxs-lookup"><span data-stu-id="37a89-203">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="37a89-204">CompressionLevel. uncompression</span><span class="sxs-lookup"><span data-stu-id="37a89-204">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-205">Není nutné provádět kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-205">No compression should be performed.</span></span> |
| [<span data-ttu-id="37a89-206">CompressionLevel. optimální</span><span class="sxs-lookup"><span data-stu-id="37a89-206">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-207">Odpovědi by se měly optimálně komprimovat, a to i v případě, že je komprese delší dobu trvat.</span><span class="sxs-lookup"><span data-stu-id="37a89-207">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="37a89-208">Zprostředkovatel komprese GZip</span><span class="sxs-lookup"><span data-stu-id="37a89-208">Gzip Compression Provider</span></span>

<span data-ttu-id="37a89-209">Použijte <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> pro komprimaci odpovědí ve [formátu souboru gzip](https://tools.ietf.org/html/rfc1952).</span><span class="sxs-lookup"><span data-stu-id="37a89-209">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="37a89-210">Pokud nejsou explicitně přidány žádní zprostředkovatelé komprese do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :</span><span class="sxs-lookup"><span data-stu-id="37a89-210">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="37a89-211">Zprostředkovatel komprese GZip je ve výchozím nastavení přidán k poli zprostředkovatelů komprese společně s [poskytovatelem Brotli Compression](#brotli-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="37a89-211">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="37a89-212">Komprese se nastaví jako výchozí Brotli komprese, když klient podporuje formát komprimovaných dat Brotli.</span><span class="sxs-lookup"><span data-stu-id="37a89-212">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="37a89-213">Pokud klient nepodporuje Brotli, komprese je standardně nastavená na gzip, když klient podporuje kompresi gzip.</span><span class="sxs-lookup"><span data-stu-id="37a89-213">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="37a89-214">Zprostředkovatel komprese GZip se musí přidat, když se explicitně přidají poskytovatelé komprese:</span><span class="sxs-lookup"><span data-stu-id="37a89-214">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="37a89-215">Nastavte úroveň komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> .</span><span class="sxs-lookup"><span data-stu-id="37a89-215">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="37a89-216">Zprostředkovatel komprese GZip je výchozím nastavením nejrychlejší kompresní úrovně ([CompressionLevel. nejrychlejší](xref:System.IO.Compression.CompressionLevel)), což nemusí způsobovat nejúčinnější kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-216">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="37a89-217">Pokud je žádoucí co nejúčinnější komprese, nakonfigurujte middleware pro optimální kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-217">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="37a89-218">Úroveň komprese</span><span class="sxs-lookup"><span data-stu-id="37a89-218">Compression Level</span></span> | <span data-ttu-id="37a89-219">Popis</span><span class="sxs-lookup"><span data-stu-id="37a89-219">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="37a89-220">CompressionLevel. nejrychlejší</span><span class="sxs-lookup"><span data-stu-id="37a89-220">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-221">Komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný výstup není optimálně komprimován.</span><span class="sxs-lookup"><span data-stu-id="37a89-221">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="37a89-222">CompressionLevel. uncompression</span><span class="sxs-lookup"><span data-stu-id="37a89-222">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-223">Není nutné provádět kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-223">No compression should be performed.</span></span> |
| [<span data-ttu-id="37a89-224">CompressionLevel. optimální</span><span class="sxs-lookup"><span data-stu-id="37a89-224">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-225">Odpovědi by se měly optimálně komprimovat, a to i v případě, že je komprese delší dobu trvat.</span><span class="sxs-lookup"><span data-stu-id="37a89-225">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="37a89-226">Vlastní poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="37a89-226">Custom providers</span></span>

<span data-ttu-id="37a89-227">Vytvořte vlastní implementace komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> .</span><span class="sxs-lookup"><span data-stu-id="37a89-227">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="37a89-228"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>Představuje kódování obsahu, které `ICompressionProvider` vytváří.</span><span class="sxs-lookup"><span data-stu-id="37a89-228">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="37a89-229">Middleware používá tyto informace k výběru poskytovatele založeného na seznamu zadaném v `Accept-Encoding` hlavičce žádosti.</span><span class="sxs-lookup"><span data-stu-id="37a89-229">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="37a89-230">Pomocí ukázkové aplikace klient odesílá požadavek s `Accept-Encoding: mycustomcompression` hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="37a89-230">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="37a89-231">Middleware používá vlastní kompresní implementaci a vrací odpověď s `Content-Encoding: mycustomcompression` hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="37a89-231">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="37a89-232">Aby mohla vlastní implementace komprese fungovat, musí být klient schopný dekomprimovat vlastní kódování.</span><span class="sxs-lookup"><span data-stu-id="37a89-232">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="37a89-233">Odešlete žádost do ukázkové aplikace s `Accept-Encoding: mycustomcompression` hlavičkou a sledujte hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-233">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="37a89-234">V `Vary` `Content-Encoding` odpovědi jsou k dispozici hlavičky a.</span><span class="sxs-lookup"><span data-stu-id="37a89-234">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="37a89-235">Text odpovědi (nezobrazený) není ukázkou komprimován.</span><span class="sxs-lookup"><span data-stu-id="37a89-235">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="37a89-236">Ve třídě ukázky neexistuje žádná implementace komprese `CustomCompressionProvider` .</span><span class="sxs-lookup"><span data-stu-id="37a89-236">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="37a89-237">Nicméně Ukázka ukazuje, kde byste takový kompresní algoritmus implementovali.</span><span class="sxs-lookup"><span data-stu-id="37a89-237">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou mycustomcompression.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="37a89-240">typy MIME</span><span class="sxs-lookup"><span data-stu-id="37a89-240">MIME types</span></span>

<span data-ttu-id="37a89-241">Middleware určuje výchozí sadu typů MIME pro kompresi:</span><span class="sxs-lookup"><span data-stu-id="37a89-241">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="37a89-242">Nahraďte nebo připojovat typy MIME pomocí možností middlewaru pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="37a89-242">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="37a89-243">Všimněte si, že zástupné typy MIME se `text/*` nepodporují.</span><span class="sxs-lookup"><span data-stu-id="37a89-243">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="37a89-244">Ukázková aplikace přidá typ MIME pro `image/svg+xml` a zkomprimuje a zachová ASP.NET Core obrázek banneru (*banner. SVG*).</span><span class="sxs-lookup"><span data-stu-id="37a89-244">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="37a89-245">Komprese pomocí zabezpečeného protokolu</span><span class="sxs-lookup"><span data-stu-id="37a89-245">Compression with secure protocol</span></span>

<span data-ttu-id="37a89-246">Komprimované odpovědi přes zabezpečená připojení se dají řídit pomocí `EnableForHttps` Možnosti, která je ve výchozím nastavení zakázaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-246">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="37a89-247">Použití komprese s dynamicky generovanými stránkami může vést k problémům se zabezpečením, jako jsou [trestné činy](https://wikipedia.org/wiki/CRIME_(security_exploit)) a útoky za [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) .</span><span class="sxs-lookup"><span data-stu-id="37a89-247">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="37a89-248">Přidávání záhlaví Vary</span><span class="sxs-lookup"><span data-stu-id="37a89-248">Adding the Vary header</span></span>

<span data-ttu-id="37a89-249">Při komprimaci odpovědí na základě `Accept-Encoding` záhlaví může být potenciálně více komprimovaných verzí odpovědi a nekomprimovaná verze.</span><span class="sxs-lookup"><span data-stu-id="37a89-249">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="37a89-250">Aby bylo možné zadat do mezipamětí klienta a proxy serveru, že existuje více verzí a měl by být uložen, `Vary` Hlavička je přidána s `Accept-Encoding` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="37a89-250">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="37a89-251">V ASP.NET Core 2,0 nebo novější middleware přidá `Vary` hlavičku automaticky, když je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-251">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="37a89-252">Problém middlewaru, když za Nginx reverzní proxy</span><span class="sxs-lookup"><span data-stu-id="37a89-252">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="37a89-253">Když požadavek vyNginx proxy serverem, `Accept-Encoding` Hlavička se odebere.</span><span class="sxs-lookup"><span data-stu-id="37a89-253">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="37a89-254">Odebrání `Accept-Encoding` hlavičky brání middlewaru v komprimaci odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-254">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="37a89-255">Další informace naleznete v tématu [Nginx: Compression a decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span><span class="sxs-lookup"><span data-stu-id="37a89-255">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="37a89-256">Tento problém je sledován pomocí [předávací komprese pro Nginx (ASPNET/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span><span class="sxs-lookup"><span data-stu-id="37a89-256">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="37a89-257">Práce s dynamickou kompresí služby IIS</span><span class="sxs-lookup"><span data-stu-id="37a89-257">Working with IIS dynamic compression</span></span>

<span data-ttu-id="37a89-258">Pokud máte aktivní dynamický kompresní modul služby IIS nakonfigurovaný na úrovni serveru, který chcete pro aplikaci zakázat, zakažte modul s přidáním do souboru *web.config* .</span><span class="sxs-lookup"><span data-stu-id="37a89-258">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="37a89-259">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="37a89-259">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="37a89-260">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="37a89-260">Troubleshooting</span></span>

<span data-ttu-id="37a89-261">Použijte nástroj, jako je [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)nebo [post](https://www.getpostman.com/), což vám umožní nastavit `Accept-Encoding` hlavičku požadavku a prozkoumat hlavičky, velikost a text odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-261">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="37a89-262">Ve výchozím nastavení middleware pro komprimaci odezvy komprimuje odezvy, které splňují následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="37a89-262">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="37a89-263">`Accept-Encoding`Hlavička je přítomna s hodnotou `br` , `gzip` , `*` nebo vlastní kódování, které odpovídá vlastnímu poskytovateli komprese, který jste navázali.</span><span class="sxs-lookup"><span data-stu-id="37a89-263">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="37a89-264">Hodnota nesmí být `identity` nebo mít hodnotu kvality (qvalue, `q` ) nastavenou na 0 (nula).</span><span class="sxs-lookup"><span data-stu-id="37a89-264">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="37a89-265">Typ MIME ( `Content-Type` ) musí být nastaven a musí odpovídat typu MIME nakonfigurovanému v <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> .</span><span class="sxs-lookup"><span data-stu-id="37a89-265">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="37a89-266">Požadavek nesmí obsahovat `Content-Range` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="37a89-266">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="37a89-267">Požadavek musí používat protokol HTTP (nezabezpečený protokol), pokud není v možnostech middleware pro kompresi odpovědí nakonfigurovaný protokol HTTPS (Secure Protocol).</span><span class="sxs-lookup"><span data-stu-id="37a89-267">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="37a89-268">*Při povolování komprese zabezpečeného obsahu si všimněte [výše popsaného](#compression-with-secure-protocol) nebezpečí.*</span><span class="sxs-lookup"><span data-stu-id="37a89-268">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37a89-269">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="37a89-269">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="37a89-270">Síť pro vývojáře Mozilla: přijmout – kódování</span><span class="sxs-lookup"><span data-stu-id="37a89-270">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="37a89-271">RFC 7231 část 3.1.2.1: kódování obsahu</span><span class="sxs-lookup"><span data-stu-id="37a89-271">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="37a89-272">RFC 7230 oddíl 4.2.3: kódování gzip</span><span class="sxs-lookup"><span data-stu-id="37a89-272">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="37a89-273">Specifikace formátu souboru GZIP verze 4,3</span><span class="sxs-lookup"><span data-stu-id="37a89-273">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="37a89-274">Šířka pásma sítě je omezeného prostředku.</span><span class="sxs-lookup"><span data-stu-id="37a89-274">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="37a89-275">Zmenšení velikosti odpovědi obvykle zvyšuje rychlost odezvy aplikace, často výrazně.</span><span class="sxs-lookup"><span data-stu-id="37a89-275">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="37a89-276">Jedním ze způsobů, jak omezit velikost datových částí, je komprimace reakcí aplikace.</span><span class="sxs-lookup"><span data-stu-id="37a89-276">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="37a89-277">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37a89-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="37a89-278">Kdy použít middleware pro kompresi odpovědí</span><span class="sxs-lookup"><span data-stu-id="37a89-278">When to use Response Compression Middleware</span></span>

<span data-ttu-id="37a89-279">Používejte technologie pro kompresi odpovědí na základě serveru ve službě IIS, Apache nebo Nginx.</span><span class="sxs-lookup"><span data-stu-id="37a89-279">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="37a89-280">Výkon middleware se pravděpodobně neshoduje s modulem serverových modulů.</span><span class="sxs-lookup"><span data-stu-id="37a89-280">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="37a89-281">Server [HTTP.sys](xref:fundamentals/servers/httpsys) Server a server [Kestrel](xref:fundamentals/servers/kestrel) momentálně nenabízí integrovanou podporu komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="37a89-282">Používejte middleware pro kompresi odpovědí, pokud jste:</span><span class="sxs-lookup"><span data-stu-id="37a89-282">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="37a89-283">Nelze použít následující technologie komprese založené na serveru:</span><span class="sxs-lookup"><span data-stu-id="37a89-283">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="37a89-284">Modul dynamické komprese služby IIS</span><span class="sxs-lookup"><span data-stu-id="37a89-284">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="37a89-285">Modul Apache mod_deflate</span><span class="sxs-lookup"><span data-stu-id="37a89-285">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="37a89-286">Nginx kompresi a dekomprese</span><span class="sxs-lookup"><span data-stu-id="37a89-286">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="37a89-287">Hostování přímo na:</span><span class="sxs-lookup"><span data-stu-id="37a89-287">Hosting directly on:</span></span>
  * <span data-ttu-id="37a89-288">[ServerHTTP.sys](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten)</span><span class="sxs-lookup"><span data-stu-id="37a89-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="37a89-289">Server Kestrel</span><span class="sxs-lookup"><span data-stu-id="37a89-289">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="37a89-290">Komprese odpovědí</span><span class="sxs-lookup"><span data-stu-id="37a89-290">Response compression</span></span>

<span data-ttu-id="37a89-291">Obvykle by jakákoli odpověď, která není nativně komprimovaná, mohla využívat kompresi odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-291">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="37a89-292">Odpovědi, které nejsou nativně zkomprimované, obvykle zahrnují: CSS, JavaScript, HTML, XML a JSON.</span><span class="sxs-lookup"><span data-stu-id="37a89-292">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="37a89-293">Neměli byste komprimovat nativně komprimované prostředky, jako jsou soubory PNG.</span><span class="sxs-lookup"><span data-stu-id="37a89-293">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="37a89-294">Pokusíte-li se dále komprimovat nativně komprimovanou odpověď, jakékoli malé další snížení velikosti a čas přenosu budou pravděpodobně převrženy časem, který trvalo zpracování komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-294">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="37a89-295">Nekomprimuje soubory menší než přibližně 150-1000 bajtů (v závislosti na obsahu souboru a efektivitě komprese).</span><span class="sxs-lookup"><span data-stu-id="37a89-295">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="37a89-296">Režie komprimace malých souborů může vytvořit komprimovaný soubor, který je větší než nekomprimovaný soubor.</span><span class="sxs-lookup"><span data-stu-id="37a89-296">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="37a89-297">Když klient může zpracovat komprimovaný obsah, klient musí informovat Server svých schopností odesláním `Accept-Encoding` hlavičky s požadavkem.</span><span class="sxs-lookup"><span data-stu-id="37a89-297">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="37a89-298">Když server pošle komprimovaný obsah, musí obsahovat informace v `Content-Encoding` záhlaví způsobu, jakým je komprimovaná odpověď zakódovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-298">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="37a89-299">V následující tabulce jsou uvedena označení kódování obsahu podporovaná middlewarem.</span><span class="sxs-lookup"><span data-stu-id="37a89-299">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="37a89-300">`Accept-Encoding`hodnoty hlaviček</span><span class="sxs-lookup"><span data-stu-id="37a89-300">`Accept-Encoding` header values</span></span> | <span data-ttu-id="37a89-301">Middleware – podporováno</span><span class="sxs-lookup"><span data-stu-id="37a89-301">Middleware Supported</span></span> | <span data-ttu-id="37a89-302">Popis</span><span class="sxs-lookup"><span data-stu-id="37a89-302">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="37a89-303">Ano (výchozí)</span><span class="sxs-lookup"><span data-stu-id="37a89-303">Yes (default)</span></span>        | [<span data-ttu-id="37a89-304">Formát komprimovaných dat Brotli</span><span class="sxs-lookup"><span data-stu-id="37a89-304">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="37a89-305">Ne</span><span class="sxs-lookup"><span data-stu-id="37a89-305">No</span></span>                   | [<span data-ttu-id="37a89-306">Formát ZÚŽENé komprese dat</span><span class="sxs-lookup"><span data-stu-id="37a89-306">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="37a89-307">Ne</span><span class="sxs-lookup"><span data-stu-id="37a89-307">No</span></span>                   | [<span data-ttu-id="37a89-308">Efektivní výměna XML pro W3C</span><span class="sxs-lookup"><span data-stu-id="37a89-308">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="37a89-309">Ano</span><span class="sxs-lookup"><span data-stu-id="37a89-309">Yes</span></span>                  | [<span data-ttu-id="37a89-310">Formát souboru gzip</span><span class="sxs-lookup"><span data-stu-id="37a89-310">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="37a89-311">Ano</span><span class="sxs-lookup"><span data-stu-id="37a89-311">Yes</span></span>                  | <span data-ttu-id="37a89-312">Identifikátor "bez kódování": odpověď nesmí být kódovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-312">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="37a89-313">Ne</span><span class="sxs-lookup"><span data-stu-id="37a89-313">No</span></span>                   | [<span data-ttu-id="37a89-314">Formát přenosu v síti pro archivy Java</span><span class="sxs-lookup"><span data-stu-id="37a89-314">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="37a89-315">Ano</span><span class="sxs-lookup"><span data-stu-id="37a89-315">Yes</span></span>                  | <span data-ttu-id="37a89-316">Jakékoli dostupné kódování obsahu není explicitně požadováno.</span><span class="sxs-lookup"><span data-stu-id="37a89-316">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="37a89-317">Další informace najdete v [seznamu kódů oficiálního obsahu IANA](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span><span class="sxs-lookup"><span data-stu-id="37a89-317">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="37a89-318">Middleware umožňuje přidat další zprostředkovatele komprese pro vlastní `Accept-Encoding` hodnoty hlaviček.</span><span class="sxs-lookup"><span data-stu-id="37a89-318">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="37a89-319">Další informace najdete v tématu [Vlastní zprostředkovatelé](#custom-providers) níže.</span><span class="sxs-lookup"><span data-stu-id="37a89-319">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="37a89-320">Middleware je schopná chovat hodnoty kvality (qvalue `q` ), když je klient odesílá k určení priorit schémat komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-320">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="37a89-321">Další informace najdete v [dokumentu RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span><span class="sxs-lookup"><span data-stu-id="37a89-321">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="37a89-322">Algoritmy komprese se vztahují na kompromisy mezi rychlostí komprese a efektivitou komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-322">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="37a89-323">*Efektivita* v tomto kontextu odkazuje na velikost výstupu po kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-323">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="37a89-324">Nejmenší velikost dosáhne *optimální* komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-324">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="37a89-325">Hlavičky zahrnuté v tématu vyžádání, odeslání, ukládání do mezipaměti a příjem komprimovaného obsahu jsou popsány v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="37a89-325">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="37a89-326">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="37a89-326">Header</span></span>             | <span data-ttu-id="37a89-327">Role</span><span class="sxs-lookup"><span data-stu-id="37a89-327">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="37a89-328">Odesílá se z klienta na server k označení schémat kódování obsahu, které jsou přijatelné pro klienta.</span><span class="sxs-lookup"><span data-stu-id="37a89-328">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="37a89-329">Odesílá se ze serveru do klienta, aby označovala kódování obsahu v datové části.</span><span class="sxs-lookup"><span data-stu-id="37a89-329">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="37a89-330">Když dojde k komprimaci, `Content-Length` Hlavička se odebere, protože obsah těla se změní, když je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-330">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="37a89-331">Když dojde k komprimaci, `Content-MD5` Hlavička se odebere, protože se změnil obsah těla a hodnota hash již není platná.</span><span class="sxs-lookup"><span data-stu-id="37a89-331">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="37a89-332">Určuje typ MIME obsahu.</span><span class="sxs-lookup"><span data-stu-id="37a89-332">Specifies the MIME type of the content.</span></span> <span data-ttu-id="37a89-333">Každá odpověď by měla specifikovat svůj `Content-Type` .</span><span class="sxs-lookup"><span data-stu-id="37a89-333">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="37a89-334">Middleware kontroluje tuto hodnotu, aby určila, jestli by měla být komprimovaná odpověď.</span><span class="sxs-lookup"><span data-stu-id="37a89-334">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="37a89-335">Middleware určuje sadu [výchozích typů MIME](#mime-types) , které může kódovat, ale můžete nahradit nebo přidat typy MIME.</span><span class="sxs-lookup"><span data-stu-id="37a89-335">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="37a89-336">Při odeslání serverem s hodnotou `Accept-Encoding` pro klienty a proxy servery `Vary` indikuje hlavička klientovi nebo proxy serveru, že by měl ukládat (Vary) odpovědi na základě hodnoty `Accept-Encoding` záhlaví požadavku.</span><span class="sxs-lookup"><span data-stu-id="37a89-336">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="37a89-337">Výsledkem vrácení obsahu s `Vary: Accept-Encoding` hlavičkou je, že komprimované i nekomprimované odpovědi jsou ukládány do mezipaměti samostatně.</span><span class="sxs-lookup"><span data-stu-id="37a89-337">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="37a89-338">Prozkoumejte funkce middleware pro kompresi odpovědí s [ukázkovou aplikací](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span><span class="sxs-lookup"><span data-stu-id="37a89-338">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="37a89-339">Ukázka znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="37a89-339">The sample illustrates:</span></span>

* <span data-ttu-id="37a89-340">Komprese odpovědí aplikace pomocí gzip a vlastních zprostředkovatelů komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-340">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="37a89-341">Postup přidání typu MIME do výchozího seznamu typů MIME pro kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-341">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="37a89-342">Balíček</span><span class="sxs-lookup"><span data-stu-id="37a89-342">Package</span></span>

<span data-ttu-id="37a89-343">Chcete-li do projektu zahrnout middleware, přidejte odkaz na soubor [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), který obsahuje balíček [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) .</span><span class="sxs-lookup"><span data-stu-id="37a89-343">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="37a89-344">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="37a89-344">Configuration</span></span>

<span data-ttu-id="37a89-345">Následující kód ukazuje, jak povolit middleware pro kompresi odpovědí pro výchozí typy MIME a zprostředkovatele komprese ([Brotli](#brotli-compression-provider) a [gzip](#gzip-compression-provider)):</span><span class="sxs-lookup"><span data-stu-id="37a89-345">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="37a89-346">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="37a89-346">Notes:</span></span>

* <span data-ttu-id="37a89-347">`app.UseResponseCompression`musí být volána před jakýmkoli middlewarem, který komprimuje odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-347">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="37a89-348">Další informace naleznete v tématu <xref:fundamentals/middleware/index#middleware-order>.</span><span class="sxs-lookup"><span data-stu-id="37a89-348">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="37a89-349">Pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)nebo [post](https://www.getpostman.com/) , můžete nastavit `Accept-Encoding` hlavičku požadavku a prozkoumat hlavičky, velikost a text odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-349">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="37a89-350">Odešlete žádost do ukázkové aplikace bez `Accept-Encoding` hlavičky a sledujte, že odpověď je nekomprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-350">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="37a89-351">`Content-Encoding` `Vary` V odpovědi nejsou k dispozici hlavičky a.</span><span class="sxs-lookup"><span data-stu-id="37a89-351">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Okno Fiddler znázorňující výsledek požadavku bez hlavičky Accept-Encoding.](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="37a89-354">Odešlete žádost do ukázkové aplikace s `Accept-Encoding: br` hlavičkou (Brotli Compression) a sledujte, že je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-354">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="37a89-355">V `Content-Encoding` `Vary` odpovědi jsou k dispozici hlavičky a.</span><span class="sxs-lookup"><span data-stu-id="37a89-355">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou br.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="37a89-359">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="37a89-359">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="37a89-360">Brotli kompresní poskytovatel</span><span class="sxs-lookup"><span data-stu-id="37a89-360">Brotli Compression Provider</span></span>

<span data-ttu-id="37a89-361">Použijte <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> pro komprimaci odpovědí s [formátem komprimovaných dat Brotli](https://tools.ietf.org/html/rfc7932).</span><span class="sxs-lookup"><span data-stu-id="37a89-361">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="37a89-362">Pokud nejsou explicitně přidány žádní zprostředkovatelé komprese do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :</span><span class="sxs-lookup"><span data-stu-id="37a89-362">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="37a89-363">Zprostředkovatel komprese Brotli se ve výchozím nastavení přidá k poli zprostředkovatelů komprese společně se [zprostředkovatelem komprese GZip](#gzip-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="37a89-363">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="37a89-364">Komprese se nastaví jako výchozí Brotli komprese, když klient podporuje formát komprimovaných dat Brotli.</span><span class="sxs-lookup"><span data-stu-id="37a89-364">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="37a89-365">Pokud klient nepodporuje Brotli, komprese je standardně nastavená na gzip, když klient podporuje kompresi gzip.</span><span class="sxs-lookup"><span data-stu-id="37a89-365">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="37a89-366">Zprostředkovatel komprese Brotli je nutné přidat, když jsou explicitně přidány poskytovatelé komprese:</span><span class="sxs-lookup"><span data-stu-id="37a89-366">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="37a89-367">Nastavte úroveň komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> .</span><span class="sxs-lookup"><span data-stu-id="37a89-367">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="37a89-368">Zprostředkovatel komprese Brotli je výchozím nastavením nejrychlejší kompresní úrovně ([CompressionLevel. nejrychlejší](xref:System.IO.Compression.CompressionLevel)), což nemusí způsobovat nejúčinnější kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-368">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="37a89-369">Pokud je žádoucí co nejúčinnější komprese, nakonfigurujte middleware pro optimální kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-369">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="37a89-370">Úroveň komprese</span><span class="sxs-lookup"><span data-stu-id="37a89-370">Compression Level</span></span> | <span data-ttu-id="37a89-371">Popis</span><span class="sxs-lookup"><span data-stu-id="37a89-371">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="37a89-372">CompressionLevel. nejrychlejší</span><span class="sxs-lookup"><span data-stu-id="37a89-372">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-373">Komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný výstup není optimálně komprimován.</span><span class="sxs-lookup"><span data-stu-id="37a89-373">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="37a89-374">CompressionLevel. uncompression</span><span class="sxs-lookup"><span data-stu-id="37a89-374">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-375">Není nutné provádět kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-375">No compression should be performed.</span></span> |
| [<span data-ttu-id="37a89-376">CompressionLevel. optimální</span><span class="sxs-lookup"><span data-stu-id="37a89-376">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-377">Odpovědi by se měly optimálně komprimovat, a to i v případě, že je komprese delší dobu trvat.</span><span class="sxs-lookup"><span data-stu-id="37a89-377">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="37a89-378">Zprostředkovatel komprese GZip</span><span class="sxs-lookup"><span data-stu-id="37a89-378">Gzip Compression Provider</span></span>

<span data-ttu-id="37a89-379">Použijte <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> pro komprimaci odpovědí ve [formátu souboru gzip](https://tools.ietf.org/html/rfc1952).</span><span class="sxs-lookup"><span data-stu-id="37a89-379">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="37a89-380">Pokud nejsou explicitně přidány žádní zprostředkovatelé komprese do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :</span><span class="sxs-lookup"><span data-stu-id="37a89-380">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="37a89-381">Zprostředkovatel komprese GZip je ve výchozím nastavení přidán k poli zprostředkovatelů komprese společně s [poskytovatelem Brotli Compression](#brotli-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="37a89-381">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="37a89-382">Komprese se nastaví jako výchozí Brotli komprese, když klient podporuje formát komprimovaných dat Brotli.</span><span class="sxs-lookup"><span data-stu-id="37a89-382">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="37a89-383">Pokud klient nepodporuje Brotli, komprese je standardně nastavená na gzip, když klient podporuje kompresi gzip.</span><span class="sxs-lookup"><span data-stu-id="37a89-383">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="37a89-384">Zprostředkovatel komprese GZip se musí přidat, když se explicitně přidají poskytovatelé komprese:</span><span class="sxs-lookup"><span data-stu-id="37a89-384">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="37a89-385">Nastavte úroveň komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> .</span><span class="sxs-lookup"><span data-stu-id="37a89-385">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="37a89-386">Zprostředkovatel komprese GZip je výchozím nastavením nejrychlejší kompresní úrovně ([CompressionLevel. nejrychlejší](xref:System.IO.Compression.CompressionLevel)), což nemusí způsobovat nejúčinnější kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-386">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="37a89-387">Pokud je žádoucí co nejúčinnější komprese, nakonfigurujte middleware pro optimální kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-387">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="37a89-388">Úroveň komprese</span><span class="sxs-lookup"><span data-stu-id="37a89-388">Compression Level</span></span> | <span data-ttu-id="37a89-389">Popis</span><span class="sxs-lookup"><span data-stu-id="37a89-389">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="37a89-390">CompressionLevel. nejrychlejší</span><span class="sxs-lookup"><span data-stu-id="37a89-390">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-391">Komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný výstup není optimálně komprimován.</span><span class="sxs-lookup"><span data-stu-id="37a89-391">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="37a89-392">CompressionLevel. uncompression</span><span class="sxs-lookup"><span data-stu-id="37a89-392">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-393">Není nutné provádět kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-393">No compression should be performed.</span></span> |
| [<span data-ttu-id="37a89-394">CompressionLevel. optimální</span><span class="sxs-lookup"><span data-stu-id="37a89-394">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-395">Odpovědi by se měly optimálně komprimovat, a to i v případě, že je komprese delší dobu trvat.</span><span class="sxs-lookup"><span data-stu-id="37a89-395">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="37a89-396">Vlastní poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="37a89-396">Custom providers</span></span>

<span data-ttu-id="37a89-397">Vytvořte vlastní implementace komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> .</span><span class="sxs-lookup"><span data-stu-id="37a89-397">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="37a89-398"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>Představuje kódování obsahu, které `ICompressionProvider` vytváří.</span><span class="sxs-lookup"><span data-stu-id="37a89-398">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="37a89-399">Middleware používá tyto informace k výběru poskytovatele založeného na seznamu zadaném v `Accept-Encoding` hlavičce žádosti.</span><span class="sxs-lookup"><span data-stu-id="37a89-399">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="37a89-400">Pomocí ukázkové aplikace klient odesílá požadavek s `Accept-Encoding: mycustomcompression` hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="37a89-400">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="37a89-401">Middleware používá vlastní kompresní implementaci a vrací odpověď s `Content-Encoding: mycustomcompression` hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="37a89-401">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="37a89-402">Aby mohla vlastní implementace komprese fungovat, musí být klient schopný dekomprimovat vlastní kódování.</span><span class="sxs-lookup"><span data-stu-id="37a89-402">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="37a89-403">Odešlete žádost do ukázkové aplikace s `Accept-Encoding: mycustomcompression` hlavičkou a sledujte hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-403">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="37a89-404">V `Vary` `Content-Encoding` odpovědi jsou k dispozici hlavičky a.</span><span class="sxs-lookup"><span data-stu-id="37a89-404">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="37a89-405">Text odpovědi (nezobrazený) není ukázkou komprimován.</span><span class="sxs-lookup"><span data-stu-id="37a89-405">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="37a89-406">Ve třídě ukázky neexistuje žádná implementace komprese `CustomCompressionProvider` .</span><span class="sxs-lookup"><span data-stu-id="37a89-406">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="37a89-407">Nicméně Ukázka ukazuje, kde byste takový kompresní algoritmus implementovali.</span><span class="sxs-lookup"><span data-stu-id="37a89-407">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou mycustomcompression.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="37a89-410">typy MIME</span><span class="sxs-lookup"><span data-stu-id="37a89-410">MIME types</span></span>

<span data-ttu-id="37a89-411">Middleware určuje výchozí sadu typů MIME pro kompresi:</span><span class="sxs-lookup"><span data-stu-id="37a89-411">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="37a89-412">Nahraďte nebo připojovat typy MIME pomocí možností middlewaru pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="37a89-412">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="37a89-413">Všimněte si, že zástupné typy MIME se `text/*` nepodporují.</span><span class="sxs-lookup"><span data-stu-id="37a89-413">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="37a89-414">Ukázková aplikace přidá typ MIME pro `image/svg+xml` a zkomprimuje a zachová ASP.NET Core obrázek banneru (*banner. SVG*).</span><span class="sxs-lookup"><span data-stu-id="37a89-414">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="37a89-415">Komprese pomocí zabezpečeného protokolu</span><span class="sxs-lookup"><span data-stu-id="37a89-415">Compression with secure protocol</span></span>

<span data-ttu-id="37a89-416">Komprimované odpovědi přes zabezpečená připojení se dají řídit pomocí `EnableForHttps` Možnosti, která je ve výchozím nastavení zakázaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-416">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="37a89-417">Použití komprese s dynamicky generovanými stránkami může vést k problémům se zabezpečením, jako jsou [trestné činy](https://wikipedia.org/wiki/CRIME_(security_exploit)) a útoky za [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) .</span><span class="sxs-lookup"><span data-stu-id="37a89-417">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="37a89-418">Přidávání záhlaví Vary</span><span class="sxs-lookup"><span data-stu-id="37a89-418">Adding the Vary header</span></span>

<span data-ttu-id="37a89-419">Při komprimaci odpovědí na základě `Accept-Encoding` záhlaví může být potenciálně více komprimovaných verzí odpovědi a nekomprimovaná verze.</span><span class="sxs-lookup"><span data-stu-id="37a89-419">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="37a89-420">Aby bylo možné zadat do mezipamětí klienta a proxy serveru, že existuje více verzí a měl by být uložen, `Vary` Hlavička je přidána s `Accept-Encoding` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="37a89-420">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="37a89-421">V ASP.NET Core 2,0 nebo novější middleware přidá `Vary` hlavičku automaticky, když je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-421">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="37a89-422">Problém middlewaru, když za Nginx reverzní proxy</span><span class="sxs-lookup"><span data-stu-id="37a89-422">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="37a89-423">Když požadavek vyNginx proxy serverem, `Accept-Encoding` Hlavička se odebere.</span><span class="sxs-lookup"><span data-stu-id="37a89-423">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="37a89-424">Odebrání `Accept-Encoding` hlavičky brání middlewaru v komprimaci odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-424">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="37a89-425">Další informace naleznete v tématu [Nginx: Compression a decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span><span class="sxs-lookup"><span data-stu-id="37a89-425">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="37a89-426">Tento problém je sledován pomocí [předávací komprese pro Nginx (ASPNET/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span><span class="sxs-lookup"><span data-stu-id="37a89-426">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="37a89-427">Práce s dynamickou kompresí služby IIS</span><span class="sxs-lookup"><span data-stu-id="37a89-427">Working with IIS dynamic compression</span></span>

<span data-ttu-id="37a89-428">Pokud máte aktivní dynamický kompresní modul služby IIS nakonfigurovaný na úrovni serveru, který chcete pro aplikaci zakázat, zakažte modul s přidáním do souboru *web.config* .</span><span class="sxs-lookup"><span data-stu-id="37a89-428">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="37a89-429">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="37a89-429">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="37a89-430">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="37a89-430">Troubleshooting</span></span>

<span data-ttu-id="37a89-431">Použijte nástroj, jako je [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)nebo [post](https://www.getpostman.com/), což vám umožní nastavit `Accept-Encoding` hlavičku požadavku a prozkoumat hlavičky, velikost a text odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-431">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="37a89-432">Ve výchozím nastavení middleware pro komprimaci odezvy komprimuje odezvy, které splňují následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="37a89-432">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="37a89-433">`Accept-Encoding`Hlavička je přítomna s hodnotou `br` , `gzip` , `*` nebo vlastní kódování, které odpovídá vlastnímu poskytovateli komprese, který jste navázali.</span><span class="sxs-lookup"><span data-stu-id="37a89-433">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="37a89-434">Hodnota nesmí být `identity` nebo mít hodnotu kvality (qvalue, `q` ) nastavenou na 0 (nula).</span><span class="sxs-lookup"><span data-stu-id="37a89-434">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="37a89-435">Typ MIME ( `Content-Type` ) musí být nastaven a musí odpovídat typu MIME nakonfigurovanému v <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> .</span><span class="sxs-lookup"><span data-stu-id="37a89-435">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="37a89-436">Požadavek nesmí obsahovat `Content-Range` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="37a89-436">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="37a89-437">Požadavek musí používat protokol HTTP (nezabezpečený protokol), pokud není v možnostech middleware pro kompresi odpovědí nakonfigurovaný protokol HTTPS (Secure Protocol).</span><span class="sxs-lookup"><span data-stu-id="37a89-437">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="37a89-438">*Při povolování komprese zabezpečeného obsahu si všimněte [výše popsaného](#compression-with-secure-protocol) nebezpečí.*</span><span class="sxs-lookup"><span data-stu-id="37a89-438">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37a89-439">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="37a89-439">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="37a89-440">Síť pro vývojáře Mozilla: přijmout – kódování</span><span class="sxs-lookup"><span data-stu-id="37a89-440">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="37a89-441">RFC 7231 část 3.1.2.1: kódování obsahu</span><span class="sxs-lookup"><span data-stu-id="37a89-441">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="37a89-442">RFC 7230 oddíl 4.2.3: kódování gzip</span><span class="sxs-lookup"><span data-stu-id="37a89-442">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="37a89-443">Specifikace formátu souboru GZIP verze 4,3</span><span class="sxs-lookup"><span data-stu-id="37a89-443">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="37a89-444">Šířka pásma sítě je omezeného prostředku.</span><span class="sxs-lookup"><span data-stu-id="37a89-444">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="37a89-445">Zmenšení velikosti odpovědi obvykle zvyšuje rychlost odezvy aplikace, často výrazně.</span><span class="sxs-lookup"><span data-stu-id="37a89-445">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="37a89-446">Jedním ze způsobů, jak omezit velikost datových částí, je komprimace reakcí aplikace.</span><span class="sxs-lookup"><span data-stu-id="37a89-446">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="37a89-447">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37a89-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="37a89-448">Kdy použít middleware pro kompresi odpovědí</span><span class="sxs-lookup"><span data-stu-id="37a89-448">When to use Response Compression Middleware</span></span>

<span data-ttu-id="37a89-449">Používejte technologie pro kompresi odpovědí na základě serveru ve službě IIS, Apache nebo Nginx.</span><span class="sxs-lookup"><span data-stu-id="37a89-449">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="37a89-450">Výkon middleware se pravděpodobně neshoduje s modulem serverových modulů.</span><span class="sxs-lookup"><span data-stu-id="37a89-450">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="37a89-451">Server [HTTP.sys](xref:fundamentals/servers/httpsys) Server a server [Kestrel](xref:fundamentals/servers/kestrel) momentálně nenabízí integrovanou podporu komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="37a89-452">Používejte middleware pro kompresi odpovědí, pokud jste:</span><span class="sxs-lookup"><span data-stu-id="37a89-452">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="37a89-453">Nelze použít následující technologie komprese založené na serveru:</span><span class="sxs-lookup"><span data-stu-id="37a89-453">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="37a89-454">Modul dynamické komprese služby IIS</span><span class="sxs-lookup"><span data-stu-id="37a89-454">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="37a89-455">Modul Apache mod_deflate</span><span class="sxs-lookup"><span data-stu-id="37a89-455">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="37a89-456">Nginx kompresi a dekomprese</span><span class="sxs-lookup"><span data-stu-id="37a89-456">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="37a89-457">Hostování přímo na:</span><span class="sxs-lookup"><span data-stu-id="37a89-457">Hosting directly on:</span></span>
  * <span data-ttu-id="37a89-458">[ServerHTTP.sys](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten)</span><span class="sxs-lookup"><span data-stu-id="37a89-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="37a89-459">Server Kestrel</span><span class="sxs-lookup"><span data-stu-id="37a89-459">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="37a89-460">Komprese odpovědí</span><span class="sxs-lookup"><span data-stu-id="37a89-460">Response compression</span></span>

<span data-ttu-id="37a89-461">Obvykle by jakákoli odpověď, která není nativně komprimovaná, mohla využívat kompresi odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-461">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="37a89-462">Odpovědi, které nejsou nativně zkomprimované, obvykle zahrnují: CSS, JavaScript, HTML, XML a JSON.</span><span class="sxs-lookup"><span data-stu-id="37a89-462">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="37a89-463">Neměli byste komprimovat nativně komprimované prostředky, jako jsou soubory PNG.</span><span class="sxs-lookup"><span data-stu-id="37a89-463">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="37a89-464">Pokusíte-li se dále komprimovat nativně komprimovanou odpověď, jakékoli malé další snížení velikosti a čas přenosu budou pravděpodobně převrženy časem, který trvalo zpracování komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-464">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="37a89-465">Nekomprimuje soubory menší než přibližně 150-1000 bajtů (v závislosti na obsahu souboru a efektivitě komprese).</span><span class="sxs-lookup"><span data-stu-id="37a89-465">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="37a89-466">Režie komprimace malých souborů může vytvořit komprimovaný soubor, který je větší než nekomprimovaný soubor.</span><span class="sxs-lookup"><span data-stu-id="37a89-466">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="37a89-467">Když klient může zpracovat komprimovaný obsah, klient musí informovat Server svých schopností odesláním `Accept-Encoding` hlavičky s požadavkem.</span><span class="sxs-lookup"><span data-stu-id="37a89-467">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="37a89-468">Když server pošle komprimovaný obsah, musí obsahovat informace v `Content-Encoding` záhlaví způsobu, jakým je komprimovaná odpověď zakódovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-468">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="37a89-469">V následující tabulce jsou uvedena označení kódování obsahu podporovaná middlewarem.</span><span class="sxs-lookup"><span data-stu-id="37a89-469">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="37a89-470">`Accept-Encoding`hodnoty hlaviček</span><span class="sxs-lookup"><span data-stu-id="37a89-470">`Accept-Encoding` header values</span></span> | <span data-ttu-id="37a89-471">Middleware – podporováno</span><span class="sxs-lookup"><span data-stu-id="37a89-471">Middleware Supported</span></span> | <span data-ttu-id="37a89-472">Popis</span><span class="sxs-lookup"><span data-stu-id="37a89-472">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="37a89-473">Ne</span><span class="sxs-lookup"><span data-stu-id="37a89-473">No</span></span>                   | [<span data-ttu-id="37a89-474">Formát komprimovaných dat Brotli</span><span class="sxs-lookup"><span data-stu-id="37a89-474">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="37a89-475">Ne</span><span class="sxs-lookup"><span data-stu-id="37a89-475">No</span></span>                   | [<span data-ttu-id="37a89-476">Formát ZÚŽENé komprese dat</span><span class="sxs-lookup"><span data-stu-id="37a89-476">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="37a89-477">Ne</span><span class="sxs-lookup"><span data-stu-id="37a89-477">No</span></span>                   | [<span data-ttu-id="37a89-478">Efektivní výměna XML pro W3C</span><span class="sxs-lookup"><span data-stu-id="37a89-478">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="37a89-479">Ano (výchozí)</span><span class="sxs-lookup"><span data-stu-id="37a89-479">Yes (default)</span></span>        | [<span data-ttu-id="37a89-480">Formát souboru gzip</span><span class="sxs-lookup"><span data-stu-id="37a89-480">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="37a89-481">Ano</span><span class="sxs-lookup"><span data-stu-id="37a89-481">Yes</span></span>                  | <span data-ttu-id="37a89-482">Identifikátor "bez kódování": odpověď nesmí být kódovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-482">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="37a89-483">Ne</span><span class="sxs-lookup"><span data-stu-id="37a89-483">No</span></span>                   | [<span data-ttu-id="37a89-484">Formát přenosu v síti pro archivy Java</span><span class="sxs-lookup"><span data-stu-id="37a89-484">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="37a89-485">Ano</span><span class="sxs-lookup"><span data-stu-id="37a89-485">Yes</span></span>                  | <span data-ttu-id="37a89-486">Jakékoli dostupné kódování obsahu není explicitně požadováno.</span><span class="sxs-lookup"><span data-stu-id="37a89-486">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="37a89-487">Další informace najdete v [seznamu kódů oficiálního obsahu IANA](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span><span class="sxs-lookup"><span data-stu-id="37a89-487">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="37a89-488">Middleware umožňuje přidat další zprostředkovatele komprese pro vlastní `Accept-Encoding` hodnoty hlaviček.</span><span class="sxs-lookup"><span data-stu-id="37a89-488">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="37a89-489">Další informace najdete v tématu [Vlastní zprostředkovatelé](#custom-providers) níže.</span><span class="sxs-lookup"><span data-stu-id="37a89-489">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="37a89-490">Middleware je schopná chovat hodnoty kvality (qvalue `q` ), když je klient odesílá k určení priorit schémat komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-490">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="37a89-491">Další informace najdete v [dokumentu RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span><span class="sxs-lookup"><span data-stu-id="37a89-491">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="37a89-492">Algoritmy komprese se vztahují na kompromisy mezi rychlostí komprese a efektivitou komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-492">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="37a89-493">*Efektivita* v tomto kontextu odkazuje na velikost výstupu po kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-493">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="37a89-494">Nejmenší velikost dosáhne *optimální* komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-494">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="37a89-495">Hlavičky zahrnuté v tématu vyžádání, odeslání, ukládání do mezipaměti a příjem komprimovaného obsahu jsou popsány v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="37a89-495">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="37a89-496">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="37a89-496">Header</span></span>             | <span data-ttu-id="37a89-497">Role</span><span class="sxs-lookup"><span data-stu-id="37a89-497">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="37a89-498">Odesílá se z klienta na server k označení schémat kódování obsahu, které jsou přijatelné pro klienta.</span><span class="sxs-lookup"><span data-stu-id="37a89-498">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="37a89-499">Odesílá se ze serveru do klienta, aby označovala kódování obsahu v datové části.</span><span class="sxs-lookup"><span data-stu-id="37a89-499">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="37a89-500">Když dojde k komprimaci, `Content-Length` Hlavička se odebere, protože obsah těla se změní, když je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-500">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="37a89-501">Když dojde k komprimaci, `Content-MD5` Hlavička se odebere, protože se změnil obsah těla a hodnota hash již není platná.</span><span class="sxs-lookup"><span data-stu-id="37a89-501">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="37a89-502">Určuje typ MIME obsahu.</span><span class="sxs-lookup"><span data-stu-id="37a89-502">Specifies the MIME type of the content.</span></span> <span data-ttu-id="37a89-503">Každá odpověď by měla specifikovat svůj `Content-Type` .</span><span class="sxs-lookup"><span data-stu-id="37a89-503">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="37a89-504">Middleware kontroluje tuto hodnotu, aby určila, jestli by měla být komprimovaná odpověď.</span><span class="sxs-lookup"><span data-stu-id="37a89-504">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="37a89-505">Middleware určuje sadu [výchozích typů MIME](#mime-types) , které může kódovat, ale můžete nahradit nebo přidat typy MIME.</span><span class="sxs-lookup"><span data-stu-id="37a89-505">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="37a89-506">Při odeslání serverem s hodnotou `Accept-Encoding` pro klienty a proxy servery `Vary` indikuje hlavička klientovi nebo proxy serveru, že by měl ukládat (Vary) odpovědi na základě hodnoty `Accept-Encoding` záhlaví požadavku.</span><span class="sxs-lookup"><span data-stu-id="37a89-506">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="37a89-507">Výsledkem vrácení obsahu s `Vary: Accept-Encoding` hlavičkou je, že komprimované i nekomprimované odpovědi jsou ukládány do mezipaměti samostatně.</span><span class="sxs-lookup"><span data-stu-id="37a89-507">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="37a89-508">Prozkoumejte funkce middleware pro kompresi odpovědí s [ukázkovou aplikací](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span><span class="sxs-lookup"><span data-stu-id="37a89-508">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="37a89-509">Ukázka znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="37a89-509">The sample illustrates:</span></span>

* <span data-ttu-id="37a89-510">Komprese odpovědí aplikace pomocí gzip a vlastních zprostředkovatelů komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-510">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="37a89-511">Postup přidání typu MIME do výchozího seznamu typů MIME pro kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-511">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="37a89-512">Balíček</span><span class="sxs-lookup"><span data-stu-id="37a89-512">Package</span></span>

<span data-ttu-id="37a89-513">Chcete-li do projektu zahrnout middleware, přidejte odkaz na soubor [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), který obsahuje balíček [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) .</span><span class="sxs-lookup"><span data-stu-id="37a89-513">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="37a89-514">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="37a89-514">Configuration</span></span>

<span data-ttu-id="37a89-515">Následující kód ukazuje, jak povolit middleware pro kompresi odpovědí pro výchozí typy MIME a [zprostředkovatele komprese GZip](#gzip-compression-provider):</span><span class="sxs-lookup"><span data-stu-id="37a89-515">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="37a89-516">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="37a89-516">Notes:</span></span>

* <span data-ttu-id="37a89-517">`app.UseResponseCompression`musí být volána před jakýmkoli middlewarem, který komprimuje odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-517">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="37a89-518">Další informace naleznete v tématu <xref:fundamentals/middleware/index#middleware-order>.</span><span class="sxs-lookup"><span data-stu-id="37a89-518">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="37a89-519">Pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)nebo [post](https://www.getpostman.com/) , můžete nastavit `Accept-Encoding` hlavičku požadavku a prozkoumat hlavičky, velikost a text odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-519">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="37a89-520">Odešlete žádost do ukázkové aplikace bez `Accept-Encoding` hlavičky a sledujte, že odpověď je nekomprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-520">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="37a89-521">`Content-Encoding` `Vary` V odpovědi nejsou k dispozici hlavičky a.</span><span class="sxs-lookup"><span data-stu-id="37a89-521">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Okno Fiddler znázorňující výsledek požadavku bez hlavičky Accept-Encoding.](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="37a89-524">Odešlete žádost do ukázkové aplikace s `Accept-Encoding: gzip` hlavičkou a sledujte, že je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-524">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="37a89-525">V `Content-Encoding` `Vary` odpovědi jsou k dispozici hlavičky a.</span><span class="sxs-lookup"><span data-stu-id="37a89-525">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou gzip.](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="37a89-529">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="37a89-529">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="37a89-530">Zprostředkovatel komprese GZip</span><span class="sxs-lookup"><span data-stu-id="37a89-530">Gzip Compression Provider</span></span>

<span data-ttu-id="37a89-531">Použijte <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> pro komprimaci odpovědí ve [formátu souboru gzip](https://tools.ietf.org/html/rfc1952).</span><span class="sxs-lookup"><span data-stu-id="37a89-531">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="37a89-532">Pokud nejsou explicitně přidány žádní zprostředkovatelé komprese do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :</span><span class="sxs-lookup"><span data-stu-id="37a89-532">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="37a89-533">Zprostředkovatel komprese GZip je ve výchozím nastavení přidán k poli zprostředkovatelů komprese.</span><span class="sxs-lookup"><span data-stu-id="37a89-533">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="37a89-534">Komprese je výchozím nastavením pro nástroj gzip, pokud klient podporuje kompresi gzip.</span><span class="sxs-lookup"><span data-stu-id="37a89-534">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="37a89-535">Zprostředkovatel komprese GZip se musí přidat, když se explicitně přidají poskytovatelé komprese:</span><span class="sxs-lookup"><span data-stu-id="37a89-535">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="37a89-536">Nastavte úroveň komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> .</span><span class="sxs-lookup"><span data-stu-id="37a89-536">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="37a89-537">Zprostředkovatel komprese GZip je výchozím nastavením nejrychlejší kompresní úrovně ([CompressionLevel. nejrychlejší](xref:System.IO.Compression.CompressionLevel)), což nemusí způsobovat nejúčinnější kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-537">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="37a89-538">Pokud je žádoucí co nejúčinnější komprese, nakonfigurujte middleware pro optimální kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-538">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="37a89-539">Úroveň komprese</span><span class="sxs-lookup"><span data-stu-id="37a89-539">Compression Level</span></span> | <span data-ttu-id="37a89-540">Popis</span><span class="sxs-lookup"><span data-stu-id="37a89-540">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="37a89-541">CompressionLevel. nejrychlejší</span><span class="sxs-lookup"><span data-stu-id="37a89-541">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-542">Komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný výstup není optimálně komprimován.</span><span class="sxs-lookup"><span data-stu-id="37a89-542">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="37a89-543">CompressionLevel. uncompression</span><span class="sxs-lookup"><span data-stu-id="37a89-543">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-544">Není nutné provádět kompresi.</span><span class="sxs-lookup"><span data-stu-id="37a89-544">No compression should be performed.</span></span> |
| [<span data-ttu-id="37a89-545">CompressionLevel. optimální</span><span class="sxs-lookup"><span data-stu-id="37a89-545">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="37a89-546">Odpovědi by se měly optimálně komprimovat, a to i v případě, že je komprese delší dobu trvat.</span><span class="sxs-lookup"><span data-stu-id="37a89-546">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="37a89-547">Vlastní poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="37a89-547">Custom providers</span></span>

<span data-ttu-id="37a89-548">Vytvořte vlastní implementace komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> .</span><span class="sxs-lookup"><span data-stu-id="37a89-548">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="37a89-549"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>Představuje kódování obsahu, které `ICompressionProvider` vytváří.</span><span class="sxs-lookup"><span data-stu-id="37a89-549">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="37a89-550">Middleware používá tyto informace k výběru poskytovatele založeného na seznamu zadaném v `Accept-Encoding` hlavičce žádosti.</span><span class="sxs-lookup"><span data-stu-id="37a89-550">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="37a89-551">Pomocí ukázkové aplikace klient odesílá požadavek s `Accept-Encoding: mycustomcompression` hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="37a89-551">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="37a89-552">Middleware používá vlastní kompresní implementaci a vrací odpověď s `Content-Encoding: mycustomcompression` hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="37a89-552">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="37a89-553">Aby mohla vlastní implementace komprese fungovat, musí být klient schopný dekomprimovat vlastní kódování.</span><span class="sxs-lookup"><span data-stu-id="37a89-553">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="37a89-554">Odešlete žádost do ukázkové aplikace s `Accept-Encoding: mycustomcompression` hlavičkou a sledujte hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-554">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="37a89-555">V `Vary` `Content-Encoding` odpovědi jsou k dispozici hlavičky a.</span><span class="sxs-lookup"><span data-stu-id="37a89-555">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="37a89-556">Text odpovědi (nezobrazený) není ukázkou komprimován.</span><span class="sxs-lookup"><span data-stu-id="37a89-556">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="37a89-557">Ve třídě ukázky neexistuje žádná implementace komprese `CustomCompressionProvider` .</span><span class="sxs-lookup"><span data-stu-id="37a89-557">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="37a89-558">Nicméně Ukázka ukazuje, kde byste takový kompresní algoritmus implementovali.</span><span class="sxs-lookup"><span data-stu-id="37a89-558">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou mycustomcompression.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="37a89-561">typy MIME</span><span class="sxs-lookup"><span data-stu-id="37a89-561">MIME types</span></span>

<span data-ttu-id="37a89-562">Middleware určuje výchozí sadu typů MIME pro kompresi:</span><span class="sxs-lookup"><span data-stu-id="37a89-562">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="37a89-563">Nahraďte nebo připojovat typy MIME pomocí možností middlewaru pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="37a89-563">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="37a89-564">Všimněte si, že zástupné typy MIME se `text/*` nepodporují.</span><span class="sxs-lookup"><span data-stu-id="37a89-564">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="37a89-565">Ukázková aplikace přidá typ MIME pro `image/svg+xml` a zkomprimuje a zachová ASP.NET Core obrázek banneru (*banner. SVG*).</span><span class="sxs-lookup"><span data-stu-id="37a89-565">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="37a89-566">Komprese pomocí zabezpečeného protokolu</span><span class="sxs-lookup"><span data-stu-id="37a89-566">Compression with secure protocol</span></span>

<span data-ttu-id="37a89-567">Komprimované odpovědi přes zabezpečená připojení se dají řídit pomocí `EnableForHttps` Možnosti, která je ve výchozím nastavení zakázaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-567">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="37a89-568">Použití komprese s dynamicky generovanými stránkami může vést k problémům se zabezpečením, jako jsou [trestné činy](https://wikipedia.org/wiki/CRIME_(security_exploit)) a útoky za [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) .</span><span class="sxs-lookup"><span data-stu-id="37a89-568">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="37a89-569">Přidávání záhlaví Vary</span><span class="sxs-lookup"><span data-stu-id="37a89-569">Adding the Vary header</span></span>

<span data-ttu-id="37a89-570">Při komprimaci odpovědí na základě `Accept-Encoding` záhlaví může být potenciálně více komprimovaných verzí odpovědi a nekomprimovaná verze.</span><span class="sxs-lookup"><span data-stu-id="37a89-570">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="37a89-571">Aby bylo možné zadat do mezipamětí klienta a proxy serveru, že existuje více verzí a měl by být uložen, `Vary` Hlavička je přidána s `Accept-Encoding` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="37a89-571">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="37a89-572">V ASP.NET Core 2,0 nebo novější middleware přidá `Vary` hlavičku automaticky, když je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="37a89-572">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="37a89-573">Problém middlewaru, když za Nginx reverzní proxy</span><span class="sxs-lookup"><span data-stu-id="37a89-573">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="37a89-574">Když požadavek vyNginx proxy serverem, `Accept-Encoding` Hlavička se odebere.</span><span class="sxs-lookup"><span data-stu-id="37a89-574">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="37a89-575">Odebrání `Accept-Encoding` hlavičky brání middlewaru v komprimaci odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-575">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="37a89-576">Další informace naleznete v tématu [Nginx: Compression a decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span><span class="sxs-lookup"><span data-stu-id="37a89-576">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="37a89-577">Tento problém je sledován pomocí [předávací komprese pro Nginx (ASPNET/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span><span class="sxs-lookup"><span data-stu-id="37a89-577">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="37a89-578">Práce s dynamickou kompresí služby IIS</span><span class="sxs-lookup"><span data-stu-id="37a89-578">Working with IIS dynamic compression</span></span>

<span data-ttu-id="37a89-579">Pokud máte aktivní dynamický kompresní modul služby IIS nakonfigurovaný na úrovni serveru, který chcete pro aplikaci zakázat, zakažte modul s přidáním do souboru *web.config* .</span><span class="sxs-lookup"><span data-stu-id="37a89-579">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="37a89-580">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="37a89-580">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="37a89-581">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="37a89-581">Troubleshooting</span></span>

<span data-ttu-id="37a89-582">Použijte nástroj, jako je [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)nebo [post](https://www.getpostman.com/), což vám umožní nastavit `Accept-Encoding` hlavičku požadavku a prozkoumat hlavičky, velikost a text odpovědi.</span><span class="sxs-lookup"><span data-stu-id="37a89-582">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="37a89-583">Ve výchozím nastavení middleware pro komprimaci odezvy komprimuje odezvy, které splňují následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="37a89-583">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="37a89-584">`Accept-Encoding`Hlavička je přítomna s hodnotou `gzip` , `*` nebo vlastní kódování, které se shoduje s vlastním poskytovatelem komprese, který jste navázali.</span><span class="sxs-lookup"><span data-stu-id="37a89-584">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="37a89-585">Hodnota nesmí být `identity` nebo mít hodnotu kvality (qvalue, `q` ) nastavenou na 0 (nula).</span><span class="sxs-lookup"><span data-stu-id="37a89-585">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="37a89-586">Typ MIME ( `Content-Type` ) musí být nastaven a musí odpovídat typu MIME nakonfigurovanému v <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> .</span><span class="sxs-lookup"><span data-stu-id="37a89-586">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="37a89-587">Požadavek nesmí obsahovat `Content-Range` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="37a89-587">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="37a89-588">Požadavek musí používat protokol HTTP (nezabezpečený protokol), pokud není v možnostech middleware pro kompresi odpovědí nakonfigurovaný protokol HTTPS (Secure Protocol).</span><span class="sxs-lookup"><span data-stu-id="37a89-588">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="37a89-589">*Při povolování komprese zabezpečeného obsahu si všimněte [výše popsaného](#compression-with-secure-protocol) nebezpečí.*</span><span class="sxs-lookup"><span data-stu-id="37a89-589">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37a89-590">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="37a89-590">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="37a89-591">Síť pro vývojáře Mozilla: přijmout – kódování</span><span class="sxs-lookup"><span data-stu-id="37a89-591">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="37a89-592">RFC 7231 část 3.1.2.1: kódování obsahu</span><span class="sxs-lookup"><span data-stu-id="37a89-592">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="37a89-593">RFC 7230 oddíl 4.2.3: kódování gzip</span><span class="sxs-lookup"><span data-stu-id="37a89-593">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="37a89-594">Specifikace formátu souboru GZIP verze 4,3</span><span class="sxs-lookup"><span data-stu-id="37a89-594">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
