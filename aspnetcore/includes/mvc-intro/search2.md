---
ms.openlocfilehash: b90767ce8e3d703cc5e3144feb07bdf5cd519775
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58207884"
---
<!--
[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

![Index view](~/tutorials/first-mvc-app/search/_static/ghost.png)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

--> 

<span data-ttu-id="17aee-101">Předchozí `Index` metody:</span><span class="sxs-lookup"><span data-stu-id="17aee-101">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_1stSearch)]

<span data-ttu-id="17aee-102">Aktualizovaný `Index` metodu s `id` parametr:</span><span class="sxs-lookup"><span data-stu-id="17aee-102">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_SearchID)]

<span data-ttu-id="17aee-103">Nyní lze předat název vyhledávání jako data trasy (segment adresy URL) místo jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="17aee-103">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Index zobrazení na mapách slovo ghost, přidá do adresy Url a vrácené film seznam dvou filmy, Ghostbusters a Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="17aee-105">Ale nemůžete očekávat, že uživatelům změnit adresu URL pokaždé, když chtějí hledat videa.</span><span class="sxs-lookup"><span data-stu-id="17aee-105">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="17aee-106">Takže teď přidáte prvky uživatelského rozhraní, aby to pomohl ostatním filtrovat videa.</span><span class="sxs-lookup"><span data-stu-id="17aee-106">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="17aee-107">Pokud jste změnili podpis `Index` metody testování jak předat trasy vázané `ID` parametr, změnit zpět tak, že vyžaduje parametr s názvem `searchString`:</span><span class="sxs-lookup"><span data-stu-id="17aee-107">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_1stSearch)]

<span data-ttu-id="17aee-108">Otevřít *Views/Movies/Index.cshtml* a přidejte `<form>` značek, jejichž přehled najdete níže:</span><span class="sxs-lookup"><span data-stu-id="17aee-108">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="17aee-109">Kód HTML `<form>` označení používá [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms), takže při odeslání formuláře řetězec filtru je odeslána do `Index` akce kontroleru videa.</span><span class="sxs-lookup"><span data-stu-id="17aee-109">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="17aee-110">Uložte změny a pak test filtru.</span><span class="sxs-lookup"><span data-stu-id="17aee-110">Save your changes and then test the filter.</span></span>

![Index zobrazení na mapách slovo ghost, zadaný do textového pole Název filtru](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="17aee-112">Neexistuje žádná `[HttpPost]` přetížení `Index` metody, jako jste očekávali.</span><span class="sxs-lookup"><span data-stu-id="17aee-112">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="17aee-113">Není nutné, protože metoda není změněn stav aplikace, stačí filtrování dat.</span><span class="sxs-lookup"><span data-stu-id="17aee-113">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="17aee-114">Můžete přidat následující `[HttpPost] Index` metody.</span><span class="sxs-lookup"><span data-stu-id="17aee-114">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="17aee-115">`notUsed` Parametr se používá k vytvoření přetížení pro `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="17aee-115">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="17aee-116">Budeme mluvit o, který později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="17aee-116">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="17aee-117">Pokud chcete přidat tuto metodu, by odpovídala původce volání akce `[HttpPost] Index` metody a `[HttpPost] Index` spustili byste metodu, jak je znázorněno na následujícím obrázku.</span><span class="sxs-lookup"><span data-stu-id="17aee-117">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Okno prohlížeče se odezvy aplikací z indexu HttpPost: filtr na ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="17aee-119">Ale i v případě, že přidáte to `[HttpPost]` verzi `Index` metoda, existuje omezení v tom, jak to vše implementován.</span><span class="sxs-lookup"><span data-stu-id="17aee-119">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="17aee-120">Představte si, že chcete konkrétní hledání (záložky) nebo chcete poslat odkaz s přáteli, mohou kliknout, chcete-li zobrazit stejné filtrovaný seznam videa.</span><span class="sxs-lookup"><span data-stu-id="17aee-120">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="17aee-121">Všimněte si, že adresa URL pro odeslání požadavku HTTP POST je stejný jako adresu URL pro požadavek na získání (localhost:xxxxx/filmy/Index) – není k dispozici žádné informace o vyhledávání v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="17aee-121">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:xxxxx/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="17aee-122">Vyhledávací řetězec informace jsou odeslány na server jako [tvoří pole hodnota](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span><span class="sxs-lookup"><span data-stu-id="17aee-122">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="17aee-123">Ověřte, že pomocí nástroje pro vývojáře v prohlížeči nebo vynikající [nástroj Fiddler](http://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="17aee-123">You can verify that with the browser Developer tools or the excellent [Fiddler tool](http://www.telerik.com/fiddler).</span></span> <span data-ttu-id="17aee-124">Následující obrázek ukazuje Developer tools pro prohlížeč Chrome:</span><span class="sxs-lookup"><span data-stu-id="17aee-124">The image below shows the Chrome browser Developer tools:</span></span>

![Karta síť vývojářských nástrojů v Microsoft Edge zobrazuje text požadavku s hodnotou hledaný_řetězec ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="17aee-126">Parametr hledání můžete zobrazit a [XSRF](xref:security/anti-request-forgery) tokenu v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="17aee-126">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="17aee-127">Všimněte si, jak je uvedeno v předchozím kurzu [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms) generuje [XSRF](xref:security/anti-request-forgery) token proti padělání.</span><span class="sxs-lookup"><span data-stu-id="17aee-127">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="17aee-128">Jsme nejsou úpravu dat, proto nepotřebujeme ověřit token v metodě kontroleru.</span><span class="sxs-lookup"><span data-stu-id="17aee-128">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="17aee-129">Protože parametr hledání v textu požadavku a nikoliv adresu URL, nelze zachytit těchto hledání informací na záložku nebo sdílet s ostatními.</span><span class="sxs-lookup"><span data-stu-id="17aee-129">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="17aee-130">Opravíme to tak, že zadáte, by měl být požadavek `HTTP GET`.</span><span class="sxs-lookup"><span data-stu-id="17aee-130">We'll fix this by specifying the request should be `HTTP GET`.</span></span>
