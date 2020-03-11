# <a name="aspnet-core-url-rewriting-sample"></a>Ukázka přepisování adresy URL ASP.NET Core

Tato ukázka ukazuje použití middlewaru přepisu adresy URL ASP.NET Core. Aplikace ukazuje přesměrování adresy URL a možnosti přepisování adresy URL.

Pokud se spustí ukázka, nesouborové odpovědi vrátí přepsanou nebo přesměrovanou adresu URL, pokud se jedno z pravidel použije na adresu URL požadavku. Příklady XML a textový soubor, soubor middleware statického souboru slouží po přepsání adresy URL pro middleware.

## <a name="examples-in-this-sample"></a>Příklady v této ukázce

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - Stavový kód úspěchu: 302 (našlo se)
  - Příklad (přesměrování): **/redirect-rule/{capture_group}** na **/Redirected/{capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - Stavový kód úspěchu: 200 (OK)
  - Příklad (přepsání): **/rewrite-rule/{capture_group_1}/{capture_group_2}** na **/rewritten? var1 = {capture_group_1} & var2 = {capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - Stavový kód úspěchu: 302 (našlo se)
  - Příklad (přesměrování): **/apache-mod-rules-redirect/{capture_group}** na **/Redirected? id = {capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - Stavový kód úspěchu: 200 (OK)
  - Příklad (přepsání): **/iis-rules-rewrite/{capture_group}** na **/rewritten? id = {capture_group}**
* `Add(RedirectXmlFileRequests)`
  - Stavový kód úspěchu: 301 (trvale přesunuto)
  - Příklad (přesměrování): **/File.XML** na **/xmlfiles/File.XML**
* `Add(RewriteTextFileRequests)`
  - Stavový kód úspěchu: 200 (OK)
  - Příklad (přepis): **/some_file. txt** pro **/File.txt**
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - Stavový kód úspěchu: 301 (trvale přesunuto)
  - Příklad (přesměrování): **/image.png** na **/PNG-images/image.png**
  - Příklad (přesměrování): **/image.jpg** na **/jpg-images/image.jpg**

## <a name="use-a-physicalfileprovider"></a>Použití PhysicalFileProvider

Můžete také získat `IFileProvider` vytvořením `PhysicalFileProvider`, který se předává do `AddApacheModRewrite()` a `AddIISUrlRewrite()`ch metod:

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>Rozšíření zabezpečení přesměrování

Tato ukázka zahrnuje konfiguraci `WebHostBuilder` pro aplikaci, aby používala adresy URL (`https://localhost:5001`, `https://localhost`) a testovací certifikát (*testCert. pfx*), která vám pomůže prozkoumat metody zabezpečeného přesměrování. Pokud má server již port 443 přiřazen nebo používán, `https://localhost` příklad nefunguje&mdash;odeberte `ListenOptions` pro port 443 v metodě `CreateWebHostBuilder` souboru *program.cs* nebo zrušte vazbu portu 443 na serveru tak, aby Kestrel mohl použít port.

| Metoda                           | Stavový kód |    Port    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | null (465) |
| `.AddRedirectToHttps()`          |     302     | null (465) |
| `.AddRedirectToHttps(301)`       |     301     | null (465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
