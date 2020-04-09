# <a name="aspnet-core-url-rewriting-sample"></a>ukázka přepisování ASP.NET url

Tato ukázka ilustruje použití ASP.NET základní url přepisování Middleware. Aplikace ukazuje možnosti přesměrování adres URL a přepisování adres URL.

Při spuštění ukázky vrátí nesouborové odpovědi přepsanou nebo přesměrovanou adresu URL, pokud je na adresu URL požadavku použito jedno z pravidel. Pro příklady XML a textového souboru, Statická soubor Middleware slouží soubor po požadavku URL je přepsán middleware.

## <a name="examples-in-this-sample"></a>Příklady v této ukázce

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - Stavový kód úspěchu: 302 (Nalezeno)
  - Příklad (přesměrování): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - Stavový kód úspěchu: 200 (OK)
  - Příklad (přepis): **/rewrite-rule/{capture_group_1}/{capture_group_2}** na **/rewritten?var1={capture_group_1}&var2={capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - Stavový kód úspěchu: 302 (Nalezeno)
  - Příklad (přesměrování): **/apache-mod-rules-redirect/{capture_group}** na **/redirected?id={capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - Stavový kód úspěchu: 200 (OK)
  - Příklad (přepis): **/iis-rules-rewrite/{capture_group}** na **/rewritten?id={capture_group}**
* `Add(RedirectXmlFileRequests)`
  - Stavový kód úspěchu: 301 (Trvale přesunuto)
  - Příklad (přesměrování): **/file.xml** to **/xmlfiles/file.xml**
* `Add(RewriteTextFileRequests)`
  - Stavový kód úspěchu: 200 (OK)
  - Příklad (přepsání): **/some_file.txt** to **/file.txt**
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - Stavový kód úspěchu: 301 (Trvale přesunuto)
  - Příklad (přesměrování): **/image.png** to **/png-images/image.png**
  - Příklad (přesměrování): **/image.jpg** na **/jpg-images/image.jpg**

## <a name="use-a-physicalfileprovider"></a>Použití fyzického zprostředkovatele souborů

Můžete také získat `IFileProvider` vytvořením `PhysicalFileProvider` převést `AddApacheModRewrite()` do `AddIISUrlRewrite()` a metody:

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>Zabezpečené rozšíření přesměrování

Tato `WebHostBuilder` ukázka zahrnuje konfiguraci pro aplikaci používat adresy URL (`https://localhost:5001`, `https://localhost`) a testovací certifikát *(testCert.pfx)* pomoci při zkoumání metody zabezpečené přesměrování. Pokud server již má port 443 přiřazený nebo v provozu, `https://localhost` příklad nefunguje&mdash;odebrat `ListenOptions` pro port 443 v `CreateWebHostBuilder` metodě *Program.cs* souboru nebo zrušit vazbu port 443 na serveru tak, aby Kestrel můžete použít port.

| Metoda                           | Stavový kód |    Port    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | null (465) |
| `.AddRedirectToHttps()`          |     302     | null (465) |
| `.AddRedirectToHttps(301)`       |     301     | null (465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
