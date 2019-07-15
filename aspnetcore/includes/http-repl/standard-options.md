* `-F|--no-formatting`

  <span data-ttu-id="d156f-101">Příznak, jejichž přítomnost potlačí formátování odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="d156f-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="d156f-102">Nastaví hlavičku požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d156f-102">Sets an HTTP request header.</span></span> <span data-ttu-id="d156f-103">Podporují se následující formáty dvě hodnoty:</span><span class="sxs-lookup"><span data-stu-id="d156f-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  <span data-ttu-id="d156f-104">Určuje soubor, do které by měly být napsány celé odpovědi protokolu HTTP (včetně záhlaví a text).</span><span class="sxs-lookup"><span data-stu-id="d156f-104">Specifies a file to which the entire HTTP response (including headers and body) should be written.</span></span> <span data-ttu-id="d156f-105">Například, `--response "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="d156f-105">For example, `--response "C:\response.txt"`.</span></span> <span data-ttu-id="d156f-106">Soubor je vytvořen, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="d156f-106">The file is created if it doesn't exist.</span></span>

* `--response:body`

  <span data-ttu-id="d156f-107">Určuje soubor, do které by měly být napsány textu odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="d156f-107">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="d156f-108">Například, `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="d156f-108">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="d156f-109">Soubor je vytvořen, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="d156f-109">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="d156f-110">Určuje soubor, do které by měly být napsány hlavičky HTTP odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d156f-110">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="d156f-111">Například, `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="d156f-111">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="d156f-112">Soubor je vytvořen, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="d156f-112">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="d156f-113">Příznak, jejichž přítomnost umožňuje vysílání datového proudu odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="d156f-113">A flag whose presence enables streaming of the HTTP response.</span></span>
