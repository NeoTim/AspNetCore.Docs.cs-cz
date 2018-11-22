# <a name="aspnet-core-url-rewriting-sample-aspnet-core-2x"></a>Přepisování adres ukázka URL ASP.NET Core (ASP.NET Core 2.x)

Tato ukázka ilustruje použití ASP.NET Core 2.x Middleware pro přepis adres URL. Aplikace ukazuje adresu URL pro přesměrování a možnosti přepisování adres URL.

Při spuštění ukázky, které nejsou soubory odpovědí návratová adresa URL přepsaný nebo přesměrované, při použití některé z pravidel na adrese URL žádosti. Pro příklady souborů XML a text Middleware statické soubory slouží soubor po požadavku na adresu URL je přepsán middlewarem.

## <a name="examples-in-this-sample"></a>Příklady v této ukázce

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - Stavový kód úspěchu: 302 (Found)
  - Příklad (přesměrování): **/redirect-rule / {capture_group}** k **/redirected/ {capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - Stavový kód úspěchu: 200 (OK)
  - Příklad (revize): **/rewrite-rule / {capture_group_1} / {capture_group_2}** k **/ přepsán? var1 = {capture_group_1} & var2 = {capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - Stavový kód úspěchu: 302 (Found)
  - Příklad (přesměrování): **/apache-mod-rules-redirect / {capture_group}** k **/ přesměrováno? id = {capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - Stavový kód úspěchu: 200 (OK)
  - Příklad (revize): **/iis-rules-rewrite / {capture_group}** k **/ přepsán? id = {capture_group}**
* `Add(RedirectXmlFileRequests)`
  - Stavový kód úspěchu: 301 (trvale přesunuto)
  - Příklad (přesměrování): **/file.xml** k **/xmlfiles/file.xml**
* `Add(RewriteTextFileRequests)`
  - Stavový kód úspěchu: 200 (OK)
  - Příklad (revize): **/some_file.txt** k **/file.txt**
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - Stavový kód úspěchu: 301 (trvale přesunuto)
  - Příklad (přesměrování): **/image.png** k **/png-images/image.png**
  - Příklad (přesměrování): **/image.jpg** k **/jpg-images/image.jpg**

## <a name="use-a-physicalfileprovider"></a>Použít PhysicalFileProvider

Můžete také získat `IFileProvider` tak, že vytvoříte `PhysicalFileProvider` má být předán `AddApacheModRewrite()` a `AddIISUrlRewrite()` metody:

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>Zabezpečené rozšíření přesměrování

Tato ukázka obsahuje `WebHostBuilder` konfigurace pro aplikace pro použití adresy URL (`https://localhost:5001`, `https://localhost`) a testovací certifikát (*testCert.pfx*) pomáhat při zkoumání metod zabezpečeného přesměrování. Pokud už server má port 443 přiřazené nebo používá, `https://localhost` příklad nefunguje&mdash;odebrat `ListenOptions` pro port 443 v `CreateWebHostBuilder` metodu *Program.cs* souborů nebo zrušení vazby portu 443 na Server, aby Kestrel mohl použít port.

| Metoda                           | Stavový kód |    Port    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | Null (465) |
| `.AddRedirectToHttps()`          |     302     | Null (465) |
| `.AddRedirectToHttps(301)`       |     301     | Null (465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
