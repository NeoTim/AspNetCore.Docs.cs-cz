---
uid: web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
title: Začínáme s rozhraním ASP.NET Web API 2 (C#)
author: MikeWasson
description: Protokol HTTP není jen pro poskytovat webové stránky. Je také výkonnou platformu pro vytváření rozhraní API, která zpřístupňují služby a data. HTTP je jednoduchý, flexibilní a ubiq...
ms.author: riande
ms.date: 11/28/2017
msc.legacyurl: /web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
msc.type: authoredcontent
ms.openlocfilehash: 7bec95af4532535f0d620bfe6862958907466874
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444256"
---
<a name="get-started-with-aspnet-web-api-2-c"></a><span data-ttu-id="1925f-105">Začínáme s rozhraním ASP.NET Web API 2 (C#)</span><span class="sxs-lookup"><span data-stu-id="1925f-105">Get Started with ASP.NET Web API 2 (C#)</span></span>
====================
<span data-ttu-id="1925f-106">podle [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="1925f-106">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="1925f-107">Stáhnout dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="1925f-107">Download Completed Project</span></span>](https://code.msdn.microsoft.com/Sample-code-of-Getting-c56ccb28)

<span data-ttu-id="1925f-108">Protokol HTTP není jen pro poskytovat webové stránky.</span><span class="sxs-lookup"><span data-stu-id="1925f-108">HTTP is not just for serving up web pages.</span></span> <span data-ttu-id="1925f-109">HTTP je také výkonnou platformu pro vytváření rozhraní API, která zpřístupňují služby a data.</span><span class="sxs-lookup"><span data-stu-id="1925f-109">HTTP is also a powerful platform for building APIs that expose services and data.</span></span> <span data-ttu-id="1925f-110">HTTP je snadné, flexibilní a všudypřítomná.</span><span class="sxs-lookup"><span data-stu-id="1925f-110">HTTP is simple, flexible, and ubiquitous.</span></span> <span data-ttu-id="1925f-111">Téměř jakoukoli platformu, která si můžete představit obsahuje knihovny HTTP, takže služeb HTTP můžete oslovit širokou škálu klientů, včetně prohlížečů, mobilní zařízení a tradičních desktopových aplikací.</span><span class="sxs-lookup"><span data-stu-id="1925f-111">Almost any platform that you can think of has an HTTP library, so HTTP services can reach a broad range of clients, including browsers, mobile devices, and traditional desktop applications.</span></span>

<span data-ttu-id="1925f-112">Rozhraní ASP.NET Web API je architektura určená k vytváření webových rozhraní API jako nadstavby rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="1925f-112">ASP.NET Web API is a framework for building web APIs on top of the .NET Framework.</span></span> <span data-ttu-id="1925f-113">V tomto kurzu použijete rozhraní ASP.NET Web API k vytvoření webového rozhraní API, který vrátí seznam produktů.</span><span class="sxs-lookup"><span data-stu-id="1925f-113">In this tutorial, you will use ASP.NET Web API to create a web API that returns a list of products.</span></span>

## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="1925f-114">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="1925f-114">Software versions used in the tutorial</span></span>

- [<span data-ttu-id="1925f-115">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="1925f-115">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
- <span data-ttu-id="1925f-116">Webové rozhraní API 2</span><span class="sxs-lookup"><span data-stu-id="1925f-116">Web API 2</span></span>

<span data-ttu-id="1925f-117">Zobrazit [vytvoření webového rozhraní API pomocí ASP.NET Core a Visual Studio pro Windows](https://docs.microsoft.com/aspnet/core/tutorials/first-web-api) pro novější verze tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="1925f-117">See [Create a web API with ASP.NET Core and Visual Studio for Windows](https://docs.microsoft.com/aspnet/core/tutorials/first-web-api) for a newer version of this tutorial.</span></span>

## <a name="create-a-web-api-project"></a><span data-ttu-id="1925f-118">Vytvořte projekt webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="1925f-118">Create a Web API Project</span></span>

<span data-ttu-id="1925f-119">V tomto kurzu použijete rozhraní ASP.NET Web API k vytvoření webového rozhraní API, který vrátí seznam produktů.</span><span class="sxs-lookup"><span data-stu-id="1925f-119">In this tutorial, you will use ASP.NET Web API to create a web API that returns a list of products.</span></span> <span data-ttu-id="1925f-120">Front-endové webové stránky používá jQuery pro zobrazení výsledků.</span><span class="sxs-lookup"><span data-stu-id="1925f-120">The front-end web page uses jQuery to display the results.</span></span>

![](tutorial-your-first-web-api/_static/image1.png)

<span data-ttu-id="1925f-121">Spusťte sadu Visual Studio a vyberte **nový projekt** z **Start** stránky.</span><span class="sxs-lookup"><span data-stu-id="1925f-121">Start Visual Studio and select **New Project** from the **Start** page.</span></span> <span data-ttu-id="1925f-122">Nebo z **souboru** nabídce vyberte možnost **nový** a potom **projektu**.</span><span class="sxs-lookup"><span data-stu-id="1925f-122">Or, from the **File** menu, select **New** and then **Project**.</span></span>

<span data-ttu-id="1925f-123">V **šablony** vyberte **nainstalované šablony** a rozbalte **Visual C#** uzlu.</span><span class="sxs-lookup"><span data-stu-id="1925f-123">In the **Templates** pane, select **Installed Templates** and expand the **Visual C#** node.</span></span> <span data-ttu-id="1925f-124">V části **Visual C#** vyberte **webové**.</span><span class="sxs-lookup"><span data-stu-id="1925f-124">Under **Visual C#**, select **Web**.</span></span> <span data-ttu-id="1925f-125">V seznamu šablon projektu vyberte **webová aplikace ASP.NET**.</span><span class="sxs-lookup"><span data-stu-id="1925f-125">In the list of project templates, select **ASP.NET Web Application**.</span></span> <span data-ttu-id="1925f-126">Pojmenujte projekt "ProductsApp" a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="1925f-126">Name the project "ProductsApp" and click **OK**.</span></span>

![](tutorial-your-first-web-api/_static/image2.png)

<span data-ttu-id="1925f-127">V **nový projekt ASP.NET** dialogového okna, vyberte **prázdný** šablony.</span><span class="sxs-lookup"><span data-stu-id="1925f-127">In the **New ASP.NET Project** dialog, select the **Empty** template.</span></span> <span data-ttu-id="1925f-128">V části &quot;přidat složky a základní odkazy pro&quot;, zkontrolujte **webového rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="1925f-128">Under &quot;Add folders and core references for&quot;, check **Web API**.</span></span> <span data-ttu-id="1925f-129">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="1925f-129">Click **OK**.</span></span>

![](tutorial-your-first-web-api/_static/image3.png)

> [!NOTE]
> <span data-ttu-id="1925f-130">Můžete také vytvořit projekt webového rozhraní API pomocí &quot;webového rozhraní API&quot; šablony.</span><span class="sxs-lookup"><span data-stu-id="1925f-130">You can also create a Web API project using the &quot;Web API&quot; template.</span></span> <span data-ttu-id="1925f-131">Šablona webového rozhraní API používá rozhraní ASP.NET MVC pro zajištění stránek nápovědy rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1925f-131">The Web API template uses ASP.NET MVC to provide API help pages.</span></span> <span data-ttu-id="1925f-132">Používám prázdnou šablonu pro účely tohoto kurzu, protože chci ukázat webového rozhraní API bez MVC.</span><span class="sxs-lookup"><span data-stu-id="1925f-132">I'm using the Empty template for this tutorial because I want to show Web API without MVC.</span></span> <span data-ttu-id="1925f-133">Obecně platí nemusíte vědět, ASP.NET MVC pro použití webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1925f-133">In general, you don't need to know ASP.NET MVC to use Web API.</span></span>


## <a name="adding-a-model"></a><span data-ttu-id="1925f-134">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="1925f-134">Adding a Model</span></span>

<span data-ttu-id="1925f-135">A *modelu* je objekt, který představuje data ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1925f-135">A *model* is an object that represents the data in your application.</span></span> <span data-ttu-id="1925f-136">Rozhraní ASP.NET Web API můžete automaticky serializovat modelu JSON, XML nebo jiném formátu a pak napište serializovaná data do datové části zprávy s odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="1925f-136">ASP.NET Web API can automatically serialize your model to JSON, XML, or some other format, and then write the serialized data into the body of the HTTP response message.</span></span> <span data-ttu-id="1925f-137">Za předpokladu, může klienta číst formát serializace, může deserializovat objekt.</span><span class="sxs-lookup"><span data-stu-id="1925f-137">As long as a client can read the serialization format, it can deserialize the object.</span></span> <span data-ttu-id="1925f-138">Většina klientů můžete analyzovat, XML nebo JSON.</span><span class="sxs-lookup"><span data-stu-id="1925f-138">Most clients can parse either XML or JSON.</span></span> <span data-ttu-id="1925f-139">Kromě toho klienta můžete určit, jaký formát chce nastavením hlavičky Accept ve zprávě požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="1925f-139">Moreover, the client can indicate which format it wants by setting the Accept header in the HTTP request message.</span></span>

<span data-ttu-id="1925f-140">Začněme vytvořením jednoduchého modelu, který představuje produkt.</span><span class="sxs-lookup"><span data-stu-id="1925f-140">Let's start by creating a simple model that represents a product.</span></span>

<span data-ttu-id="1925f-141">Pokud již není Průzkumník řešení viditelný, klikněte na tlačítko **zobrazení** nabídky a vybereme **Průzkumníka řešení**.</span><span class="sxs-lookup"><span data-stu-id="1925f-141">If Solution Explorer is not already visible, click the **View** menu and select **Solution Explorer**.</span></span> <span data-ttu-id="1925f-142">V Průzkumníku řešení klikněte pravým tlačítkem na složku modely.</span><span class="sxs-lookup"><span data-stu-id="1925f-142">In Solution Explorer, right-click the Models folder.</span></span> <span data-ttu-id="1925f-143">V místní nabídce vyberte **přidat** vyberte **třídy**.</span><span class="sxs-lookup"><span data-stu-id="1925f-143">From the context menu, select **Add** then select **Class**.</span></span>

![](tutorial-your-first-web-api/_static/image4.png)

<span data-ttu-id="1925f-144">Název třídy &quot;produktu&quot;.</span><span class="sxs-lookup"><span data-stu-id="1925f-144">Name the class &quot;Product&quot;.</span></span> <span data-ttu-id="1925f-145">Přidejte následující vlastnosti pro `Product` třídy.</span><span class="sxs-lookup"><span data-stu-id="1925f-145">Add the following properties to the `Product` class.</span></span>

[!code-csharp[Main](tutorial-your-first-web-api/samples/sample1.cs)]

## <a name="adding-a-controller"></a><span data-ttu-id="1925f-146">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="1925f-146">Adding a Controller</span></span>

<span data-ttu-id="1925f-147">V rozhraní Web API *řadič* je objekt, který zpracovává požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="1925f-147">In Web API, a *controller* is an object that handles HTTP requests.</span></span> <span data-ttu-id="1925f-148">Přidáme kontroler, který může vrátit seznam produktů nebo jednoho produktu určeným ID.</span><span class="sxs-lookup"><span data-stu-id="1925f-148">We'll add a controller that can return either a list of products or a single product specified by ID.</span></span>

> [!NOTE]
> <span data-ttu-id="1925f-149">Pokud jste použili technologie ASP.NET MVC, jste obeznámeni s řadiči.</span><span class="sxs-lookup"><span data-stu-id="1925f-149">If you have used ASP.NET MVC, you are already familiar with controllers.</span></span> <span data-ttu-id="1925f-150">Kontrolerů webového rozhraní API jsou podobné řadiče MVC, ale dědit **objektu ApiController** místo na třídě **řadič** třídy.</span><span class="sxs-lookup"><span data-stu-id="1925f-150">Web API controllers are similar to MVC controllers, but inherit the **ApiController** class instead of the **Controller** class.</span></span>

<span data-ttu-id="1925f-151">V **Průzkumníka řešení**, klikněte pravým tlačítkem na složku řadiče.</span><span class="sxs-lookup"><span data-stu-id="1925f-151">In **Solution Explorer**, right-click the Controllers folder.</span></span> <span data-ttu-id="1925f-152">Vyberte **přidat** a pak vyberte **řadič**.</span><span class="sxs-lookup"><span data-stu-id="1925f-152">Select **Add** and then select **Controller**.</span></span>

![](tutorial-your-first-web-api/_static/image5.png)

<span data-ttu-id="1925f-153">V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **Kontroleru webového rozhraní API – prázdný**.</span><span class="sxs-lookup"><span data-stu-id="1925f-153">In the **Add Scaffold** dialog, select **Web API Controller - Empty**.</span></span> <span data-ttu-id="1925f-154">Klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="1925f-154">Click **Add**.</span></span>

![](tutorial-your-first-web-api/_static/image6.png)

<span data-ttu-id="1925f-155">V **přidat kontroler** dialogového okna, názvu kontroleru &quot;ProductsController&quot;.</span><span class="sxs-lookup"><span data-stu-id="1925f-155">In the **Add Controller** dialog, name the controller &quot;ProductsController&quot;.</span></span> <span data-ttu-id="1925f-156">Klikněte na **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="1925f-156">Click **Add**.</span></span>

![](tutorial-your-first-web-api/_static/image7.png)

<span data-ttu-id="1925f-157">Základní kostry aplikace vytvoří soubor s názvem ProductsController.cs ve složce řadiče.</span><span class="sxs-lookup"><span data-stu-id="1925f-157">The scaffolding creates a file named ProductsController.cs in the Controllers folder.</span></span>

![](tutorial-your-first-web-api/_static/image8.png)

> [!NOTE]
> <span data-ttu-id="1925f-158">Není nutné převést vaše řadiče do složky s názvem řadiče.</span><span class="sxs-lookup"><span data-stu-id="1925f-158">You don't need to put your controllers into a folder named Controllers.</span></span> <span data-ttu-id="1925f-159">Název složky je jenom pohodlný způsob, jak uspořádat zdrojové soubory.</span><span class="sxs-lookup"><span data-stu-id="1925f-159">The folder name is just a convenient way to organize your source files.</span></span>


<span data-ttu-id="1925f-160">Pokud tento soubor ještě není otevřený, klikněte dvakrát na soubor otevřete.</span><span class="sxs-lookup"><span data-stu-id="1925f-160">If this file is not open already, double-click the file to open it.</span></span> <span data-ttu-id="1925f-161">Nahraďte kód v tomto souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1925f-161">Replace the code in this file with the following:</span></span>

[!code-csharp[Main](tutorial-your-first-web-api/samples/sample2.cs)]

<span data-ttu-id="1925f-162">Pro zjednodušení tento příklad produktech ukládají do pole s pevnou uvnitř třídy kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1925f-162">To keep the example simple, products are stored in a fixed array inside the controller class.</span></span> <span data-ttu-id="1925f-163">V reálné aplikaci, by samozřejmě dotaz na databázi nebo použít jiný zdroj externí data.</span><span class="sxs-lookup"><span data-stu-id="1925f-163">Of course, in a real application, you would query a database or use some other external data source.</span></span>

<span data-ttu-id="1925f-164">Kontroler definuje dvě metody, které vracejí produkty:</span><span class="sxs-lookup"><span data-stu-id="1925f-164">The controller defines two methods that return products:</span></span>

- <span data-ttu-id="1925f-165">`GetAllProducts` Metoda vrátí celý seznam produktů jako **IEnumerable&lt;produktu&gt;**  typu.</span><span class="sxs-lookup"><span data-stu-id="1925f-165">The `GetAllProducts` method returns the entire list of products as an **IEnumerable&lt;Product&gt;** type.</span></span>
- <span data-ttu-id="1925f-166">`GetProduct` Metoda vyhledá jeden produkt pomocí jeho ID.</span><span class="sxs-lookup"><span data-stu-id="1925f-166">The `GetProduct` method looks up a single product by its ID.</span></span>

<span data-ttu-id="1925f-167">A to je vše!</span><span class="sxs-lookup"><span data-stu-id="1925f-167">That's it!</span></span> <span data-ttu-id="1925f-168">Máte pracovní webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1925f-168">You have a working web API.</span></span> <span data-ttu-id="1925f-169">Každá metoda v řadiči odpovídá jedné nebo více identifikátorů URI:</span><span class="sxs-lookup"><span data-stu-id="1925f-169">Each method on the controller corresponds to one or more URIs:</span></span>

| <span data-ttu-id="1925f-170">Metoda kontroleru</span><span class="sxs-lookup"><span data-stu-id="1925f-170">Controller Method</span></span> | <span data-ttu-id="1925f-171">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="1925f-171">URI</span></span> |
| --- | --- |
| <span data-ttu-id="1925f-172">GetAllProducts</span><span class="sxs-lookup"><span data-stu-id="1925f-172">GetAllProducts</span></span> | <span data-ttu-id="1925f-173">/ api/produkty</span><span class="sxs-lookup"><span data-stu-id="1925f-173">/api/products</span></span> |
| <span data-ttu-id="1925f-174">GetProduct</span><span class="sxs-lookup"><span data-stu-id="1925f-174">GetProduct</span></span> | <span data-ttu-id="1925f-175">/ webové rozhraníAPI/produkty/*id*</span><span class="sxs-lookup"><span data-stu-id="1925f-175">/api/products/*id*</span></span> |

<span data-ttu-id="1925f-176">Pro `GetProduct` metody, *id* v identifikátoru URI je zástupný symbol.</span><span class="sxs-lookup"><span data-stu-id="1925f-176">For the `GetProduct` method, the *id* in the URI is a placeholder.</span></span> <span data-ttu-id="1925f-177">Například pokud chcete získat produkt s ID 5, identifikátor URI je `api/products/5`.</span><span class="sxs-lookup"><span data-stu-id="1925f-177">For example, to get the product with ID of 5, the URI is `api/products/5`.</span></span>

<span data-ttu-id="1925f-178">Další informace o jak směruje požadavky HTTP pro metody kontroleru webového rozhraní API najdete v tématu [směrování v rozhraní ASP.NET Web API](../web-api-routing-and-actions/routing-in-aspnet-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="1925f-178">For more information about how Web API routes HTTP requests to controller methods, see [Routing in ASP.NET Web API](../web-api-routing-and-actions/routing-in-aspnet-web-api.md).</span></span>

## <a name="calling-the-web-api-with-javascript-and-jquery"></a><span data-ttu-id="1925f-179">Volání webového rozhraní API s použitím jazyka Javascript a jQuery</span><span class="sxs-lookup"><span data-stu-id="1925f-179">Calling the Web API with Javascript and jQuery</span></span>

<span data-ttu-id="1925f-180">V této části přidáme stránku HTML, který používá AJAX, aby volala webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1925f-180">In this section, we'll add an HTML page that uses AJAX to call the web API.</span></span> <span data-ttu-id="1925f-181">Použijeme jQuery provádět volání jazyka AJAX a také aktualizovat na stránce s výsledky.</span><span class="sxs-lookup"><span data-stu-id="1925f-181">We'll use jQuery to make the AJAX calls and also to update the page with the results.</span></span>

<span data-ttu-id="1925f-182">V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **přidat**a pak vyberte **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="1925f-182">In Solution Explorer, right-click the project and select **Add**, then select **New Item**.</span></span>

![](tutorial-your-first-web-api/_static/image9.png)

<span data-ttu-id="1925f-183">V **přidat novou položku** dialogového okna, vyberte **webové** pod uzlem **Visual C#** a pak vyberte **stránku HTML** položky.</span><span class="sxs-lookup"><span data-stu-id="1925f-183">In the **Add New Item** dialog, select the **Web** node under **Visual C#**, and then select the **HTML Page** item.</span></span> <span data-ttu-id="1925f-184">Pojmenujte stránku &quot;index.html&quot;.</span><span class="sxs-lookup"><span data-stu-id="1925f-184">Name the page &quot;index.html&quot;.</span></span>

![](tutorial-your-first-web-api/_static/image10.png)

<span data-ttu-id="1925f-185">Všechno, co je v tomto souboru nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1925f-185">Replace everything in this file with the following:</span></span>

[!code-html[Main](tutorial-your-first-web-api/samples/sample3.html)]

<span data-ttu-id="1925f-186">Existuje několik způsobů, jak získat jQuery.</span><span class="sxs-lookup"><span data-stu-id="1925f-186">There are several ways to get jQuery.</span></span> <span data-ttu-id="1925f-187">V tomto příkladu byl použit [Microsoft Ajax CDN](../../../ajax/cdn/overview.md).</span><span class="sxs-lookup"><span data-stu-id="1925f-187">In this example, I used the [Microsoft Ajax CDN](../../../ajax/cdn/overview.md).</span></span> <span data-ttu-id="1925f-188">Můžete také stáhnout z [ http://jquery.com/ ](http://jquery.com/)a technologie ASP.NET "Webového rozhraní API" jQuery také zahrnuje šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="1925f-188">You can also download it from [http://jquery.com/](http://jquery.com/), and the ASP.NET "Web API" project template includes jQuery as well.</span></span>

### <a name="getting-a-list-of-products"></a><span data-ttu-id="1925f-189">Načítá se seznam produktů</span><span class="sxs-lookup"><span data-stu-id="1925f-189">Getting a List of Products</span></span>

<span data-ttu-id="1925f-190">Pokud chcete získat seznam produktů, odeslat požadavek HTTP GET na &quot;/api/produkty&quot;.</span><span class="sxs-lookup"><span data-stu-id="1925f-190">To get a list of products, send an HTTP GET request to &quot;/api/products&quot;.</span></span>

<span data-ttu-id="1925f-191">JQuery [getJSON](http://api.jquery.com/jQuery.getJSON/) funkce odešle požadavek AJAX.</span><span class="sxs-lookup"><span data-stu-id="1925f-191">The jQuery [getJSON](http://api.jquery.com/jQuery.getJSON/) function sends an AJAX request.</span></span> <span data-ttu-id="1925f-192">Odpověď obsahuje pole objektů JSON.</span><span class="sxs-lookup"><span data-stu-id="1925f-192">For response contains array of JSON objects.</span></span> <span data-ttu-id="1925f-193">`done` Funkce určuje zpětné volání, která je volána, pokud je žádost úspěšná.</span><span class="sxs-lookup"><span data-stu-id="1925f-193">The `done` function specifies a callback that is called if the request succeeds.</span></span> <span data-ttu-id="1925f-194">Při zpětném volání aktualizujeme modelu DOM se informace o produktu.</span><span class="sxs-lookup"><span data-stu-id="1925f-194">In the callback, we update the DOM with the product information.</span></span>

[!code-html[Main](tutorial-your-first-web-api/samples/sample4.html)]

### <a name="getting-a-product-by-id"></a><span data-ttu-id="1925f-195">Získání produktu podle ID</span><span class="sxs-lookup"><span data-stu-id="1925f-195">Getting a Product By ID</span></span>

<span data-ttu-id="1925f-196">Získat produktů podle ID, odeslat požadavek HTTP GET na &quot;/webové rozhraní API/produkty/*id*&quot;, kde *id* je ID produktu.</span><span class="sxs-lookup"><span data-stu-id="1925f-196">To get a product by ID, send an HTTP GET request to &quot;/api/products/*id*&quot;, where *id* is the product ID.</span></span>

[!code-javascript[Main](tutorial-your-first-web-api/samples/sample5.js)]

<span data-ttu-id="1925f-197">Stále říkáme `getJSON` odeslat požadavek AJAX, ale tentokrát klademe ID v identifikátoru URI požadavku.</span><span class="sxs-lookup"><span data-stu-id="1925f-197">We still call `getJSON` to send the AJAX request, but this time we put the ID in the request URI.</span></span> <span data-ttu-id="1925f-198">Odpověď z této žádosti se JSON s reprezentací provedených jednoho produktu.</span><span class="sxs-lookup"><span data-stu-id="1925f-198">The response from this request is a JSON representation of a single product.</span></span>

## <a name="running-the-application"></a><span data-ttu-id="1925f-199">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="1925f-199">Running the Application</span></span>

<span data-ttu-id="1925f-200">Stisknutím klávesy F5 spusťte ladění aplikace.</span><span class="sxs-lookup"><span data-stu-id="1925f-200">Press F5 to start debugging the application.</span></span> <span data-ttu-id="1925f-201">Webové stránce by měl vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="1925f-201">The web page should look like the following:</span></span>

![](tutorial-your-first-web-api/_static/image11.png)

<span data-ttu-id="1925f-202">K získání produktu podle ID, zadejte ID a klikněte na tlačítko Hledat:</span><span class="sxs-lookup"><span data-stu-id="1925f-202">To get a product by ID, enter the ID and click Search:</span></span>

![](tutorial-your-first-web-api/_static/image12.png)

<span data-ttu-id="1925f-203">Pokud zadáte neplatné ID, server vrátí chybu HTTP:</span><span class="sxs-lookup"><span data-stu-id="1925f-203">If you enter an invalid ID, the server returns an HTTP error:</span></span>

![](tutorial-your-first-web-api/_static/image13.png)

## <a name="using-f12-to-view-the-http-request-and-response"></a><span data-ttu-id="1925f-204">Chcete-li zobrazit požadavků HTTP a odpovědí pomocí F12</span><span class="sxs-lookup"><span data-stu-id="1925f-204">Using F12 to View the HTTP Request and Response</span></span>

<span data-ttu-id="1925f-205">Při práci se službou HTTP, může být velmi užitečná k naleznete požadavek HTTP a zprávy s požadavkem.</span><span class="sxs-lookup"><span data-stu-id="1925f-205">When you are working with an HTTP service, it can be very useful to see the HTTP request and request messages.</span></span> <span data-ttu-id="1925f-206">Můžete to provést pomocí vývojářských nástrojů F12 v aplikaci Internet Explorer 9.</span><span class="sxs-lookup"><span data-stu-id="1925f-206">You can do this by using the F12 developer tools in Internet Explorer 9.</span></span> <span data-ttu-id="1925f-207">Z aplikace Internet Explorer 9, stiskněte klávesu **F12** otevřete nástroje.</span><span class="sxs-lookup"><span data-stu-id="1925f-207">From Internet Explorer 9, press **F12** to open the tools.</span></span> <span data-ttu-id="1925f-208">Klikněte na tlačítko **sítě** kartu a stiskněte klávesu **spustit zachytávání**.</span><span class="sxs-lookup"><span data-stu-id="1925f-208">Click the **Network** tab and press **Start Capturing**.</span></span> <span data-ttu-id="1925f-209">Nyní přejděte zpět na webovou stránku a stiskněte klávesu **F5** k opětovnému načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="1925f-209">Now go back to the web page and press **F5** to reload the web page.</span></span> <span data-ttu-id="1925f-210">Aplikace Internet Explorer zaznamená přenos pomocí protokolu HTTP mezi prohlížečem a webový server.</span><span class="sxs-lookup"><span data-stu-id="1925f-210">Internet Explorer will capture the HTTP traffic between the browser and the web server.</span></span> <span data-ttu-id="1925f-211">Souhrnné zobrazení se zobrazuje veškerý síťový provoz pro stránku:</span><span class="sxs-lookup"><span data-stu-id="1925f-211">The summary view shows all the network traffic for a page:</span></span>

![](tutorial-your-first-web-api/_static/image14.png)

<span data-ttu-id="1925f-212">Vyhledejte položku pro relativní identifikátor URI "produktů s rozhraním api / /".</span><span class="sxs-lookup"><span data-stu-id="1925f-212">Locate the entry for the relative URI "api/products/".</span></span> <span data-ttu-id="1925f-213">Vyberte tuto položku a klikněte na tlačítko **přejít na podrobnou zobrazení**.</span><span class="sxs-lookup"><span data-stu-id="1925f-213">Select this entry and click **Go to detailed view**.</span></span> <span data-ttu-id="1925f-214">V zobrazení podrobností jsou karty zobrazíte hlaviček žádostí a odpovědí a úřadů.</span><span class="sxs-lookup"><span data-stu-id="1925f-214">In the detail view, there are tabs to view the request and response headers and bodies.</span></span> <span data-ttu-id="1925f-215">Například, pokud kliknete **hlavičky požadavku** kartě, zobrazí se, že klient vyžádal &quot;application/json&quot; v hlavičce Accept.</span><span class="sxs-lookup"><span data-stu-id="1925f-215">For example, if you click the **Request headers** tab, you can see that the client requested &quot;application/json&quot; in the Accept header.</span></span>

![](tutorial-your-first-web-api/_static/image15.png)

<span data-ttu-id="1925f-216">Pokud kliknete na kartě tělo odpovědi, uvidíte, jak byl seznam produktů serializovat do formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="1925f-216">If you click the Response body tab, you can see how the product list was serialized to JSON.</span></span> <span data-ttu-id="1925f-217">Jiné prohlížeče mají podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="1925f-217">Other browsers have similar functionality.</span></span> <span data-ttu-id="1925f-218">Další užitečné nástroje je [Fiddler](http://www.fiddler2.com/fiddler2/), webový ladicí proxy server.</span><span class="sxs-lookup"><span data-stu-id="1925f-218">Another useful tool is [Fiddler](http://www.fiddler2.com/fiddler2/), a web debugging proxy.</span></span> <span data-ttu-id="1925f-219">Můžete Fiddleru zobrazíte provoz protokolu HTTP a také k vytváření žádostí HTTP, která poskytuje plnou kontrolu nad hlavičky protokolu HTTP v požadavku.</span><span class="sxs-lookup"><span data-stu-id="1925f-219">You can use Fiddler to view your HTTP traffic, and also to compose HTTP requests, which gives you full control over the HTTP headers in the request.</span></span>

## <a name="see-this-app-running-on-azure"></a><span data-ttu-id="1925f-220">Zobrazit tuto aplikaci běžící v Azure</span><span class="sxs-lookup"><span data-stu-id="1925f-220">See this App Running on Azure</span></span>

<span data-ttu-id="1925f-221">Chcete zobrazit dokončené web spuštěný jako živou webovou aplikaci?</span><span class="sxs-lookup"><span data-stu-id="1925f-221">Would you like to see the finished site running as a live web app?</span></span> <span data-ttu-id="1925f-222">Kompletní verze aplikace můžete nasadit ke svému účtu Azure jednoduše kliknutím na následující tlačítko.</span><span class="sxs-lookup"><span data-stu-id="1925f-222">You can deploy a complete version of the app to your Azure account by simply clicking the following button.</span></span>

[![](https://azuredeploy.net/deploybutton.png)](https://deploy.azure.com/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/WebAPI-ProductsApp#/form/setup)

<span data-ttu-id="1925f-223">Potřebujete účet Azure k nasazení tohoto řešení do Azure.</span><span class="sxs-lookup"><span data-stu-id="1925f-223">You need an Azure account to deploy this solution to Azure.</span></span> <span data-ttu-id="1925f-224">Pokud ještě nemáte účet, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="1925f-224">If you do not already have an account, you have the following options:</span></span>

- <span data-ttu-id="1925f-225">[Zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) – budete dostávat kredity můžete použít k vyzkoušení placených služeb Azure a dokonce i po jejich použití až můžete účet ponechat a dál používat bezplatné služby Azure.</span><span class="sxs-lookup"><span data-stu-id="1925f-225">[Open an Azure account for free](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) - You get credits you can use to try out paid Azure services, and even after they're used up you can keep the account and use free Azure services.</span></span>
- <span data-ttu-id="1925f-226">[Aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) – vaše předplatné MSDN vám dává kredity každý měsíc, můžete použít k placení za služby Azure.</span><span class="sxs-lookup"><span data-stu-id="1925f-226">[Activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) - Your MSDN subscription gives you credits every month that you can use for paid Azure services.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1925f-227">Další kroky</span><span class="sxs-lookup"><span data-stu-id="1925f-227">Next Steps</span></span>

- <span data-ttu-id="1925f-228">Kompletní příklad, který podporuje akce POST, PUT a DELETE a zapisuje do databáze služby protokolu HTTP, naleznete v tématu [pomocí webového rozhraní API 2 s Entity Framework 6](../data/using-web-api-with-entity-framework/part-1.md).</span><span class="sxs-lookup"><span data-stu-id="1925f-228">For a more complete example of an HTTP service that supports POST, PUT, and DELETE actions and writes to a database, see [Using Web API 2 with Entity Framework 6](../data/using-web-api-with-entity-framework/part-1.md).</span></span>
- <span data-ttu-id="1925f-229">Další informace o vytváření plynulé a rychlost reakce webové aplikace založené na službě HTTP v tématu [jednostránkové aplikace ASP.NET](../../../single-page-application/index.md).</span><span class="sxs-lookup"><span data-stu-id="1925f-229">For more about creating fluid and responsive web applications on top of an HTTP service, see [ASP.NET Single Page Application](../../../single-page-application/index.md).</span></span>
- <span data-ttu-id="1925f-230">Informace o tom, jak nasadit webový projekt sady Visual Studio do služby Azure App Service najdete v tématu [vytvoření webové aplikace ASP.NET ve službě Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span><span class="sxs-lookup"><span data-stu-id="1925f-230">For information about how to deploy a Visual Studio web project to Azure App Service, see [Create an ASP.NET web app in Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span></span>
