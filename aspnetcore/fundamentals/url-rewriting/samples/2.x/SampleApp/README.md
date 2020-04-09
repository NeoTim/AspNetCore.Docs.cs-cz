# <a name="aspnet-core-url-rewriting-sample"></a><span data-ttu-id="f8888-101">ukázka přepisování ASP.NET url</span><span class="sxs-lookup"><span data-stu-id="f8888-101">ASP.NET Core URL Rewriting Sample</span></span>

<span data-ttu-id="f8888-102">Tato ukázka ilustruje použití ASP.NET základní url přepisování Middleware.</span><span class="sxs-lookup"><span data-stu-id="f8888-102">This sample illustrates usage of ASP.NET Core URL Rewriting Middleware.</span></span> <span data-ttu-id="f8888-103">Aplikace ukazuje možnosti přesměrování adres URL a přepisování adres URL.</span><span class="sxs-lookup"><span data-stu-id="f8888-103">The app demonstrates URL redirect and URL rewriting options.</span></span>

<span data-ttu-id="f8888-104">Při spuštění ukázky vrátí nesouborové odpovědi přepsanou nebo přesměrovanou adresu URL, pokud je na adresu URL požadavku použito jedno z pravidel.</span><span class="sxs-lookup"><span data-stu-id="f8888-104">When running the sample, non-file responses return the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span> <span data-ttu-id="f8888-105">Pro příklady XML a textového souboru, Statická soubor Middleware slouží soubor po požadavku URL je přepsán middleware.</span><span class="sxs-lookup"><span data-stu-id="f8888-105">For the XML and text file examples, Static File Middleware serves the file after the request URL is rewritten by the middleware.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="f8888-106">Příklady v této ukázce</span><span class="sxs-lookup"><span data-stu-id="f8888-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - <span data-ttu-id="f8888-107">Stavový kód úspěchu: 302 (Nalezeno)</span><span class="sxs-lookup"><span data-stu-id="f8888-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="f8888-108">Příklad (přesměrování): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span><span class="sxs-lookup"><span data-stu-id="f8888-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="f8888-109">Stavový kód úspěchu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="f8888-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="f8888-110">Příklad (přepis): **/rewrite-rule/{capture_group_1}/{capture_group_2}** na **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="f8888-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="f8888-111">Stavový kód úspěchu: 302 (Nalezeno)</span><span class="sxs-lookup"><span data-stu-id="f8888-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="f8888-112">Příklad (přesměrování): **/apache-mod-rules-redirect/{capture_group}** na **/redirected?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="f8888-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="f8888-113">Stavový kód úspěchu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="f8888-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="f8888-114">Příklad (přepis): **/iis-rules-rewrite/{capture_group}** na **/rewritten?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="f8888-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXmlFileRequests)`
  - <span data-ttu-id="f8888-115">Stavový kód úspěchu: 301 (Trvale přesunuto)</span><span class="sxs-lookup"><span data-stu-id="f8888-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="f8888-116">Příklad (přesměrování): **/file.xml** to **/xmlfiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="f8888-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(RewriteTextFileRequests)`
  - <span data-ttu-id="f8888-117">Stavový kód úspěchu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="f8888-117">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="f8888-118">Příklad (přepsání): **/some_file.txt** to **/file.txt**</span><span class="sxs-lookup"><span data-stu-id="f8888-118">Example (rewrite): **/some_file.txt** to **/file.txt**</span></span>
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="f8888-119">Stavový kód úspěchu: 301 (Trvale přesunuto)</span><span class="sxs-lookup"><span data-stu-id="f8888-119">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="f8888-120">Příklad (přesměrování): **/image.png** to **/png-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="f8888-120">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="f8888-121">Příklad (přesměrování): **/image.jpg** na **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="f8888-121">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="use-a-physicalfileprovider"></a><span data-ttu-id="f8888-122">Použití fyzického zprostředkovatele souborů</span><span class="sxs-lookup"><span data-stu-id="f8888-122">Use a PhysicalFileProvider</span></span>

<span data-ttu-id="f8888-123">Můžete také získat `IFileProvider` vytvořením `PhysicalFileProvider` převést `AddApacheModRewrite()` do `AddIISUrlRewrite()` a metody:</span><span class="sxs-lookup"><span data-stu-id="f8888-123">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a><span data-ttu-id="f8888-124">Zabezpečené rozšíření přesměrování</span><span class="sxs-lookup"><span data-stu-id="f8888-124">Secure redirection extensions</span></span>

<span data-ttu-id="f8888-125">Tato `WebHostBuilder` ukázka zahrnuje konfiguraci pro aplikaci používat adresy URL (`https://localhost:5001`, `https://localhost`) a testovací certifikát *(testCert.pfx)* pomoci při zkoumání metody zabezpečené přesměrování.</span><span class="sxs-lookup"><span data-stu-id="f8888-125">This sample includes `WebHostBuilder` configuration for the app to use URLs (`https://localhost:5001`, `https://localhost`) and a test certificate (*testCert.pfx*) to assist in exploring the secure redirect methods.</span></span> <span data-ttu-id="f8888-126">Pokud server již má port 443 přiřazený nebo v provozu, `https://localhost` příklad nefunguje&mdash;odebrat `ListenOptions` pro port 443 v `CreateWebHostBuilder` metodě *Program.cs* souboru nebo zrušit vazbu port 443 na serveru tak, aby Kestrel můžete použít port.</span><span class="sxs-lookup"><span data-stu-id="f8888-126">If the server already has port 443 assigned or in use, the `https://localhost` example doesn't work&mdash;remove the `ListenOptions` for port 443 in the `CreateWebHostBuilder` method of the *Program.cs* file or unbind port 443 on the server so that Kestrel can use the port.</span></span>

| <span data-ttu-id="f8888-127">Metoda</span><span class="sxs-lookup"><span data-stu-id="f8888-127">Method</span></span>                           | <span data-ttu-id="f8888-128">Stavový kód</span><span class="sxs-lookup"><span data-stu-id="f8888-128">Status Code</span></span> |    <span data-ttu-id="f8888-129">Port</span><span class="sxs-lookup"><span data-stu-id="f8888-129">Port</span></span>    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     <span data-ttu-id="f8888-130">301</span><span class="sxs-lookup"><span data-stu-id="f8888-130">301</span></span>     | <span data-ttu-id="f8888-131">null (465)</span><span class="sxs-lookup"><span data-stu-id="f8888-131">null (465)</span></span> |
| `.AddRedirectToHttps()`          |     <span data-ttu-id="f8888-132">302</span><span class="sxs-lookup"><span data-stu-id="f8888-132">302</span></span>     | <span data-ttu-id="f8888-133">null (465)</span><span class="sxs-lookup"><span data-stu-id="f8888-133">null (465)</span></span> |
| `.AddRedirectToHttps(301)`       |     <span data-ttu-id="f8888-134">301</span><span class="sxs-lookup"><span data-stu-id="f8888-134">301</span></span>     | <span data-ttu-id="f8888-135">null (465)</span><span class="sxs-lookup"><span data-stu-id="f8888-135">null (465)</span></span> |
| `.AddRedirectToHttps(301, 5001)` |     <span data-ttu-id="f8888-136">301</span><span class="sxs-lookup"><span data-stu-id="f8888-136">301</span></span>     |    <span data-ttu-id="f8888-137">5001</span><span class="sxs-lookup"><span data-stu-id="f8888-137">5001</span></span>    |
