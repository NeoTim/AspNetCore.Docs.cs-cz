# <a name="aspnet-core-url-rewriting-sample"></a><span data-ttu-id="5549d-101">Ukázka přepisování adresy URL ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5549d-101">ASP.NET Core URL Rewriting Sample</span></span>

<span data-ttu-id="5549d-102">Tato ukázka ukazuje použití middlewaru přepisu adresy URL ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5549d-102">This sample illustrates usage of ASP.NET Core URL Rewriting Middleware.</span></span> <span data-ttu-id="5549d-103">Aplikace ukazuje přesměrování adresy URL a možnosti přepisování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="5549d-103">The app demonstrates URL redirect and URL rewriting options.</span></span>

<span data-ttu-id="5549d-104">Pokud se spustí ukázka, nesouborové odpovědi vrátí přepsanou nebo přesměrovanou adresu URL, pokud se jedno z pravidel použije na adresu URL požadavku.</span><span class="sxs-lookup"><span data-stu-id="5549d-104">When running the sample, non-file responses return the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span> <span data-ttu-id="5549d-105">Příklady XML a textový soubor, soubor middleware statického souboru slouží po přepsání adresy URL pro middleware.</span><span class="sxs-lookup"><span data-stu-id="5549d-105">For the XML and text file examples, Static File Middleware serves the file after the request URL is rewritten by the middleware.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="5549d-106">Příklady v této ukázce</span><span class="sxs-lookup"><span data-stu-id="5549d-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - <span data-ttu-id="5549d-107">Stavový kód úspěchu: 302 (našlo se)</span><span class="sxs-lookup"><span data-stu-id="5549d-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="5549d-108">Příklad (přesměrování): **/redirect-rule/{capture_group}** na **/Redirected/{capture_group}**</span><span class="sxs-lookup"><span data-stu-id="5549d-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="5549d-109">Stavový kód úspěchu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="5549d-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="5549d-110">Příklad (přepsání): **/rewrite-rule/{capture_group_1}/{capture_group_2}** na **/rewritten? var1 = {capture_group_1} & var2 = {capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="5549d-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="5549d-111">Stavový kód úspěchu: 302 (našlo se)</span><span class="sxs-lookup"><span data-stu-id="5549d-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="5549d-112">Příklad (přesměrování): **/apache-mod-rules-redirect/{capture_group}** na **/Redirected? id = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="5549d-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="5549d-113">Stavový kód úspěchu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="5549d-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="5549d-114">Příklad (přepsání): **/iis-rules-rewrite/{capture_group}** na **/rewritten? id = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="5549d-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXmlFileRequests)`
  - <span data-ttu-id="5549d-115">Stavový kód úspěchu: 301 (trvale přesunuto)</span><span class="sxs-lookup"><span data-stu-id="5549d-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="5549d-116">Příklad (přesměrování): **/File.XML** na **/xmlfiles/File.XML**</span><span class="sxs-lookup"><span data-stu-id="5549d-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(RewriteTextFileRequests)`
  - <span data-ttu-id="5549d-117">Stavový kód úspěchu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="5549d-117">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="5549d-118">Příklad (přepis): **/some_file. txt** pro **/File.txt**</span><span class="sxs-lookup"><span data-stu-id="5549d-118">Example (rewrite): **/some_file.txt** to **/file.txt**</span></span>
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="5549d-119">Stavový kód úspěchu: 301 (trvale přesunuto)</span><span class="sxs-lookup"><span data-stu-id="5549d-119">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="5549d-120">Příklad (přesměrování): **/image.png** na **/PNG-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="5549d-120">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="5549d-121">Příklad (přesměrování): **/image.jpg** na **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="5549d-121">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="use-a-physicalfileprovider"></a><span data-ttu-id="5549d-122">Použití PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="5549d-122">Use a PhysicalFileProvider</span></span>

<span data-ttu-id="5549d-123">Můžete také získat `IFileProvider` vytvořením `PhysicalFileProvider`, který se předává do `AddApacheModRewrite()` a `AddIISUrlRewrite()`ch metod:</span><span class="sxs-lookup"><span data-stu-id="5549d-123">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a><span data-ttu-id="5549d-124">Rozšíření zabezpečení přesměrování</span><span class="sxs-lookup"><span data-stu-id="5549d-124">Secure redirection extensions</span></span>

<span data-ttu-id="5549d-125">Tato ukázka zahrnuje konfiguraci `WebHostBuilder` pro aplikaci, aby používala adresy URL (`https://localhost:5001`, `https://localhost`) a testovací certifikát (*testCert. pfx*), která vám pomůže prozkoumat metody zabezpečeného přesměrování.</span><span class="sxs-lookup"><span data-stu-id="5549d-125">This sample includes `WebHostBuilder` configuration for the app to use URLs (`https://localhost:5001`, `https://localhost`) and a test certificate (*testCert.pfx*) to assist in exploring the secure redirect methods.</span></span> <span data-ttu-id="5549d-126">Pokud má server již port 443 přiřazen nebo používán, `https://localhost` příklad nefunguje&mdash;odeberte `ListenOptions` pro port 443 v metodě `CreateWebHostBuilder` souboru *program.cs* nebo zrušte vazbu portu 443 na serveru tak, aby Kestrel mohl použít port.</span><span class="sxs-lookup"><span data-stu-id="5549d-126">If the server already has port 443 assigned or in use, the `https://localhost` example doesn't work&mdash;remove the `ListenOptions` for port 443 in the `CreateWebHostBuilder` method of the *Program.cs* file or unbind port 443 on the server so that Kestrel can use the port.</span></span>

| <span data-ttu-id="5549d-127">Metoda</span><span class="sxs-lookup"><span data-stu-id="5549d-127">Method</span></span>                           | <span data-ttu-id="5549d-128">Stavový kód</span><span class="sxs-lookup"><span data-stu-id="5549d-128">Status Code</span></span> |    <span data-ttu-id="5549d-129">Port</span><span class="sxs-lookup"><span data-stu-id="5549d-129">Port</span></span>    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     <span data-ttu-id="5549d-130">301</span><span class="sxs-lookup"><span data-stu-id="5549d-130">301</span></span>     | <span data-ttu-id="5549d-131">null (465)</span><span class="sxs-lookup"><span data-stu-id="5549d-131">null (465)</span></span> |
| `.AddRedirectToHttps()`          |     <span data-ttu-id="5549d-132">302</span><span class="sxs-lookup"><span data-stu-id="5549d-132">302</span></span>     | <span data-ttu-id="5549d-133">null (465)</span><span class="sxs-lookup"><span data-stu-id="5549d-133">null (465)</span></span> |
| `.AddRedirectToHttps(301)`       |     <span data-ttu-id="5549d-134">301</span><span class="sxs-lookup"><span data-stu-id="5549d-134">301</span></span>     | <span data-ttu-id="5549d-135">null (465)</span><span class="sxs-lookup"><span data-stu-id="5549d-135">null (465)</span></span> |
| `.AddRedirectToHttps(301, 5001)` |     <span data-ttu-id="5549d-136">301</span><span class="sxs-lookup"><span data-stu-id="5549d-136">301</span></span>     |    <span data-ttu-id="5549d-137">5001</span><span class="sxs-lookup"><span data-stu-id="5549d-137">5001</span></span>    |
