# <a name="response-compression-sample-application-aspnet-core-2x"></a>Odpověď komprese ukázkovou aplikaci (ASP.NET Core 2.x)

Tento příklad ukazuje použití metody ASP.NET Core 2.x Middleware pro kompresi odpovědí na komprimovaly odpovědi HTTP. Vzorek ukazuje Gzip, Brotli a komprese Vlastní zprostředkovatelé pro textové a obrázkové odpovědi a ukazuje, jak přidat typ MIME pro kompresi. ASP.NET Core 1.x ukázku najdete v tématu [odpovědi komprese ukázkovou aplikaci (ASP.NET Core 1.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/1.x).

## <a name="examples-in-this-sample"></a>Příklady v této ukázce

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum textový soubor odpovědi 2,044 bajtů, které komprimuje ~ 979 bajtů.
    * **/testfile1kb.txt** -text odpovědi soubor 1,033 bajtů, které komprimuje přibližně 36 bajtů.
    * **/ skapat** – odpovědi na 1 sekundových intervalech vydaný jako jednotlivé znaky.
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum textový soubor odpovědi 2,044 bajtů, které komprimuje ~ 927 bajtů.
    * **/testfile1kb.txt** – soubor odpověď 1,033 bajtů, které komprimuje ~ 47 bajtů.
    * **/ skapat** – odpovědi na 1 sekundových intervalech vydaný jako jednotlivé znaky.
  * `image/svg+xml`
    * **/Banner.SVG** – objekt grafiky SVG (Scalable Vector) odpověď image 9,707 bajtů, která komprimuje ~ 4,459 bajtů.
* `CustomCompressionProvider`<br>Ukazuje, jak implementovat vlastní komprese poskytovatele pro použití s middlewarem.

Pokud požadavek obsahuje `Accept-Encoding` kompresi záhlaví a odpověď je úspěšný, middleware automaticky přidá `Vary: Accept-Encoding` hlavičky odpovědi. `Vary` Nastaví záhlaví mezipaměti k udržování více kopií v reakci na alternativní hodnoty `Accept-Encoding`, tak i na komprimované (metodou Gzip nebo Brotli) a nekomprimované verze jsou uloženy v mezipaměti pro systémy, které můžete buď přijmout komprimované nebo nekomprimovaných odpovědi.

## <a name="use-the-sample"></a>Použitím této ukázky

1. Vytvořit žádost o prostřednictvím [Fiddleru](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), nebo [Postman](https://www.getpostman.com/) k aplikaci bez `Accept-Encoding` záhlaví a Všimněte si datové části odpovědi velikost odpovědi a hlavičky odpovědi.
1. Přidat `Accept-Encoding: br` nebo `Accept-Encoding: gzip` záhlaví a poznamenejte si velikost komprimovaných odpovědi a hlaviček odpovědí. Velikost odpovědi zahodí a `Content-Encoding` middlewarem označující, že komprese se buď Gzip je zahrnuta hlavička odpovědi nebo Brotli došlo k chybě. Když se podíváte na datovou část odpovědi pro Lorem Ipsum nebo **testfile1kb.txt** odpovědi, uvidíte, že text je komprimovaná a nejde přečíst.
1. Přidat `Accept-Encoding: mycustomcompression` záhlaví a poznamenejte si hlavičky odpovědi. `CustomCompressionProvider` Je prázdná implementace, která není ve skutečnosti komprimovat odpověď, ale můžete vytvořit vlastní kompresi datového proudu obálku pro `CreateStream()` metody.
