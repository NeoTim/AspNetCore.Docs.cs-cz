
## <a name="test-the-app"></a><span data-ttu-id="9cf7a-101">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="9cf7a-101">Test the app</span></span>

* <span data-ttu-id="9cf7a-102">Spusťte aplikaci a klepněte **Mvc Movie** odkaz.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-102">Run the app and tap the **Mvc Movie** link.</span></span>
* <span data-ttu-id="9cf7a-103">Klepněte **vytvořit nový** propojit a vytvořit film.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-103">Tap the **Create New** link and create a movie.</span></span>

  ![Vytvořit zobrazení s poli pro genre, ceny, datum vydání a název](~/tutorials/first-mvc-app/adding-model/_static/movies.png)

* <span data-ttu-id="9cf7a-105">Není možné zadávat desetinné tečky a čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-105">You may not be able to enter decimal points or commas in the `Price` field.</span></span> <span data-ttu-id="9cf7a-106">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") pro desetinné čárky a USA retweetovat neanglické formáty kalendářního data, je nutné provést kroky aplikaci poslali do světa.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-106">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="9cf7a-107">Zobrazit [ https://github.com/aspnet/Docs/issues/4076 ](https://github.com/aspnet/Docs/issues/4076) a [další prostředky](#additional-resources) Další informace.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-107">See [https://github.com/aspnet/Docs/issues/4076](https://github.com/aspnet/Docs/issues/4076) and [Additional resources](#additional-resources) for more information.</span></span> <span data-ttu-id="9cf7a-108">Teď zadejte celá čísla, jako je 10.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-108">For now, just enter whole numbers like 10.</span></span>

<a name="displayformatdatelocal"></a>

* <span data-ttu-id="9cf7a-109">V některých národní prostředí je třeba zadat formát data.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-109">In some locales you need to specify the date format.</span></span> <span data-ttu-id="9cf7a-110">Viz následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-110">See the highlighted code below.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateFormat.cs?name=snippet_1&highlight=2,10)]

<span data-ttu-id="9cf7a-111">Budeme mluvit o `DataAnnotations` později v tomto návodu.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-111">We'll talk about `DataAnnotations` later in the tutorial.</span></span>

<span data-ttu-id="9cf7a-112">Klepnutím na **vytvořit** způsobí, že formulář, který se má publikovat na server, kde je video informace uloženy v databázi.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-112">Tapping **Create** causes the form to be posted to the server, where the movie information is saved in a database.</span></span> <span data-ttu-id="9cf7a-113">Aplikace přesměruje */Movies* adresu URL, kde se zobrazí informace o nově vytvořený video.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-113">The app redirects to the */Movies* URL, where the newly created movie information is displayed.</span></span>

![Výpis film zobrazení zobrazující nově vytvořený filmy](~/tutorials/first-mvc-app/adding-model/_static/h.png)

<span data-ttu-id="9cf7a-115">Vytvořte několik další položky video.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-115">Create a couple more movie entries.</span></span> <span data-ttu-id="9cf7a-116">Zkuste **upravit**, **podrobnosti**, a **odstranit** odkazy, které jsou všechny funkční.</span><span class="sxs-lookup"><span data-stu-id="9cf7a-116">Try the **Edit**, **Details**, and **Delete** links, which are all functional.</span></span>
