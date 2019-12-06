---
title: Komprese odezvy v ASP.NET Core
author: guardrex
description: Přečtěte si o kompresi odpovědí a způsobu použití middleware pro kompresi odpovědí v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: performance/response-compression
ms.openlocfilehash: 04b2ffd7047e8b127968adb5d40e0141365fb5fe
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880913"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="672f5-103">Komprese odezvy v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="672f5-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="672f5-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="672f5-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="672f5-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="672f5-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="672f5-106">Šířka pásma sítě je omezeného prostředku.</span><span class="sxs-lookup"><span data-stu-id="672f5-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="672f5-107">Odezvu aplikace obvykle zlepšuje zmenšení velikosti odpovědi, a to často výrazně.</span><span class="sxs-lookup"><span data-stu-id="672f5-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="672f5-108">Jedním ze způsobů zmenšení velikosti datové části je komprese odpovědí vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="672f5-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="672f5-109">Kdy použít middleware pro kompresi odpovědí</span><span class="sxs-lookup"><span data-stu-id="672f5-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="672f5-110">Používejte technologie pro kompresi odpovědí na základě serveru ve službě IIS, Apache nebo Nginx.</span><span class="sxs-lookup"><span data-stu-id="672f5-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="672f5-111">Výkon middleware se pravděpodobně neshoduje s modulem serverových modulů.</span><span class="sxs-lookup"><span data-stu-id="672f5-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="672f5-112">Server [http. sys](xref:fundamentals/servers/httpsys) Server a server [Kestrel](xref:fundamentals/servers/kestrel) momentálně nenabízí integrovanou podporu komprese.</span><span class="sxs-lookup"><span data-stu-id="672f5-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="672f5-113">Používejte middleware pro kompresi odpovědí, pokud jste:</span><span class="sxs-lookup"><span data-stu-id="672f5-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="672f5-114">Nelze použít následující technologie komprese založené na serveru:</span><span class="sxs-lookup"><span data-stu-id="672f5-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="672f5-115">Modul dynamické komprese služby IIS</span><span class="sxs-lookup"><span data-stu-id="672f5-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="672f5-116">Modul Apache mod_deflate</span><span class="sxs-lookup"><span data-stu-id="672f5-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="672f5-117">Nginx kompresi a dekomprese</span><span class="sxs-lookup"><span data-stu-id="672f5-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="672f5-118">Hostování přímo na:</span><span class="sxs-lookup"><span data-stu-id="672f5-118">Hosting directly on:</span></span>
  * <span data-ttu-id="672f5-119">[Http. sys Server](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten)</span><span class="sxs-lookup"><span data-stu-id="672f5-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="672f5-120">Server Kestrel</span><span class="sxs-lookup"><span data-stu-id="672f5-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="672f5-121">Komprese odpovědí</span><span class="sxs-lookup"><span data-stu-id="672f5-121">Response compression</span></span>

<span data-ttu-id="672f5-122">Obvykle by jakákoli odpověď, která není nativně komprimovaná, mohla využívat kompresi odpovědi.</span><span class="sxs-lookup"><span data-stu-id="672f5-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="672f5-123">Odpovědi, které nejsou nativně zkomprimované, obvykle zahrnují: CSS, JavaScript, HTML, XML a JSON.</span><span class="sxs-lookup"><span data-stu-id="672f5-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="672f5-124">Neměli byste komprimovat nativně komprimované prostředky, jako jsou soubory PNG.</span><span class="sxs-lookup"><span data-stu-id="672f5-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="672f5-125">Pokusíte-li se dále komprimovat nativně komprimovanou odpověď, jakékoli malé další snížení velikosti a čas přenosu budou pravděpodobně převrženy časem, který trvalo zpracování komprese.</span><span class="sxs-lookup"><span data-stu-id="672f5-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="672f5-126">Nekomprimuje soubory menší než přibližně 150-1000 bajtů (v závislosti na obsahu souboru a efektivitě komprese).</span><span class="sxs-lookup"><span data-stu-id="672f5-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="672f5-127">Režie komprimace malých souborů může vytvořit komprimovaný soubor, který je větší než nekomprimovaný soubor.</span><span class="sxs-lookup"><span data-stu-id="672f5-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="672f5-128">Když klient může zpracovat komprimovaný obsah, klient musí informovat Server svých schopností odesláním hlavičky `Accept-Encoding` s požadavkem.</span><span class="sxs-lookup"><span data-stu-id="672f5-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="672f5-129">Když server pošle komprimovaný obsah, musí obsahovat informace v hlavičce `Content-Encoding`, jak je komprimovaná odpověď zakódovaná.</span><span class="sxs-lookup"><span data-stu-id="672f5-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="672f5-130">V následující tabulce jsou uvedena označení kódování obsahu podporovaná middlewarem.</span><span class="sxs-lookup"><span data-stu-id="672f5-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="672f5-131">`Accept-Encoding` hodnoty hlaviček</span><span class="sxs-lookup"><span data-stu-id="672f5-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="672f5-132">Middleware – podporováno</span><span class="sxs-lookup"><span data-stu-id="672f5-132">Middleware Supported</span></span> | <span data-ttu-id="672f5-133">Popis</span><span class="sxs-lookup"><span data-stu-id="672f5-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="672f5-134">Ano (výchozí)</span><span class="sxs-lookup"><span data-stu-id="672f5-134">Yes (default)</span></span>        | [<span data-ttu-id="672f5-135">Formát komprimovaných dat Brotli</span><span class="sxs-lookup"><span data-stu-id="672f5-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="672f5-136">Ne</span><span class="sxs-lookup"><span data-stu-id="672f5-136">No</span></span>                   | [<span data-ttu-id="672f5-137">Formát ZÚŽENé komprese dat</span><span class="sxs-lookup"><span data-stu-id="672f5-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="672f5-138">Ne</span><span class="sxs-lookup"><span data-stu-id="672f5-138">No</span></span>                   | [<span data-ttu-id="672f5-139">Efektivní výměna XML pro W3C</span><span class="sxs-lookup"><span data-stu-id="672f5-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="672f5-140">Ano</span><span class="sxs-lookup"><span data-stu-id="672f5-140">Yes</span></span>                  | [<span data-ttu-id="672f5-141">Formát souboru gzip</span><span class="sxs-lookup"><span data-stu-id="672f5-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="672f5-142">Ano</span><span class="sxs-lookup"><span data-stu-id="672f5-142">Yes</span></span>                  | <span data-ttu-id="672f5-143">Identifikátor "bez kódování": odpověď nesmí být kódovaná.</span><span class="sxs-lookup"><span data-stu-id="672f5-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="672f5-144">Ne</span><span class="sxs-lookup"><span data-stu-id="672f5-144">No</span></span>                   | [<span data-ttu-id="672f5-145">Formát přenosu v síti pro archivy Java</span><span class="sxs-lookup"><span data-stu-id="672f5-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="672f5-146">Ano</span><span class="sxs-lookup"><span data-stu-id="672f5-146">Yes</span></span>                  | <span data-ttu-id="672f5-147">Jakékoli dostupné kódování obsahu není explicitně požadováno.</span><span class="sxs-lookup"><span data-stu-id="672f5-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="672f5-148">`Accept-Encoding` hodnoty hlaviček</span><span class="sxs-lookup"><span data-stu-id="672f5-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="672f5-149">Middleware – podporováno</span><span class="sxs-lookup"><span data-stu-id="672f5-149">Middleware Supported</span></span> | <span data-ttu-id="672f5-150">Popis</span><span class="sxs-lookup"><span data-stu-id="672f5-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="672f5-151">Ne</span><span class="sxs-lookup"><span data-stu-id="672f5-151">No</span></span>                   | [<span data-ttu-id="672f5-152">Formát komprimovaných dat Brotli</span><span class="sxs-lookup"><span data-stu-id="672f5-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="672f5-153">Ne</span><span class="sxs-lookup"><span data-stu-id="672f5-153">No</span></span>                   | [<span data-ttu-id="672f5-154">Formát ZÚŽENé komprese dat</span><span class="sxs-lookup"><span data-stu-id="672f5-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="672f5-155">Ne</span><span class="sxs-lookup"><span data-stu-id="672f5-155">No</span></span>                   | [<span data-ttu-id="672f5-156">Efektivní výměna XML pro W3C</span><span class="sxs-lookup"><span data-stu-id="672f5-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="672f5-157">Ano (výchozí)</span><span class="sxs-lookup"><span data-stu-id="672f5-157">Yes (default)</span></span>        | [<span data-ttu-id="672f5-158">Formát souboru gzip</span><span class="sxs-lookup"><span data-stu-id="672f5-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="672f5-159">Ano</span><span class="sxs-lookup"><span data-stu-id="672f5-159">Yes</span></span>                  | <span data-ttu-id="672f5-160">Identifikátor "bez kódování": odpověď nesmí být kódovaná.</span><span class="sxs-lookup"><span data-stu-id="672f5-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="672f5-161">Ne</span><span class="sxs-lookup"><span data-stu-id="672f5-161">No</span></span>                   | [<span data-ttu-id="672f5-162">Formát přenosu v síti pro archivy Java</span><span class="sxs-lookup"><span data-stu-id="672f5-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="672f5-163">Ano</span><span class="sxs-lookup"><span data-stu-id="672f5-163">Yes</span></span>                  | <span data-ttu-id="672f5-164">Jakékoli dostupné kódování obsahu není explicitně požadováno.</span><span class="sxs-lookup"><span data-stu-id="672f5-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="672f5-165">Další informace najdete v [seznamu kódů oficiálního obsahu IANA](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span><span class="sxs-lookup"><span data-stu-id="672f5-165">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="672f5-166">Middleware umožňuje přidat další zprostředkovatele komprese pro vlastní hodnoty hlaviček `Accept-Encoding`.</span><span class="sxs-lookup"><span data-stu-id="672f5-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="672f5-167">Další informace najdete v tématu [Vlastní zprostředkovatelé](#custom-providers) níže.</span><span class="sxs-lookup"><span data-stu-id="672f5-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="672f5-168">Middleware je schopná chovat hodnoty kvality (qvalue, `q`), když je klient odesílá k určení priorit schémat komprese.</span><span class="sxs-lookup"><span data-stu-id="672f5-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="672f5-169">Další informace najdete v [dokumentu RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span><span class="sxs-lookup"><span data-stu-id="672f5-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="672f5-170">Algoritmy komprese se vztahují na kompromisy mezi rychlostí komprese a efektivitou komprese.</span><span class="sxs-lookup"><span data-stu-id="672f5-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="672f5-171">*Efektivita* v tomto kontextu odkazuje na velikost výstupu po kompresi.</span><span class="sxs-lookup"><span data-stu-id="672f5-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="672f5-172">Nejmenší velikost dosáhne *optimální* komprese.</span><span class="sxs-lookup"><span data-stu-id="672f5-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="672f5-173">Hlavičky zahrnuté v tématu vyžádání, odeslání, ukládání do mezipaměti a příjem komprimovaného obsahu jsou popsány v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="672f5-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="672f5-174">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="672f5-174">Header</span></span>             | <span data-ttu-id="672f5-175">Role</span><span class="sxs-lookup"><span data-stu-id="672f5-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="672f5-176">Odesílá se z klienta na server k označení schémat kódování obsahu, které jsou přijatelné pro klienta.</span><span class="sxs-lookup"><span data-stu-id="672f5-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="672f5-177">Odesílá se ze serveru do klienta, aby označovala kódování obsahu v datové části.</span><span class="sxs-lookup"><span data-stu-id="672f5-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="672f5-178">Když dojde k komprimaci, `Content-Length` záhlaví je odebráno, protože obsah těla se po kompresi odpovědi změní.</span><span class="sxs-lookup"><span data-stu-id="672f5-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="672f5-179">Když dojde k komprimaci, `Content-MD5` záhlaví je odebráno, protože došlo ke změně obsahu textu a hodnota hash již není platná.</span><span class="sxs-lookup"><span data-stu-id="672f5-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="672f5-180">Určuje typ MIME obsahu.</span><span class="sxs-lookup"><span data-stu-id="672f5-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="672f5-181">Každá odpověď by měla specifikovat svůj `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="672f5-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="672f5-182">Middleware kontroluje tuto hodnotu, aby určila, jestli by měla být komprimovaná odpověď.</span><span class="sxs-lookup"><span data-stu-id="672f5-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="672f5-183">Middleware určuje sadu [výchozích typů MIME](#mime-types) , které může kódovat, ale můžete nahradit nebo přidat typy MIME.</span><span class="sxs-lookup"><span data-stu-id="672f5-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="672f5-184">Když server odešle server s hodnotou `Accept-Encoding` klientům a proxy, označuje `Vary` záhlaví klienta nebo proxy serveru, že by měl ukládat (měnit) odpovědi na základě hodnoty v hlavičce `Accept-Encoding` požadavku.</span><span class="sxs-lookup"><span data-stu-id="672f5-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="672f5-185">Výsledkem vrácení obsahu s hlavičkou `Vary: Accept-Encoding` je, že komprimované i nekomprimované odpovědi jsou ukládány do mezipaměti samostatně.</span><span class="sxs-lookup"><span data-stu-id="672f5-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="672f5-186">Prozkoumejte funkce middleware pro kompresi odpovědí s [ukázkovou aplikací](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span><span class="sxs-lookup"><span data-stu-id="672f5-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="672f5-187">Ukázka znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="672f5-187">The sample illustrates:</span></span>

* <span data-ttu-id="672f5-188">Komprese odpovědí aplikace pomocí gzip a vlastních zprostředkovatelů komprese.</span><span class="sxs-lookup"><span data-stu-id="672f5-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="672f5-189">Postup přidání typu MIME do výchozího seznamu typů MIME pro kompresi.</span><span class="sxs-lookup"><span data-stu-id="672f5-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="672f5-190">Balíček</span><span class="sxs-lookup"><span data-stu-id="672f5-190">Package</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="672f5-191">Middleware pro kompresi odpovědí poskytuje balíček [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) , který je implicitně zahrnutý v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="672f5-191">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="672f5-192">Chcete-li do projektu zahrnout middleware, přidejte odkaz na soubor [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), který obsahuje balíček [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) .</span><span class="sxs-lookup"><span data-stu-id="672f5-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="672f5-193">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="672f5-193">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="672f5-194">Následující kód ukazuje, jak povolit middleware pro kompresi odpovědí pro výchozí typy MIME a zprostředkovatele komprese ([Brotli](#brotli-compression-provider) a [gzip](#gzip-compression-provider)):</span><span class="sxs-lookup"><span data-stu-id="672f5-194">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="672f5-195">Následující kód ukazuje, jak povolit middleware pro kompresi odpovědí pro výchozí typy MIME a [zprostředkovatele komprese GZip](#gzip-compression-provider):</span><span class="sxs-lookup"><span data-stu-id="672f5-195">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="672f5-196">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="672f5-196">Notes:</span></span>

* <span data-ttu-id="672f5-197">před `app.UseMvc`je nutné volat `app.UseResponseCompression`.</span><span class="sxs-lookup"><span data-stu-id="672f5-197">`app.UseResponseCompression` must be called before `app.UseMvc`.</span></span>
* <span data-ttu-id="672f5-198">Pomocí nástroje, jako je například [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)nebo [poster](https://www.getpostman.com/) , nastavte hlavičku `Accept-Encoding` žádosti a prostudujte hlavičky, velikost a text odpovědi.</span><span class="sxs-lookup"><span data-stu-id="672f5-198">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="672f5-199">Odešlete žádost do ukázkové aplikace bez hlavičky `Accept-Encoding` a sledujte, že odpověď je nekomprimovaná.</span><span class="sxs-lookup"><span data-stu-id="672f5-199">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="672f5-200">V odpovědi se nevyskytují hlavičky `Content-Encoding` a `Vary`.</span><span class="sxs-lookup"><span data-stu-id="672f5-200">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Okno Fiddler znázorňující výsledek požadavku bez hlavičky Accept-Encoding.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="672f5-203">Odešlete žádost do ukázkové aplikace s hlavičkou `Accept-Encoding: br` (Brotli Compression) a sledujte, že je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="672f5-203">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="672f5-204">V odpovědi jsou k dispozici hlavičky `Content-Encoding` a `Vary`.</span><span class="sxs-lookup"><span data-stu-id="672f5-204">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou br.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="672f5-208">Odešlete žádost do ukázkové aplikace s hlavičkou `Accept-Encoding: gzip` a sledujte, že je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="672f5-208">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="672f5-209">V odpovědi jsou k dispozici hlavičky `Content-Encoding` a `Vary`.</span><span class="sxs-lookup"><span data-stu-id="672f5-209">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou gzip.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="672f5-213">Poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="672f5-213">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="672f5-214">Brotli kompresní poskytovatel</span><span class="sxs-lookup"><span data-stu-id="672f5-214">Brotli Compression Provider</span></span>

<span data-ttu-id="672f5-215">Pro komprimaci odpovědí s [formátem komprimovaných dat Brotli](https://tools.ietf.org/html/rfc7932)použijte <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>.</span><span class="sxs-lookup"><span data-stu-id="672f5-215">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="672f5-216">Pokud nejsou do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>explicitně přidány žádní zprostředkovatelé komprese:</span><span class="sxs-lookup"><span data-stu-id="672f5-216">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="672f5-217">Zprostředkovatel komprese Brotli se ve výchozím nastavení přidá k poli zprostředkovatelů komprese společně se [zprostředkovatelem komprese GZip](#gzip-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="672f5-217">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="672f5-218">Komprese se nastaví jako výchozí Brotli komprese, když klient podporuje formát komprimovaných dat Brotli.</span><span class="sxs-lookup"><span data-stu-id="672f5-218">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="672f5-219">Pokud klient nepodporuje Brotli, komprese je standardně nastavená na gzip, když klient podporuje kompresi gzip.</span><span class="sxs-lookup"><span data-stu-id="672f5-219">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="672f5-220">Zprostředkovatel komprese Brotoli je nutné přidat, když jsou explicitně přidány poskytovatelé komprese:</span><span class="sxs-lookup"><span data-stu-id="672f5-220">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="672f5-221">Nastavte úroveň komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span><span class="sxs-lookup"><span data-stu-id="672f5-221">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="672f5-222">Zprostředkovatel komprese Brotli je výchozím nastavením nejrychlejší kompresní úrovně ([CompressionLevel. nejrychlejší](xref:System.IO.Compression.CompressionLevel)), což nemusí způsobovat nejúčinnější kompresi.</span><span class="sxs-lookup"><span data-stu-id="672f5-222">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="672f5-223">Pokud je žádoucí co nejúčinnější komprese, nakonfigurujte middleware pro optimální kompresi.</span><span class="sxs-lookup"><span data-stu-id="672f5-223">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="672f5-224">Úroveň komprese</span><span class="sxs-lookup"><span data-stu-id="672f5-224">Compression Level</span></span> | <span data-ttu-id="672f5-225">Popis</span><span class="sxs-lookup"><span data-stu-id="672f5-225">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="672f5-226">CompressionLevel. nejrychlejší</span><span class="sxs-lookup"><span data-stu-id="672f5-226">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="672f5-227">Komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný výstup není optimálně komprimován.</span><span class="sxs-lookup"><span data-stu-id="672f5-227">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="672f5-228">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="672f5-228">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="672f5-229">Není nutné provádět kompresi.</span><span class="sxs-lookup"><span data-stu-id="672f5-229">No compression should be performed.</span></span> |
| [<span data-ttu-id="672f5-230">CompressionLevel. optimální</span><span class="sxs-lookup"><span data-stu-id="672f5-230">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="672f5-231">Odpovědi by se měly optimálně komprimovat, a to i v případě, že je komprese delší dobu trvat.</span><span class="sxs-lookup"><span data-stu-id="672f5-231">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="672f5-232">Zprostředkovatel komprese GZip</span><span class="sxs-lookup"><span data-stu-id="672f5-232">Gzip Compression Provider</span></span>

<span data-ttu-id="672f5-233">Pomocí <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> můžete komprimovat odpovědi ve [formátu souboru gzip](https://tools.ietf.org/html/rfc1952).</span><span class="sxs-lookup"><span data-stu-id="672f5-233">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="672f5-234">Pokud nejsou do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>explicitně přidány žádní zprostředkovatelé komprese:</span><span class="sxs-lookup"><span data-stu-id="672f5-234">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="672f5-235">Zprostředkovatel komprese GZip je ve výchozím nastavení přidán k poli zprostředkovatelů komprese společně s [poskytovatelem Brotli Compression](#brotli-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="672f5-235">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="672f5-236">Komprese se nastaví jako výchozí Brotli komprese, když klient podporuje formát komprimovaných dat Brotli.</span><span class="sxs-lookup"><span data-stu-id="672f5-236">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="672f5-237">Pokud klient nepodporuje Brotli, komprese je standardně nastavená na gzip, když klient podporuje kompresi gzip.</span><span class="sxs-lookup"><span data-stu-id="672f5-237">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="672f5-238">Zprostředkovatel komprese GZip je ve výchozím nastavení přidán k poli zprostředkovatelů komprese.</span><span class="sxs-lookup"><span data-stu-id="672f5-238">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="672f5-239">Komprese je výchozím nastavením pro nástroj gzip, pokud klient podporuje kompresi gzip.</span><span class="sxs-lookup"><span data-stu-id="672f5-239">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="672f5-240">Zprostředkovatel komprese GZip se musí přidat, když se explicitně přidají poskytovatelé komprese:</span><span class="sxs-lookup"><span data-stu-id="672f5-240">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

<span data-ttu-id="672f5-241">Nastavte úroveň komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span><span class="sxs-lookup"><span data-stu-id="672f5-241">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="672f5-242">Zprostředkovatel komprese GZip je výchozím nastavením nejrychlejší kompresní úrovně ([CompressionLevel. nejrychlejší](xref:System.IO.Compression.CompressionLevel)), což nemusí způsobovat nejúčinnější kompresi.</span><span class="sxs-lookup"><span data-stu-id="672f5-242">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="672f5-243">Pokud je žádoucí co nejúčinnější komprese, nakonfigurujte middleware pro optimální kompresi.</span><span class="sxs-lookup"><span data-stu-id="672f5-243">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="672f5-244">Úroveň komprese</span><span class="sxs-lookup"><span data-stu-id="672f5-244">Compression Level</span></span> | <span data-ttu-id="672f5-245">Popis</span><span class="sxs-lookup"><span data-stu-id="672f5-245">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="672f5-246">CompressionLevel. nejrychlejší</span><span class="sxs-lookup"><span data-stu-id="672f5-246">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="672f5-247">Komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný výstup není optimálně komprimován.</span><span class="sxs-lookup"><span data-stu-id="672f5-247">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="672f5-248">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="672f5-248">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="672f5-249">Není nutné provádět kompresi.</span><span class="sxs-lookup"><span data-stu-id="672f5-249">No compression should be performed.</span></span> |
| [<span data-ttu-id="672f5-250">CompressionLevel. optimální</span><span class="sxs-lookup"><span data-stu-id="672f5-250">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="672f5-251">Odpovědi by se měly optimálně komprimovat, a to i v případě, že je komprese delší dobu trvat.</span><span class="sxs-lookup"><span data-stu-id="672f5-251">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="672f5-252">Vlastní poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="672f5-252">Custom providers</span></span>

<span data-ttu-id="672f5-253">Vytvářejte vlastní implementace komprese pomocí <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span><span class="sxs-lookup"><span data-stu-id="672f5-253">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="672f5-254"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> představuje kódování obsahu, které vytváří tato `ICompressionProvider`.</span><span class="sxs-lookup"><span data-stu-id="672f5-254">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="672f5-255">Middleware používá tyto informace k výběru poskytovatele na základě seznamu zadaného v hlavičce `Accept-Encoding` žádosti.</span><span class="sxs-lookup"><span data-stu-id="672f5-255">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="672f5-256">Pomocí ukázkové aplikace klient odesílá požadavek s hlavičkou `Accept-Encoding: mycustomcompression`.</span><span class="sxs-lookup"><span data-stu-id="672f5-256">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="672f5-257">Middleware používá vlastní kompresní implementaci a vrací odpověď s hlavičkou `Content-Encoding: mycustomcompression`.</span><span class="sxs-lookup"><span data-stu-id="672f5-257">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="672f5-258">Aby mohla vlastní implementace komprese fungovat, musí být klient schopný dekomprimovat vlastní kódování.</span><span class="sxs-lookup"><span data-stu-id="672f5-258">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="672f5-259">Odešlete žádost do ukázkové aplikace s hlavičkou `Accept-Encoding: mycustomcompression` a sledujte hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="672f5-259">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="672f5-260">V odpovědi jsou k dispozici hlavičky `Vary` a `Content-Encoding`.</span><span class="sxs-lookup"><span data-stu-id="672f5-260">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="672f5-261">Text odpovědi (nezobrazený) není ukázkou komprimován.</span><span class="sxs-lookup"><span data-stu-id="672f5-261">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="672f5-262">Neexistuje žádná implementace komprese v třídě `CustomCompressionProvider` ukázky.</span><span class="sxs-lookup"><span data-stu-id="672f5-262">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="672f5-263">Nicméně Ukázka ukazuje, kde byste takový kompresní algoritmus implementovali.</span><span class="sxs-lookup"><span data-stu-id="672f5-263">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Okno Fiddler znázorňující výsledek požadavku s hlavičkou Accept-Encoding a hodnotou mycustomcompression.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="672f5-266">typy MIME</span><span class="sxs-lookup"><span data-stu-id="672f5-266">MIME types</span></span>

<span data-ttu-id="672f5-267">Middleware určuje výchozí sadu typů MIME pro kompresi:</span><span class="sxs-lookup"><span data-stu-id="672f5-267">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="672f5-268">Nahraďte nebo připojovat typy MIME pomocí možností middlewaru pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="672f5-268">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="672f5-269">Všimněte si, že zástupné typy MIME, například `text/*`, nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="672f5-269">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="672f5-270">Ukázková aplikace přidá typ MIME pro `image/svg+xml` a komprimuje a obsluhuje obrázek ASP.NET Core banneru (*banner. SVG*).</span><span class="sxs-lookup"><span data-stu-id="672f5-270">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="672f5-271">Komprese pomocí zabezpečeného protokolu</span><span class="sxs-lookup"><span data-stu-id="672f5-271">Compression with secure protocol</span></span>

<span data-ttu-id="672f5-272">Komprimované odpovědi přes zabezpečená připojení se dají řídit pomocí možnosti `EnableForHttps`, která je ve výchozím nastavení zakázaná.</span><span class="sxs-lookup"><span data-stu-id="672f5-272">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="672f5-273">Použití komprese s dynamicky generovanými stránkami může vést k problémům se zabezpečením, jako jsou [trestné činy](https://wikipedia.org/wiki/CRIME_(security_exploit)) a útoky za [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) .</span><span class="sxs-lookup"><span data-stu-id="672f5-273">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="672f5-274">Přidávání záhlaví Vary</span><span class="sxs-lookup"><span data-stu-id="672f5-274">Adding the Vary header</span></span>

<span data-ttu-id="672f5-275">Při kompresi odpovědí na základě hlavičky `Accept-Encoding` existuje potenciálně více komprimovaných verzí odpovědi a nekomprimovaná verze.</span><span class="sxs-lookup"><span data-stu-id="672f5-275">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="672f5-276">Aby bylo možné zadat do mezipamětí klienta a proxy serveru, že existuje více verzí a měl by být uložen, je `Vary` hlavička přidána s hodnotou `Accept-Encoding`.</span><span class="sxs-lookup"><span data-stu-id="672f5-276">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="672f5-277">V ASP.NET Core 2,0 nebo novějším, middleware přidá hlavičku `Vary` automaticky, když je odpověď komprimovaná.</span><span class="sxs-lookup"><span data-stu-id="672f5-277">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="672f5-278">Problém middlewaru, když za Nginx reverzní proxy</span><span class="sxs-lookup"><span data-stu-id="672f5-278">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="672f5-279">Pokud je požadavek proxy serverem Nginx, bude odstraněna hlavička `Accept-Encoding`.</span><span class="sxs-lookup"><span data-stu-id="672f5-279">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="672f5-280">Odebrání hlavičky `Accept-Encoding` brání middlewaru v komprimaci odpovědi.</span><span class="sxs-lookup"><span data-stu-id="672f5-280">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="672f5-281">Další informace naleznete v tématu [Nginx: Compression a decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span><span class="sxs-lookup"><span data-stu-id="672f5-281">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="672f5-282">Tento problém je sledován pomocí [předávací komprese pro Nginx (ASPNET/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span><span class="sxs-lookup"><span data-stu-id="672f5-282">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="672f5-283">Práce s dynamickou kompresí služby IIS</span><span class="sxs-lookup"><span data-stu-id="672f5-283">Working with IIS dynamic compression</span></span>

<span data-ttu-id="672f5-284">Pokud máte aktivní dynamický kompresní modul služby IIS nakonfigurovaný na úrovni serveru, který chcete pro aplikaci zakázat, zakažte modul s přidáním do souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="672f5-284">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="672f5-285">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="672f5-285">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="672f5-286">Odstraňování problémů</span><span class="sxs-lookup"><span data-stu-id="672f5-286">Troubleshooting</span></span>

<span data-ttu-id="672f5-287">Použijte nástroj, jako je [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)nebo [post](https://www.getpostman.com/), což vám umožní nastavit hlavičku požadavku `Accept-Encoding` a prozkoumat hlavičky, velikost a text odpovědi.</span><span class="sxs-lookup"><span data-stu-id="672f5-287">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="672f5-288">Ve výchozím nastavení middleware pro komprimaci odezvy komprimuje odezvy, které splňují následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="672f5-288">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="672f5-289">Hlavička `Accept-Encoding` je k dispozici s hodnotou `br`, `gzip`, `*`nebo vlastním kódováním, které odpovídá vlastnímu poskytovateli komprese, který jste navázali.</span><span class="sxs-lookup"><span data-stu-id="672f5-289">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="672f5-290">Hodnota nesmí být `identity` nebo mít hodnotu kvality (qvalue, `q`) nastavenou na 0 (nula).</span><span class="sxs-lookup"><span data-stu-id="672f5-290">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="672f5-291">Je nutné nastavit typ MIME (`Content-Type`) a musí odpovídat typu MIME nakonfigurovanému v <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span><span class="sxs-lookup"><span data-stu-id="672f5-291">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="672f5-292">Požadavek nesmí obsahovat hlavičku `Content-Range`.</span><span class="sxs-lookup"><span data-stu-id="672f5-292">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="672f5-293">Požadavek musí používat protokol HTTP (nezabezpečený protokol), pokud není v možnostech middleware pro kompresi odpovědí nakonfigurovaný protokol HTTPS (Secure Protocol).</span><span class="sxs-lookup"><span data-stu-id="672f5-293">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="672f5-294">*Při povolování komprese zabezpečeného obsahu si všimněte [výše popsaného](#compression-with-secure-protocol) nebezpečí.*</span><span class="sxs-lookup"><span data-stu-id="672f5-294">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="672f5-295">Hlavička `Accept-Encoding` je k dispozici s hodnotou `gzip`, `*`nebo vlastním kódováním, které se shoduje s vlastním poskytovatelem komprese, který jste navázali.</span><span class="sxs-lookup"><span data-stu-id="672f5-295">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="672f5-296">Hodnota nesmí být `identity` nebo mít hodnotu kvality (qvalue, `q`) nastavenou na 0 (nula).</span><span class="sxs-lookup"><span data-stu-id="672f5-296">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="672f5-297">Je nutné nastavit typ MIME (`Content-Type`) a musí odpovídat typu MIME nakonfigurovanému v <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span><span class="sxs-lookup"><span data-stu-id="672f5-297">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="672f5-298">Požadavek nesmí obsahovat hlavičku `Content-Range`.</span><span class="sxs-lookup"><span data-stu-id="672f5-298">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="672f5-299">Požadavek musí používat protokol HTTP (nezabezpečený protokol), pokud není v možnostech middleware pro kompresi odpovědí nakonfigurovaný protokol HTTPS (Secure Protocol).</span><span class="sxs-lookup"><span data-stu-id="672f5-299">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="672f5-300">*Při povolování komprese zabezpečeného obsahu si všimněte [výše popsaného](#compression-with-secure-protocol) nebezpečí.*</span><span class="sxs-lookup"><span data-stu-id="672f5-300">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="672f5-301">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="672f5-301">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="672f5-302">Síť pro vývojáře Mozilla: přijmout – kódování</span><span class="sxs-lookup"><span data-stu-id="672f5-302">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="672f5-303">RFC 7231 část 3.1.2.1: kódování obsahu</span><span class="sxs-lookup"><span data-stu-id="672f5-303">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="672f5-304">RFC 7230 oddíl 4.2.3: kódování gzip</span><span class="sxs-lookup"><span data-stu-id="672f5-304">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="672f5-305">Specifikace formátu souboru GZIP verze 4,3</span><span class="sxs-lookup"><span data-stu-id="672f5-305">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)
