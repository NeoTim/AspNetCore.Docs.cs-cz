---
title: Kompresi odpovědí v ASP.NET Core
author: guardrex
description: Další informace o kompresi odpovědí a jak používat Middleware pro kompresi odpovědí v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: performance/response-compression
ms.openlocfilehash: d5d2da3dc0a8a452de97d98161d429389d2f7638
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815606"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="4def4-103">Kompresi odpovědí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4def4-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="4def4-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4def4-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4def4-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4def4-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4def4-106">Šířka pásma sítě je omezená prostředků.</span><span class="sxs-lookup"><span data-stu-id="4def4-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="4def4-107">Odezvu aplikace obvykle zlepšuje zmenšení velikosti odpovědi, a to často výrazně.</span><span class="sxs-lookup"><span data-stu-id="4def4-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="4def4-108">Jedním ze způsobů zmenšení velikosti datové části je komprese odpovědí vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="4def4-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="4def4-109">Kdy použít Middleware pro kompresi odpovědí</span><span class="sxs-lookup"><span data-stu-id="4def4-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="4def4-110">Použití technologie komprese odpovědi na serveru IIS, Apache nebo Nginx.</span><span class="sxs-lookup"><span data-stu-id="4def4-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="4def4-111">Výkon middleware pravděpodobně nebude odpovídat moduly serveru.</span><span class="sxs-lookup"><span data-stu-id="4def4-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="4def4-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) serveru a [Kestrel](xref:fundamentals/servers/kestrel) server aktuálně nenabízí podporu integrovanou komprese.</span><span class="sxs-lookup"><span data-stu-id="4def4-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="4def4-113">Middleware pro kompresi odpovědí použijte, když jste:</span><span class="sxs-lookup"><span data-stu-id="4def4-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="4def4-114">Nelze použít následující technologie serverových komprese:</span><span class="sxs-lookup"><span data-stu-id="4def4-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="4def4-115">Modul dynamická komprese služby IIS</span><span class="sxs-lookup"><span data-stu-id="4def4-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="4def4-116">Apache mod_deflate modulu</span><span class="sxs-lookup"><span data-stu-id="4def4-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="4def4-117">Server Nginx komprese a dekomprese</span><span class="sxs-lookup"><span data-stu-id="4def4-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="4def4-118">Hostování přímo na:</span><span class="sxs-lookup"><span data-stu-id="4def4-118">Hosting directly on:</span></span>
  * <span data-ttu-id="4def4-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (dříve se označovaly jako WebListener)</span><span class="sxs-lookup"><span data-stu-id="4def4-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="4def4-120">Kestrel serveru</span><span class="sxs-lookup"><span data-stu-id="4def4-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="4def4-121">Komprese odpovědí</span><span class="sxs-lookup"><span data-stu-id="4def4-121">Response compression</span></span>

<span data-ttu-id="4def4-122">Obvykle žádnou odpověď komprimované nativně využívat kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="4def4-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="4def4-123">Odpovědí se komprimované nativně obvykle patří: Šablony stylů CSS, JavaScript, HTML, XML a JSON.</span><span class="sxs-lookup"><span data-stu-id="4def4-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="4def4-124">Nativně komprimované prostředky, jako jsou například soubory PNG by neměly komprimovat.</span><span class="sxs-lookup"><span data-stu-id="4def4-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="4def4-125">Pokud se pokusíte další komprimovat nativně zkomprimovanou odpověď, všechny malé Další velikosti a přenos zkrácení času nutného pravděpodobně overshadowed v době, jakou trvalo zpracování komprese.</span><span class="sxs-lookup"><span data-stu-id="4def4-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="4def4-126">Nekomprimovat soubory menší než přibližně 150 – 1 000 bajtů (v závislosti na obsahu souboru a efektivity komprese).</span><span class="sxs-lookup"><span data-stu-id="4def4-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="4def4-127">Režie komprimace malých souborů může vytvořit komprimovaný soubor větší než nekomprimovaného souboru.</span><span class="sxs-lookup"><span data-stu-id="4def4-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="4def4-128">Když klient může zpracovat komprimovaného obsahu, klient informuje server její možnosti odesláním `Accept-Encoding` záhlaví s požadavkem.</span><span class="sxs-lookup"><span data-stu-id="4def4-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="4def4-129">Když server odešle komprimovaného obsahu, musí obsahovat informace `Content-Encoding` záhlaví na tom, jak je zakódován zkomprimovanou odpověď.</span><span class="sxs-lookup"><span data-stu-id="4def4-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="4def4-130">V následující tabulce jsou uvedeny obsahu kódování označení podporované middlewarem.</span><span class="sxs-lookup"><span data-stu-id="4def4-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="4def4-131">`Accept-Encoding` hodnoty hlavičky</span><span class="sxs-lookup"><span data-stu-id="4def4-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="4def4-132">Middleware podporována</span><span class="sxs-lookup"><span data-stu-id="4def4-132">Middleware Supported</span></span> | <span data-ttu-id="4def4-133">Popis</span><span class="sxs-lookup"><span data-stu-id="4def4-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="4def4-134">Ano (výchozí)</span><span class="sxs-lookup"><span data-stu-id="4def4-134">Yes (default)</span></span>        | [<span data-ttu-id="4def4-135">Formát komprimovaných dat Brotli</span><span class="sxs-lookup"><span data-stu-id="4def4-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="4def4-136">Ne</span><span class="sxs-lookup"><span data-stu-id="4def4-136">No</span></span>                   | [<span data-ttu-id="4def4-137">Formát komprimovaných dat DEFLATE</span><span class="sxs-lookup"><span data-stu-id="4def4-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="4def4-138">Ne</span><span class="sxs-lookup"><span data-stu-id="4def4-138">No</span></span>                   | [<span data-ttu-id="4def4-139">W3C XML efektivní výměny</span><span class="sxs-lookup"><span data-stu-id="4def4-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="4def4-140">Ano</span><span class="sxs-lookup"><span data-stu-id="4def4-140">Yes</span></span>                  | [<span data-ttu-id="4def4-141">Formát souborů GZIP</span><span class="sxs-lookup"><span data-stu-id="4def4-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="4def4-142">Ano</span><span class="sxs-lookup"><span data-stu-id="4def4-142">Yes</span></span>                  | <span data-ttu-id="4def4-143">"Bez kódování" identifikátor: Odpověď nesmí být zakódován.</span><span class="sxs-lookup"><span data-stu-id="4def4-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="4def4-144">Ne</span><span class="sxs-lookup"><span data-stu-id="4def4-144">No</span></span>                   | [<span data-ttu-id="4def4-145">Formát přenosu sítě pro archivy Java</span><span class="sxs-lookup"><span data-stu-id="4def4-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="4def4-146">Ano</span><span class="sxs-lookup"><span data-stu-id="4def4-146">Yes</span></span>                  | <span data-ttu-id="4def4-147">Žádný k dispozici obsah, kódování není explicitně požadovaný</span><span class="sxs-lookup"><span data-stu-id="4def4-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="4def4-148">`Accept-Encoding` hodnoty hlavičky</span><span class="sxs-lookup"><span data-stu-id="4def4-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="4def4-149">Middleware podporována</span><span class="sxs-lookup"><span data-stu-id="4def4-149">Middleware Supported</span></span> | <span data-ttu-id="4def4-150">Popis</span><span class="sxs-lookup"><span data-stu-id="4def4-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="4def4-151">Ne</span><span class="sxs-lookup"><span data-stu-id="4def4-151">No</span></span>                   | [<span data-ttu-id="4def4-152">Formát komprimovaných dat Brotli</span><span class="sxs-lookup"><span data-stu-id="4def4-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="4def4-153">Ne</span><span class="sxs-lookup"><span data-stu-id="4def4-153">No</span></span>                   | [<span data-ttu-id="4def4-154">Formát komprimovaných dat DEFLATE</span><span class="sxs-lookup"><span data-stu-id="4def4-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="4def4-155">Ne</span><span class="sxs-lookup"><span data-stu-id="4def4-155">No</span></span>                   | [<span data-ttu-id="4def4-156">W3C XML efektivní výměny</span><span class="sxs-lookup"><span data-stu-id="4def4-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="4def4-157">Ano (výchozí)</span><span class="sxs-lookup"><span data-stu-id="4def4-157">Yes (default)</span></span>        | [<span data-ttu-id="4def4-158">Formát souborů GZIP</span><span class="sxs-lookup"><span data-stu-id="4def4-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="4def4-159">Ano</span><span class="sxs-lookup"><span data-stu-id="4def4-159">Yes</span></span>                  | <span data-ttu-id="4def4-160">"Bez kódování" identifikátor: Odpověď nesmí být zakódován.</span><span class="sxs-lookup"><span data-stu-id="4def4-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="4def4-161">Ne</span><span class="sxs-lookup"><span data-stu-id="4def4-161">No</span></span>                   | [<span data-ttu-id="4def4-162">Formát přenosu sítě pro archivy Java</span><span class="sxs-lookup"><span data-stu-id="4def4-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="4def4-163">Ano</span><span class="sxs-lookup"><span data-stu-id="4def4-163">Yes</span></span>                  | <span data-ttu-id="4def4-164">Žádný k dispozici obsah, kódování není explicitně požadovaný</span><span class="sxs-lookup"><span data-stu-id="4def4-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="4def4-165">Další informace najdete v tématu [IANA oficiální kódování seznamu obsahu](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span><span class="sxs-lookup"><span data-stu-id="4def4-165">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="4def4-166">Middleware umožňuje přidat další komprese zprostředkovatelé pro vlastní `Accept-Encoding` hodnoty hlavičky.</span><span class="sxs-lookup"><span data-stu-id="4def4-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="4def4-167">Další informace najdete v tématu [Vlastní zprostředkovatelé](#custom-providers) níže.</span><span class="sxs-lookup"><span data-stu-id="4def4-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="4def4-168">Middleware je schopný reagovat na hodnotu kvality (qvalue, `q`) vážení při odeslání klientem nástroje k určení priority schémat komprese.</span><span class="sxs-lookup"><span data-stu-id="4def4-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="4def4-169">Další informace najdete v tématu [RFC 7231: Přijmout kódování](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span><span class="sxs-lookup"><span data-stu-id="4def4-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="4def4-170">Algoritmy komprese podléhají kompromis mezi komprese rychlost a efektivitu komprese.</span><span class="sxs-lookup"><span data-stu-id="4def4-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="4def4-171">*Efektivnost definování* v tomto kontextu označuje velikost výstup po kompresi.</span><span class="sxs-lookup"><span data-stu-id="4def4-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="4def4-172">Nejmenší velikost se dosahuje nejčastěji *optimální* komprese.</span><span class="sxs-lookup"><span data-stu-id="4def4-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="4def4-173">Záhlaví účastnící se požaduje, odesílání, ukládání do mezipaměti a přijímání komprimovaného obsahu jsou popsány v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="4def4-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="4def4-174">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="4def4-174">Header</span></span>             | <span data-ttu-id="4def4-175">Role</span><span class="sxs-lookup"><span data-stu-id="4def4-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="4def4-176">Odeslaných z klienta na server k označení schémata přijatelné klientovi kódování obsahu.</span><span class="sxs-lookup"><span data-stu-id="4def4-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="4def4-177">Odeslaná ze serveru klientovi jako oznámení, kódování obsahu v datové části</span><span class="sxs-lookup"><span data-stu-id="4def4-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="4def4-178">Pokud dojde k kompresi, `Content-Length` záhlaví Odebereme, protože změny obsahu těla při kompresi odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4def4-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="4def4-179">Pokud dojde k komprese, `Content-MD5` záhlaví Odebereme, protože došlo ke změně obsah textu a -the-hash již není platný.</span><span class="sxs-lookup"><span data-stu-id="4def4-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="4def4-180">Určuje typ MIME obsahu.</span><span class="sxs-lookup"><span data-stu-id="4def4-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="4def4-181">Každou odpověď by měla určit jeho `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="4def4-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="4def4-182">Middleware ověří tuto hodnotu k určení, pokud je nutné zkomprimovat odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4def4-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="4def4-183">Middleware určuje sadu [výchozí typy MIME](#mime-types) , která můžete kódovat, ale můžete nahradit nebo přidat typy MIME.</span><span class="sxs-lookup"><span data-stu-id="4def4-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="4def4-184">Při odeslání server s hodnotou `Accept-Encoding` pro klienty a proxy servery, `Vary` hlavička označuje klienta nebo proxy server, který by měl mezipaměti (lišit) odpovědi na základě hodnoty z `Accept-Encoding` záhlaví požadavku.</span><span class="sxs-lookup"><span data-stu-id="4def4-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="4def4-185">Vrací obsah s výsledkem `Vary: Accept-Encoding` záhlaví je, že i komprimované a jsou v odpovědi na nekomprimované samostatně uložené v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="4def4-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="4def4-186">Seznamte se s funkcemi Middleware pro kompresi odpovědí s [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span><span class="sxs-lookup"><span data-stu-id="4def4-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="4def4-187">Ukázka znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="4def4-187">The sample illustrates:</span></span>

* <span data-ttu-id="4def4-188">Kompresi odpovědí aplikace pomocí Gzip a poskytovatelé vlastní komprese.</span><span class="sxs-lookup"><span data-stu-id="4def4-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="4def4-189">Jak přidat typ MIME do seznamu výchozích typů MIME pro kompresi.</span><span class="sxs-lookup"><span data-stu-id="4def4-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="4def4-190">Balíček</span><span class="sxs-lookup"><span data-stu-id="4def4-190">Package</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4def4-191">Aby byly middleware v projektu, přidejte odkaz na [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), což zahrnuje [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="4def4-191">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="4def4-192">Aby byly middleware v projektu, přidejte odkaz na [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage), což zahrnuje [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="4def4-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="4def4-193">Aby byly middleware v projektu, přidejte odkaz na [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="4def4-193">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="4def4-194">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="4def4-194">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4def4-195">Následující kód ukazuje, jak povolit Middleware pro kompresi odpovědí pro typy MIME výchozí a komprese poskytovatele ([Brotli](#brotli-compression-provider) a [Gzip](#gzip-compression-provider)):</span><span class="sxs-lookup"><span data-stu-id="4def4-195">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4def4-196">Následující kód ukazuje, jak povolit Middleware pro kompresi odpovědí pro typy MIME výchozí a [poskytovatele kompresi Gzip](#gzip-compression-provider):</span><span class="sxs-lookup"><span data-stu-id="4def4-196">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="4def4-197">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="4def4-197">Notes:</span></span>

* <span data-ttu-id="4def4-198">`app.UseResponseCompression` musí být volána před `app.UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="4def4-198">`app.UseResponseCompression` must be called before `app.UseMvc`.</span></span>
* <span data-ttu-id="4def4-199">Použijte nástroj, jako [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), nebo [Postman](https://www.getpostman.com/) nastavit `Accept-Encoding` hlavička požadavku a studovat hlavičky odpovědi, velikost a text.</span><span class="sxs-lookup"><span data-stu-id="4def4-199">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="4def4-200">Odeslat žádost pro ukázkovou aplikaci bez `Accept-Encoding` záhlaví a podívejte se, že odpověď nekomprimované.</span><span class="sxs-lookup"><span data-stu-id="4def4-200">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="4def4-201">`Content-Encoding` a `Vary` záhlaví nejsou k dispozici v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4def4-201">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler okno zobrazuje výsledek požadavku bez hlavičky Accept-Encoding.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4def4-204">Odeslat žádost pro ukázkovou aplikaci s `Accept-Encoding: br` záhlaví (Brotli komprese) a podívejte se, že je komprimován odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4def4-204">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="4def4-205">`Content-Encoding` a `Vary` záhlaví jsou k dispozici v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4def4-205">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler okno zobrazuje výsledek žádosti s hlavičku Accept-Encoding a hodnotu br.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4def4-209">Odeslat žádost pro ukázkovou aplikaci s `Accept-Encoding: gzip` záhlaví a podívejte se, že je komprimován odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4def4-209">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="4def4-210">`Content-Encoding` a `Vary` záhlaví jsou k dispozici v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4def4-210">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler okno zobrazuje výsledek žádosti s hlavičku Accept-Encoding a hodnotu gzip.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="4def4-214">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="4def4-214">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="4def4-215">Zprostředkovatel Brotli komprese</span><span class="sxs-lookup"><span data-stu-id="4def4-215">Brotli Compression Provider</span></span>

<span data-ttu-id="4def4-216">Použití <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> kompresi odpovědí s [Formát komprimovaných dat Brotli](https://tools.ietf.org/html/rfc7932).</span><span class="sxs-lookup"><span data-stu-id="4def4-216">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="4def4-217">Pokud žádný poskytovatel komprese jsou explicitně přidány do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="4def4-217">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="4def4-218">Zprostředkovatel komprese Brotli je přidán ve výchozím nastavení do pole komprese poskytovatelů spolu s [poskytovatele kompresi Gzip](#gzip-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="4def4-218">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="4def4-219">Výchozí nastavení komprese Brotli komprese při Brotli Formát komprimovaných dat je podporované klientem.</span><span class="sxs-lookup"><span data-stu-id="4def4-219">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="4def4-220">Pokud klient není podporován Brotli, komprese výchozí Gzip Pokud klient podporuje kompresi Gzip.</span><span class="sxs-lookup"><span data-stu-id="4def4-220">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4def4-221">Zprostředkovatel Brotoli komprese musí být přidán, když jsou explicitně přidány všechny zprostředkovatele komprese:</span><span class="sxs-lookup"><span data-stu-id="4def4-221">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="4def4-222">Nastavit úroveň díky komprese <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span><span class="sxs-lookup"><span data-stu-id="4def4-222">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="4def4-223">Zprostředkovatel komprese Brotli výchozí hodnota je nejvyšší úroveň komprese ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), který nemusí vracet nejúčinnější komprese.</span><span class="sxs-lookup"><span data-stu-id="4def4-223">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4def4-224">V případě potřeby je nejúčinnější komprese, nakonfigurujte middleware pro kompresi optimální.</span><span class="sxs-lookup"><span data-stu-id="4def4-224">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4def4-225">Úroveň komprese</span><span class="sxs-lookup"><span data-stu-id="4def4-225">Compression Level</span></span> | <span data-ttu-id="4def4-226">Popis</span><span class="sxs-lookup"><span data-stu-id="4def4-226">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4def4-227">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="4def4-227">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4def4-228">Komprese by se měla dokončit co nejrychleji i v případě, že výsledný výstup není komprimována optimálně.</span><span class="sxs-lookup"><span data-stu-id="4def4-228">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4def4-229">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="4def4-229">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4def4-230">Bez komprese je třeba provést.</span><span class="sxs-lookup"><span data-stu-id="4def4-230">No compression should be performed.</span></span> |
| [<span data-ttu-id="4def4-231">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="4def4-231">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4def4-232">Odpovědí by měl být optimálně komprimovaný, i v případě, že komprese trvá déle.</span><span class="sxs-lookup"><span data-stu-id="4def4-232">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="4def4-233">Zprostředkovatel kompresi GZIP</span><span class="sxs-lookup"><span data-stu-id="4def4-233">Gzip Compression Provider</span></span>

<span data-ttu-id="4def4-234">Použití <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> kompresi odpovědí s [formátu Gzip](https://tools.ietf.org/html/rfc1952).</span><span class="sxs-lookup"><span data-stu-id="4def4-234">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="4def4-235">Pokud žádný poskytovatel komprese jsou explicitně přidány do <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="4def4-235">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="4def4-236">Zprostředkovatel kompresi Gzip je přidat ve výchozím nastavení do pole, poskytovatelů komprese spolu s [Brotli komprese poskytovatele](#brotli-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="4def4-236">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="4def4-237">Výchozí nastavení komprese Brotli komprese při Brotli Formát komprimovaných dat je podporované klientem.</span><span class="sxs-lookup"><span data-stu-id="4def4-237">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="4def4-238">Pokud klient není podporován Brotli, komprese výchozí Gzip Pokud klient podporuje kompresi Gzip.</span><span class="sxs-lookup"><span data-stu-id="4def4-238">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="4def4-239">Zprostředkovatel kompresi Gzip je ve výchozím nastavení do pole komprese poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="4def4-239">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="4def4-240">Výchozí hodnota kompresi Gzip, pokud klient podporuje kompresi Gzip.</span><span class="sxs-lookup"><span data-stu-id="4def4-240">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4def4-241">Zprostředkovatel kompresi Gzip musí být přidán, když jsou explicitně přidány všechny zprostředkovatele komprese:</span><span class="sxs-lookup"><span data-stu-id="4def4-241">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5)]

::: moniker-end

<span data-ttu-id="4def4-242">Nastavit úroveň díky komprese <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span><span class="sxs-lookup"><span data-stu-id="4def4-242">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="4def4-243">Zprostředkovatel kompresi Gzip výchozí hodnota je nejvyšší úroveň komprese ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), který nemusí vracet nejúčinnější komprese.</span><span class="sxs-lookup"><span data-stu-id="4def4-243">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4def4-244">V případě potřeby je nejúčinnější komprese, nakonfigurujte middleware pro kompresi optimální.</span><span class="sxs-lookup"><span data-stu-id="4def4-244">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4def4-245">Úroveň komprese</span><span class="sxs-lookup"><span data-stu-id="4def4-245">Compression Level</span></span> | <span data-ttu-id="4def4-246">Popis</span><span class="sxs-lookup"><span data-stu-id="4def4-246">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4def4-247">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="4def4-247">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4def4-248">Komprese by se měla dokončit co nejrychleji i v případě, že výsledný výstup není komprimována optimálně.</span><span class="sxs-lookup"><span data-stu-id="4def4-248">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4def4-249">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="4def4-249">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4def4-250">Bez komprese je třeba provést.</span><span class="sxs-lookup"><span data-stu-id="4def4-250">No compression should be performed.</span></span> |
| [<span data-ttu-id="4def4-251">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="4def4-251">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4def4-252">Odpovědí by měl být optimálně komprimovaný, i v případě, že komprese trvá déle.</span><span class="sxs-lookup"><span data-stu-id="4def4-252">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="4def4-253">Vlastní zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="4def4-253">Custom providers</span></span>

<span data-ttu-id="4def4-254">Vytvořit vlastní komprese implementace s <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span><span class="sxs-lookup"><span data-stu-id="4def4-254">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="4def4-255"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> Reprezentuje obsah, kódování, že tento `ICompressionProvider` vytvoří.</span><span class="sxs-lookup"><span data-stu-id="4def4-255">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="4def4-256">Middleware použije tyto informace k výběru poskytovatele podle zadaného v seznamu `Accept-Encoding` záhlaví požadavku.</span><span class="sxs-lookup"><span data-stu-id="4def4-256">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="4def4-257">Pomocí ukázkové aplikace, klient odešle požadavek s `Accept-Encoding: mycustomcompression` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="4def4-257">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4def4-258">Middleware použije implementace vlastní komprese a vrátí odpověď se `Content-Encoding: mycustomcompression` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="4def4-258">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4def4-259">Klient musí být schopen dekomprimovat vlastní kódování v pořadí pro implementaci vlastního komprese pracovat.</span><span class="sxs-lookup"><span data-stu-id="4def4-259">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="4def4-260">Odeslat žádost pro ukázkovou aplikaci s `Accept-Encoding: mycustomcompression` záhlaví a sledujte hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4def4-260">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="4def4-261">`Vary` a `Content-Encoding` záhlaví jsou k dispozici v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4def4-261">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="4def4-262">Text odpovědi (není vidět) není komprimována v rámci ukázky.</span><span class="sxs-lookup"><span data-stu-id="4def4-262">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="4def4-263">Není k dispozici implementace komprese ve `CustomCompressionProvider` třídy vzorku.</span><span class="sxs-lookup"><span data-stu-id="4def4-263">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="4def4-264">Ale tato ukázka vysvětluje, kdy, měli byste implementovat algoritmus komprese.</span><span class="sxs-lookup"><span data-stu-id="4def4-264">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler okno zobrazuje výsledek žádosti s hlavičku Accept-Encoding a hodnotu mycustomcompression.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="4def4-267">typy MIME</span><span class="sxs-lookup"><span data-stu-id="4def4-267">MIME types</span></span>

<span data-ttu-id="4def4-268">Middleware Určuje výchozí sadu typů MIME pro komprese:</span><span class="sxs-lookup"><span data-stu-id="4def4-268">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="4def4-269">Nahradit nebo přidat typy MIME s možnostmi Middleware pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="4def4-269">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="4def4-270">Všimněte si danému zástupnému znaku MIME typy, jako například `text/*` nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="4def4-270">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="4def4-271">Ukázková aplikace přidá typ MIME pro `image/svg+xml` a komprimuje a ASP.NET Core slouží banner image (*banner.svg*).</span><span class="sxs-lookup"><span data-stu-id="4def4-271">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="4def4-272">Komprese se zabezpečený protokol</span><span class="sxs-lookup"><span data-stu-id="4def4-272">Compression with secure protocol</span></span>

<span data-ttu-id="4def4-273">Komprimované odpovědi prostřednictvím zabezpečeného připojení se dá řídit pomocí `EnableForHttps` možnost, která je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="4def4-273">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="4def4-274">Pomocí komprese dynamicky generované stránky může vést k problémům zabezpečení, jako [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) a [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) útoky.</span><span class="sxs-lookup"><span data-stu-id="4def4-274">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="4def4-275">Přidání měnit hlavičky</span><span class="sxs-lookup"><span data-stu-id="4def4-275">Adding the Vary header</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="4def4-276">Při kompresi odpovědí na základě `Accept-Encoding` záhlaví, jsou potenciálně více komprimované verze odpovědi a nekomprimované verze.</span><span class="sxs-lookup"><span data-stu-id="4def4-276">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="4def4-277">Aby bylo možné, že více verzí existují a by měla být uložena, dát pokyn mezipaměti klienta a serveru proxy `Vary` záhlaví se přidá s `Accept-Encoding` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4def4-277">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="4def4-278">V technologii ASP.NET Core 2.0 nebo novější, přidá middleware `Vary` záhlaví automaticky, pokud je odpověď je komprimován.</span><span class="sxs-lookup"><span data-stu-id="4def4-278">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="4def4-279">Při kompresi odpovědí na základě `Accept-Encoding` záhlaví, jsou potenciálně více komprimované verze odpovědi a nekomprimované verze.</span><span class="sxs-lookup"><span data-stu-id="4def4-279">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="4def4-280">Aby bylo možné, že více verzí existují a by měla být uložena, dát pokyn mezipaměti klienta a serveru proxy `Vary` záhlaví se přidá s `Accept-Encoding` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4def4-280">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="4def4-281">V ASP.NET Core 1.x, přidání `Vary` hlavičky odpovědi se provádí ručně:</span><span class="sxs-lookup"><span data-stu-id="4def4-281">In ASP.NET Core 1.x, adding the `Vary` header to the response is accomplished manually:</span></span>

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="4def4-282">Middleware problém při za reverzní proxy server Nginx</span><span class="sxs-lookup"><span data-stu-id="4def4-282">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="4def4-283">Pokud je požadavek směrovány přes proxy server pomocí Nginx, `Accept-Encoding` odebrat záhlaví.</span><span class="sxs-lookup"><span data-stu-id="4def4-283">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="4def4-284">Odebrání `Accept-Encoding` záhlaví zabraňuje middleware komprese odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4def4-284">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="4def4-285">Další informace najdete v tématu [NGINX: Komprese a dekomprese](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span><span class="sxs-lookup"><span data-stu-id="4def4-285">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="4def4-286">Tento problém je sledován pomocí funkce [zjistit průchozí komprese Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span><span class="sxs-lookup"><span data-stu-id="4def4-286">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="4def4-287">Práce s dynamické komprese služby IIS</span><span class="sxs-lookup"><span data-stu-id="4def4-287">Working with IIS dynamic compression</span></span>

<span data-ttu-id="4def4-288">Pokud máte aktivní IIS dynamické komprese modul nakonfigurována na úrovni serveru, který chcete zakázat aplikaci zakázat modul s parametrem doplněk *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="4def4-288">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="4def4-289">Další informace najdete v tématu [moduly IIS zakázání](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="4def4-289">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4def4-290">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="4def4-290">Troubleshooting</span></span>

<span data-ttu-id="4def4-291">Pomocí některého nástroje, například [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), nebo [Postman](https://www.getpostman.com/), což umožní nastavit `Accept-Encoding` hlavička požadavku a studovat hlavičky odpovědi, velikost a text.</span><span class="sxs-lookup"><span data-stu-id="4def4-291">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="4def4-292">Ve výchozím nastavení komprimuje Middleware pro kompresi odpovědí odpovědi, které splňovat tyto podmínky:</span><span class="sxs-lookup"><span data-stu-id="4def4-292">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="4def4-293">`Accept-Encoding` Záhlaví je k dispozici s hodnotou `br`, `gzip`, `*`, nebo vlastní kódování, který odpovídá vlastní komprese poskytovatele, který jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="4def4-293">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="4def4-294">Hodnota nesmí být `identity` nebo mít hodnotu kvality (qvalue, `q`) nastavení 0 (nula).</span><span class="sxs-lookup"><span data-stu-id="4def4-294">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="4def4-295">Typ MIME (`Content-Type`) musí být nastavena a musí odpovídat typu MIME na nakonfigurované <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span><span class="sxs-lookup"><span data-stu-id="4def4-295">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="4def4-296">Nesmí obsahovat požadavek `Content-Range` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="4def4-296">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="4def4-297">Žádost musí používat zabezpečený protokol (http), pokud zabezpečený protokol (https) je nakonfigurován v možnostech Middleware pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="4def4-297">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="4def4-298">*Všimněte si, nebezpečí [výše popsané](#compression-with-secure-protocol) při povolování zabezpečené komprese obsahu.*</span><span class="sxs-lookup"><span data-stu-id="4def4-298">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="4def4-299">`Accept-Encoding` Záhlaví je k dispozici s hodnotou `gzip`, `*`, nebo vlastní kódování, který odpovídá vlastní komprese poskytovatele, který jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="4def4-299">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="4def4-300">Hodnota nesmí být `identity` nebo mít hodnotu kvality (qvalue, `q`) nastavení 0 (nula).</span><span class="sxs-lookup"><span data-stu-id="4def4-300">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="4def4-301">Typ MIME (`Content-Type`) musí být nastavena a musí odpovídat typu MIME na nakonfigurované <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span><span class="sxs-lookup"><span data-stu-id="4def4-301">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="4def4-302">Nesmí obsahovat požadavek `Content-Range` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="4def4-302">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="4def4-303">Žádost musí používat zabezpečený protokol (http), pokud zabezpečený protokol (https) je nakonfigurován v možnostech Middleware pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="4def4-303">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="4def4-304">*Všimněte si, nebezpečí [výše popsané](#compression-with-secure-protocol) při povolování zabezpečené komprese obsahu.*</span><span class="sxs-lookup"><span data-stu-id="4def4-304">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4def4-305">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4def4-305">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="4def4-306">Mozilla Developer Network: Přijmout kódování</span><span class="sxs-lookup"><span data-stu-id="4def4-306">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="4def4-307">RFC 7231 části 3.1.2.1: Codings obsahu</span><span class="sxs-lookup"><span data-stu-id="4def4-307">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="4def4-308">RFC 7230 oddílu 4.2.3: Kódování GZIP</span><span class="sxs-lookup"><span data-stu-id="4def4-308">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="4def4-309">Verze specifikace formátu souboru GZIP 4.3</span><span class="sxs-lookup"><span data-stu-id="4def4-309">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)
