* `-F|--no-formatting`

  <span data-ttu-id="a9eb1-101">Příznak, jehož přítomnost potlačuje formátování odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9eb1-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="a9eb1-102">Nastaví hlavičku požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9eb1-102">Sets an HTTP request header.</span></span> <span data-ttu-id="a9eb1-103">Podporují se tyto dva formáty hodnot:</span><span class="sxs-lookup"><span data-stu-id="a9eb1-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  <span data-ttu-id="a9eb1-104">Určuje soubor, do kterého by se měla zapsat celá odpověď HTTP (včetně hlaviček a textu).</span><span class="sxs-lookup"><span data-stu-id="a9eb1-104">Specifies a file to which the entire HTTP response (including headers and body) should be written.</span></span> <span data-ttu-id="a9eb1-105">například `--response "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="a9eb1-105">For example, `--response "C:\response.txt"`.</span></span> <span data-ttu-id="a9eb1-106">Soubor se vytvoří, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="a9eb1-106">The file is created if it doesn't exist.</span></span>

* `--response:body`

  <span data-ttu-id="a9eb1-107">Určuje soubor, do kterého se má zapsat tělo odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9eb1-107">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="a9eb1-108">například `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="a9eb1-108">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="a9eb1-109">Soubor se vytvoří, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="a9eb1-109">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="a9eb1-110">Určuje soubor, do kterého se mají zapsat hlavičky odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9eb1-110">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="a9eb1-111">například `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="a9eb1-111">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="a9eb1-112">Soubor se vytvoří, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="a9eb1-112">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="a9eb1-113">Příznak, jehož přítomnost umožňuje streamování odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9eb1-113">A flag whose presence enables streaming of the HTTP response.</span></span>
