# <a name="response-compression-sample-application-aspnet-core-3x"></a>Ukázková aplikace pro kompresi odpovědí (ASP.NET Core 3. x)

Tato ukázka demonstruje použití middleware pro kompresi odpovědí HTTP pomocí ASP.NET Core 3. x. Ukázka ukazuje Brotli a vlastní zprostředkovatele komprese pro textové a obrázkové odpovědi a ukazuje, jak přidat typ MIME pro kompresi. Ukázku ASP.NET Core 2. x najdete v tématu [ukázková aplikace pro kompresi odpovědí (ASP.NET Core 2. x)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x).

## <a name="examples-in-this-sample"></a>Příklady v této ukázce

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum odpověď na textové soubory v 2 044 bajtech, které se komprimují do ~ 979 bajtů.
    * **/testfile1kb.txt** -text response v 1 033 bajtech, které se komprimují do ~ 36 bajtů.
    * **/trickle** – odpověď byla vydávána jako jeden znak v intervalu 1 sekund.
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum odpověď na textové soubory v 2 044 bajtech, které se komprimují do ~ 927 bajtů.
    * **/testfile1kb.txt** -text response v 1 033 bajtech, které se komprimují do ~ 47 bajtů.
    * **/trickle** – odpověď byla vydávána jako jeden znak v intervalu 1 sekund.
  * `image/svg+xml`
    * **/banner.SVG** – odpověď na obrázek ve formátu SVG (Scalable Vector Graphics) na 9 707 bajtů, která se zkomprimuje na ~ 4 459 bajtů.
* `CustomCompressionProvider`<br>Ukazuje, jak implementovat vlastního zprostředkovatele komprese pro použití s middlewarem.

Když požadavek zahrnuje hlavičku `Accept-Encoding` a komprese odezvy je úspěšná, middleware automaticky přidá hlavičku `Vary: Accept-Encoding` do odpovědi. Hlavička `Vary` řídí ukládání do mezipaměti, aby bylo možné uchovávat více kopií odpovědi na základě alternativních hodnot `Accept-Encoding`, takže komprimovaná (gzip nebo Brotli) i nekomprimovaná verze jsou uloženy v mezipamětech pro systémy, které mohou buď přijmout komprimovanou, nebo nekomprimovanou odpověď.

## <a name="use-the-sample"></a>Použití ukázky

1. Vytvořte požadavek pomocí [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)nebo [post](https://www.getpostman.com/) do aplikace bez hlavičky `Accept-Encoding` a poznamenejte si datovou část odpovědi, velikost odpovědi a hlavičky odpovědí.
1. Přidejte hlavičku `Accept-Encoding: br` nebo `Accept-Encoding: gzip` a poznamenejte si velikost komprimované odpovědi a hlavičky odpovědi. Velikost odpovědi se zmenší a middleware `Content-Encoding` hlavička je zahrnuta, což značí, že došlo k kompresi pomocí gzip nebo Brotli. Když se podíváte na text odpovědi pro odpověď Lorem na Ipsum nebo **testfile1kb. txt** , uvidíte, že je text komprimovaný a nedá se číst.
1. Přidejte hlavičku `Accept-Encoding: mycustomcompression` a poznamenejte si hlavičky odpovědi. `CustomCompressionProvider` je prázdná implementace, která ve skutečnosti nekomprimuje odpověď, ale můžete vytvořit vlastní obálku kompresního streamu pro metodu `CreateStream()`.
