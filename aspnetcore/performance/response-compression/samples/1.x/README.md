# <a name="response-compression-sample-application-aspnet-core-1x"></a>Odpověď komprese ukázkovou aplikaci (ASP.NET Core 1.x)

Tento příklad ukazuje použití metody ASP.NET Core 1.x do komprimovaly odpovědi HTTP pro Middleware pro kompresi odpovědí. Vzorek ukazuje Gzip a komprese Vlastní zprostředkovatelé pro textové a obrázkové odpovědi a ukazuje, jak přidat typ MIME pro kompresi. ASP.NET Core 2.x ukázku najdete v tématu [odpovědi komprese ukázkovou aplikaci (ASP.NET Core 2.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x).

## <a name="examples-in-this-sample"></a>Příklady v této ukázce

* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum textový soubor odezvy 2,044 bajtů, které se komprimují 927 bajtů
    * **/testfile1kb.txt** -text souboru odezvy 1,033 bajtů, které se komprimují 47 bajtů
    * **/ skapat** – odpovědi na 1 sekundových intervalech vydaný jako jednotlivé znaky
  * `image/svg+xml`
    * **/Banner.SVG** – odpověď obrázků A grafiky SVG (Scalable Vector) 9,707 bajtů, které se komprimují 4,459 bajtů
* `CustomCompressionProvider`<br>Ukazuje, jak implementovat vlastní komprese poskytovatele pro použití s middlewarem

Pokud požadavek obsahuje `Accept-Encoding` záhlaví, ukázka přidá `Vary: Accept-Encoding` hlavičky odpovědi. `Vary` Nastaví záhlaví mezipaměti k udržování více kopií v reakci na alternativní hodnoty `Accept-Encoding`, takže komprimované (gzip) i nekomprimovaných verze jsou uloženy v mezipaměti pro systémy, které můžete buď přijmout komprimované nebo nekomprimovaný odpovědi.

## <a name="using-the-sample"></a>Pomocí ukázky

1. Vytvořit žádost o prostřednictvím [Fiddleru](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), nebo [Postman](https://www.getpostman.com/) k aplikaci bez `Accept-Encoding` záhlaví a Všimněte si datové části odpovědi velikost odpovědi a hlavičky odpovědi.
1. Přidat `Accept-Encoding: gzip` záhlaví a poznamenejte si velikost komprimovaných odpovědi a hlaviček odpovědí. Zobrazí velikost odpovědi vyřadit a `Content-Encoding: gzip` ukázkové aplikace je součástí hlavičky odpovědi. Když se podíváte na datovou část odpovědi pro Lorem Ipsum nebo **testfile1kb.txt** odpovědi, uvidíte, že text je komprimovaná a nejde přečíst.
1. Přidat `Accept-Encoding: mycustomcompression` záhlaví a poznamenejte si hlavičky odpovědi. `CustomCompressionProvider` Je prázdná implementace, která není ve skutečnosti komprimovat odpověď, ale můžete vytvořit vlastní kompresi datového proudu obálku pro `CreateStream()` metody.
