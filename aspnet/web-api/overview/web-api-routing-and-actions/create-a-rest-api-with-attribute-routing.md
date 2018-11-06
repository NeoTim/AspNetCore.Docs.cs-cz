---
uid: web-api/overview/web-api-routing-and-actions/create-a-rest-api-with-attribute-routing
title: Vytvořit rozhraní REST API se směrováním atributů v rozhraní ASP.NET Web API 2 | Dokumentace Microsoftu
author: MikeWasson
description: ''
ms.author: riande
ms.date: 06/26/2013
ms.assetid: 23fc77da-2725-4434-99a0-ff872d96336b
msc.legacyurl: /web-api/overview/web-api-routing-and-actions/create-a-rest-api-with-attribute-routing
msc.type: authoredcontent
ms.openlocfilehash: 18a44c280e6df1603837938d24d7d639d8c87cc2
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021414"
---
<a name="create-a-rest-api-with-attribute-routing-in-aspnet-web-api-2"></a><span data-ttu-id="e3191-102">Vytvořit rozhraní REST API se směrováním atributů ve rozhraní ASP.NET Web API 2</span><span class="sxs-lookup"><span data-stu-id="e3191-102">Create a REST API with Attribute Routing in ASP.NET Web API 2</span></span>
====================
<span data-ttu-id="e3191-103">podle [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="e3191-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="e3191-104">Webové rozhraní API 2 podporuje nový typ směrování, nazývá *směrováním atributů*.</span><span class="sxs-lookup"><span data-stu-id="e3191-104">Web API 2 supports a new type of routing, called *attribute routing*.</span></span> <span data-ttu-id="e3191-105">Obecný přehled směrování atributů, naleznete v tématu [směrováním atributů ve webovém rozhraní API 2](attribute-routing-in-web-api-2.md).</span><span class="sxs-lookup"><span data-stu-id="e3191-105">For a general overview of attribute routing, see [Attribute Routing in Web API 2](attribute-routing-in-web-api-2.md).</span></span> <span data-ttu-id="e3191-106">V tomto kurzu použijete směrováním atributů k vytvoření rozhraní REST API pro kolekce knihy.</span><span class="sxs-lookup"><span data-stu-id="e3191-106">In this tutorial, you will use attribute routing to create a REST API for a collection of books.</span></span> <span data-ttu-id="e3191-107">Rozhraní API bude podporovat tyto akce:</span><span class="sxs-lookup"><span data-stu-id="e3191-107">The API will support the following actions:</span></span>

| <span data-ttu-id="e3191-108">Akce</span><span class="sxs-lookup"><span data-stu-id="e3191-108">Action</span></span> | <span data-ttu-id="e3191-109">Příklad identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="e3191-109">Example URI</span></span> |
| --- | --- |
| <span data-ttu-id="e3191-110">Získání seznamu všech knih.</span><span class="sxs-lookup"><span data-stu-id="e3191-110">Get a list of all books.</span></span> | <span data-ttu-id="e3191-111">/ api/knihy</span><span class="sxs-lookup"><span data-stu-id="e3191-111">/api/books</span></span> |
| <span data-ttu-id="e3191-112">Získat knihu podle ID.</span><span class="sxs-lookup"><span data-stu-id="e3191-112">Get a book by ID.</span></span> | <span data-ttu-id="e3191-113">/API/Books/1</span><span class="sxs-lookup"><span data-stu-id="e3191-113">/api/books/1</span></span> |
| <span data-ttu-id="e3191-114">Získání podrobností o knize.</span><span class="sxs-lookup"><span data-stu-id="e3191-114">Get the details of a book.</span></span> | <span data-ttu-id="e3191-115">/API/Books/1/details</span><span class="sxs-lookup"><span data-stu-id="e3191-115">/api/books/1/details</span></span> |
| <span data-ttu-id="e3191-116">Získání seznamu knih podle žánru.</span><span class="sxs-lookup"><span data-stu-id="e3191-116">Get a list of books by genre.</span></span> | <span data-ttu-id="e3191-117">/API/Books/fantasy</span><span class="sxs-lookup"><span data-stu-id="e3191-117">/api/books/fantasy</span></span> |
| <span data-ttu-id="e3191-118">Získání seznamu sad knihy datu publikování.</span><span class="sxs-lookup"><span data-stu-id="e3191-118">Get a list of books by publication date.</span></span> | <span data-ttu-id="e3191-119">/API/Books/Date/2013-02-16 /api/books/date/2013/02/16 (alternativní formuláře)</span><span class="sxs-lookup"><span data-stu-id="e3191-119">/api/books/date/2013-02-16 /api/books/date/2013/02/16 (alternate form)</span></span> |
| <span data-ttu-id="e3191-120">Získání seznamu sad knihy konkrétním autorem.</span><span class="sxs-lookup"><span data-stu-id="e3191-120">Get a list of books by a particular author.</span></span> | <span data-ttu-id="e3191-121">/API/authors/1/Books</span><span class="sxs-lookup"><span data-stu-id="e3191-121">/api/authors/1/books</span></span> |

<span data-ttu-id="e3191-122">Všechny metody jsou jen pro čtení (požadavky HTTP GET).</span><span class="sxs-lookup"><span data-stu-id="e3191-122">All methods are read-only (HTTP GET requests).</span></span>

<span data-ttu-id="e3191-123">Datová vrstva použijeme Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e3191-123">For the data layer, we'll use Entity Framework.</span></span> <span data-ttu-id="e3191-124">Záznamy o knihách bude mít tahle pole:</span><span class="sxs-lookup"><span data-stu-id="e3191-124">Book records will have the following fields:</span></span>

- <span data-ttu-id="e3191-125">ID</span><span class="sxs-lookup"><span data-stu-id="e3191-125">ID</span></span>
- <span data-ttu-id="e3191-126">Název</span><span class="sxs-lookup"><span data-stu-id="e3191-126">Title</span></span>
- <span data-ttu-id="e3191-127">Genre</span><span class="sxs-lookup"><span data-stu-id="e3191-127">Genre</span></span>
- <span data-ttu-id="e3191-128">Datum publikování</span><span class="sxs-lookup"><span data-stu-id="e3191-128">Publication date</span></span>
- <span data-ttu-id="e3191-129">Cena</span><span class="sxs-lookup"><span data-stu-id="e3191-129">Price</span></span>
- <span data-ttu-id="e3191-130">Popis</span><span class="sxs-lookup"><span data-stu-id="e3191-130">Description</span></span>
- <span data-ttu-id="e3191-131">AuthorID (cizí klíč do tabulky autoři)</span><span class="sxs-lookup"><span data-stu-id="e3191-131">AuthorID (foreign key to an Authors table)</span></span>

<span data-ttu-id="e3191-132">Pro většinu požadavků, ale rozhraní API vrátí podmnožinu těchto dat (názvu, autora a rozšířením podle tematických).</span><span class="sxs-lookup"><span data-stu-id="e3191-132">For most requests, however, the API will return a subset of this data (title, author, and genre).</span></span> <span data-ttu-id="e3191-133">Chcete-li získat úplný záznam klienta požadavky `/api/books/{id}/details`.</span><span class="sxs-lookup"><span data-stu-id="e3191-133">To get the complete record, the client requests `/api/books/{id}/details`.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e3191-134">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e3191-134">Prerequisites</span></span>

<span data-ttu-id="e3191-135">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) Community, Professional nebo Enterprise edition.</span><span class="sxs-lookup"><span data-stu-id="e3191-135">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) Community, Professional or Enterprise edition.</span></span>

## <a name="create-the-visual-studio-project"></a><span data-ttu-id="e3191-136">Vytvoření projektu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e3191-136">Create the Visual Studio Project</span></span>

<span data-ttu-id="e3191-137">Začněte tím, že spustíte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e3191-137">Start by running Visual Studio.</span></span> <span data-ttu-id="e3191-138">Z **souboru** nabídce vyberte možnost **nový** a pak vyberte **projektu**.</span><span class="sxs-lookup"><span data-stu-id="e3191-138">From the **File** menu, select **New** and then select **Project**.</span></span>

<span data-ttu-id="e3191-139">Rozbalte **nainstalováno** > **Visual C#** kategorie.</span><span class="sxs-lookup"><span data-stu-id="e3191-139">Expand the **Installed** > **Visual C#** category.</span></span> <span data-ttu-id="e3191-140">V části **Visual C#** vyberte **webové**.</span><span class="sxs-lookup"><span data-stu-id="e3191-140">Under **Visual C#**, select **Web**.</span></span> <span data-ttu-id="e3191-141">V seznamu šablon projektu vyberte **webová aplikace ASP.NET (.NET Framework)**.</span><span class="sxs-lookup"><span data-stu-id="e3191-141">In the list of project templates, select **ASP.NET Web Application (.NET Framework)**.</span></span> <span data-ttu-id="e3191-142">Pojmenujte projekt &quot;BooksAPI&quot;.</span><span class="sxs-lookup"><span data-stu-id="e3191-142">Name the project &quot;BooksAPI&quot;.</span></span>

![](create-a-rest-api-with-attribute-routing/_static/image1.png)

<span data-ttu-id="e3191-143">V **nová webová aplikace ASP.NET** dialogového okna, vyberte **prázdný** šablony.</span><span class="sxs-lookup"><span data-stu-id="e3191-143">In the **New ASP.NET Web Application** dialog, select the **Empty** template.</span></span> <span data-ttu-id="e3191-144">V části "Přidat složky a základní odkazy pro" vyberte **webového rozhraní API** zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="e3191-144">Under "Add folders and core references for", select the **Web API** checkbox.</span></span> <span data-ttu-id="e3191-145">Klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="e3191-145">Click **OK**.</span></span>

![](create-a-rest-api-with-attribute-routing/_static/image2.png)

<span data-ttu-id="e3191-146">Tím se vytvoří kostru projektu, který je nakonfigurovaný pro webové rozhraní API funkce.</span><span class="sxs-lookup"><span data-stu-id="e3191-146">This creates a skeleton project that is configured for Web API functionality.</span></span>

### <a name="domain-models"></a><span data-ttu-id="e3191-147">Doménových modelů</span><span class="sxs-lookup"><span data-stu-id="e3191-147">Domain Models</span></span>

<span data-ttu-id="e3191-148">Dále přidejte třídy pro doménových modelů.</span><span class="sxs-lookup"><span data-stu-id="e3191-148">Next, add classes for domain models.</span></span> <span data-ttu-id="e3191-149">V Průzkumníku řešení klikněte pravým tlačítkem na složku modely.</span><span class="sxs-lookup"><span data-stu-id="e3191-149">In Solution Explorer, right-click the Models folder.</span></span> <span data-ttu-id="e3191-150">Vyberte **přidat**a pak vyberte **třídy**.</span><span class="sxs-lookup"><span data-stu-id="e3191-150">Select **Add**, then select **Class**.</span></span> <span data-ttu-id="e3191-151">Název třídy `Author`.</span><span class="sxs-lookup"><span data-stu-id="e3191-151">Name the class `Author`.</span></span>

![](create-a-rest-api-with-attribute-routing/_static/image3.png)

<span data-ttu-id="e3191-152">Nahraďte kód v Author.cs následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="e3191-152">Replace the code in Author.cs with the following:</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample1.cs)]

<span data-ttu-id="e3191-153">Nyní přidejte další třídu s názvem `Book`.</span><span class="sxs-lookup"><span data-stu-id="e3191-153">Now add another class named `Book`.</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample2.cs)]

### <a name="add-a-web-api-controller"></a><span data-ttu-id="e3191-154">Přidat kontroler rozhraní Web API</span><span class="sxs-lookup"><span data-stu-id="e3191-154">Add a Web API Controller</span></span>

<span data-ttu-id="e3191-155">V tomto kroku přidáme kontroler Web API, která používá Entity Framework jako datová vrstva.</span><span class="sxs-lookup"><span data-stu-id="e3191-155">In this step, we'll add a Web API controller that uses Entity Framework as the data layer.</span></span>

<span data-ttu-id="e3191-156">Sestavte projekt stisknutím kombinace kláves CTRL+SHIFT+B.</span><span class="sxs-lookup"><span data-stu-id="e3191-156">Press CTRL+SHIFT+B to build the project.</span></span> <span data-ttu-id="e3191-157">Entity Framework používá reflexi ke zjišťování vlastností modely, takže vyžaduje zkompilovaného sestavení k vytvoření schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="e3191-157">Entity Framework uses reflection to discover the properties of the models, so it requires a compiled assembly to create the database schema.</span></span>

<span data-ttu-id="e3191-158">V Průzkumníku řešení klikněte pravým tlačítkem myši na složku řadiče.</span><span class="sxs-lookup"><span data-stu-id="e3191-158">In Solution Explorer, right-click the Controllers folder.</span></span> <span data-ttu-id="e3191-159">Vyberte **přidat**a pak vyberte **řadič**.</span><span class="sxs-lookup"><span data-stu-id="e3191-159">Select **Add**, then select **Controller**.</span></span>

![](create-a-rest-api-with-attribute-routing/_static/image4.png)

<span data-ttu-id="e3191-160">V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **Kontroleru webového rozhraní API 2 s akcemi používající nástroj Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="e3191-160">In the **Add Scaffold** dialog, select **Web API 2 Controller with actions, using Entity Framework**.</span></span>

[![](create-a-rest-api-with-attribute-routing/_static/image6.png)](create-a-rest-api-with-attribute-routing/_static/image5.png)

<span data-ttu-id="e3191-161">V **přidat kontroler** dialogovém okně pro **názvu Kontroleru**, zadejte &quot;BooksController&quot;.</span><span class="sxs-lookup"><span data-stu-id="e3191-161">In the **Add Controller** dialog, for **Controller name**, enter &quot;BooksController&quot;.</span></span> <span data-ttu-id="e3191-162">Vyberte &quot;použít asynchronní akce kontroleru&quot; zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="e3191-162">Select the &quot;Use async controller actions&quot; checkbox.</span></span> <span data-ttu-id="e3191-163">Pro **třída modelu**vyberte &quot;knihy&quot;.</span><span class="sxs-lookup"><span data-stu-id="e3191-163">For **Model class**, select &quot;Book&quot;.</span></span> <span data-ttu-id="e3191-164">(Pokud se nezobrazí `Book` třídy uvedené v rozevíracím seznamu, ujistěte se, že jste sestavili projekt.) Klikněte na tlačítko "+".</span><span class="sxs-lookup"><span data-stu-id="e3191-164">(If you don't see the `Book` class listed in the dropdown, make sure that you built the project.) Then click the "+" button.</span></span>

![](create-a-rest-api-with-attribute-routing/_static/image7.png)

<span data-ttu-id="e3191-165">Klikněte na tlačítko **přidat** v **nový kontext dat** dialogového okna.</span><span class="sxs-lookup"><span data-stu-id="e3191-165">Click **Add** in the **New Data Context** dialog.</span></span>

![](create-a-rest-api-with-attribute-routing/_static/image8.png)

<span data-ttu-id="e3191-166">Klikněte na tlačítko **přidat** v **přidat kontroler** dialogového okna.</span><span class="sxs-lookup"><span data-stu-id="e3191-166">Click **Add** in the **Add Controller** dialog.</span></span> <span data-ttu-id="e3191-167">Základní kostry aplikace přidá třídu s názvem `BooksController` , který definuje kontroleru rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e3191-167">The scaffolding adds a class named `BooksController` that defines the API controller.</span></span> <span data-ttu-id="e3191-168">Také přidá třídu s názvem `BooksAPIContext` ve složce modelů, která definuje kontext dat pro Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e3191-168">It also adds a class named `BooksAPIContext` in the Models folder, which defines the data context for Entity Framework.</span></span>

![](create-a-rest-api-with-attribute-routing/_static/image9.png)

### <a name="seed-the-database"></a><span data-ttu-id="e3191-169">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="e3191-169">Seed the Database</span></span>

<span data-ttu-id="e3191-170">V nabídce Nástroje vyberte **Správce balíčků NuGet**a pak vyberte **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="e3191-170">From the Tools menu, select **NuGet Package Manager**, and then select **Package Manager Console**.</span></span>

<span data-ttu-id="e3191-171">V okně konzoly Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e3191-171">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](create-a-rest-api-with-attribute-routing/samples/sample3.ps1)]

<span data-ttu-id="e3191-172">Tento příkaz vytvoří složku migrace a přidá nový soubor kódu s názvem Configuration.cs.</span><span class="sxs-lookup"><span data-stu-id="e3191-172">This command creates a Migrations folder and adds a new code file named Configuration.cs.</span></span> <span data-ttu-id="e3191-173">Otevřete tento soubor a přidejte následující kód, který `Configuration.Seed` metody.</span><span class="sxs-lookup"><span data-stu-id="e3191-173">Open this file and add the following code to the `Configuration.Seed` method.</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample4.cs)]

<span data-ttu-id="e3191-174">V okně konzoly Správce balíčků zadejte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="e3191-174">In the Package Manager Console window, type the following commands.</span></span>

[!code-powershell[Main](create-a-rest-api-with-attribute-routing/samples/sample5.ps1)]

<span data-ttu-id="e3191-175">Tyto příkazy vytvoří místní databáze a vyvolat metodu počáteční hodnoty k naplnění databáze.</span><span class="sxs-lookup"><span data-stu-id="e3191-175">These commands create a local database and invoke the Seed method to populate the database.</span></span>

![](create-a-rest-api-with-attribute-routing/_static/image10.png)

## <a name="add-dto-classes"></a><span data-ttu-id="e3191-176">Přidat objekt DTO třídy</span><span class="sxs-lookup"><span data-stu-id="e3191-176">Add DTO Classes</span></span>

<span data-ttu-id="e3191-177">Pokud aplikaci nyní spustit a odeslat požadavek GET na /api/books/1 odpověď vypadá podobně jako následující.</span><span class="sxs-lookup"><span data-stu-id="e3191-177">If you run the application now and send a GET request to /api/books/1, the response looks similar to the following.</span></span> <span data-ttu-id="e3191-178">(Po přidání odsazení pro lepší čitelnost).</span><span class="sxs-lookup"><span data-stu-id="e3191-178">(I added indentation for readability.)</span></span>

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample6.json)]

<span data-ttu-id="e3191-179">Místo toho chci, aby tento požadavek vrátí podmnožinu polí.</span><span class="sxs-lookup"><span data-stu-id="e3191-179">Instead, I want this request to return a subset of the fields.</span></span> <span data-ttu-id="e3191-180">Také jsem má vrátí jméno autora, spíše než ID autora.</span><span class="sxs-lookup"><span data-stu-id="e3191-180">Also, I want it to return the author's name, rather than the author ID.</span></span> <span data-ttu-id="e3191-181">K tomu upravíme metody kontroleru se vraťte *objekt pro přenos dat* (DTO) namísto EF modelu.</span><span class="sxs-lookup"><span data-stu-id="e3191-181">To accomplish this, we'll modify the controller methods to return a *data transfer object* (DTO) instead of the EF model.</span></span> <span data-ttu-id="e3191-182">Objekt DTO je objekt, který je určena pouze pro přenášejí data.</span><span class="sxs-lookup"><span data-stu-id="e3191-182">A DTO is an object that is designed only to carry data.</span></span>

<span data-ttu-id="e3191-183">V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **přidat** | **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e3191-183">In Solution Explorer, right-click the project and select **Add** | **New Folder**.</span></span> <span data-ttu-id="e3191-184">Název složky &quot;DTO&quot;.</span><span class="sxs-lookup"><span data-stu-id="e3191-184">Name the folder &quot;DTOs&quot;.</span></span> <span data-ttu-id="e3191-185">Přidejte třídu pojmenovanou `BookDto` ke složce DTO s následující definice:</span><span class="sxs-lookup"><span data-stu-id="e3191-185">Add a class named `BookDto` to the DTOs folder, with the following definition:</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample7.cs)]

<span data-ttu-id="e3191-186">Přidejte další třídu s názvem `BookDetailDto`.</span><span class="sxs-lookup"><span data-stu-id="e3191-186">Add another class named `BookDetailDto`.</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample8.cs)]

<span data-ttu-id="e3191-187">Dále, aktualizujte `BooksController` třídy pro vracení `BookDto` instancí.</span><span class="sxs-lookup"><span data-stu-id="e3191-187">Next, update the `BooksController` class to return `BookDto` instances.</span></span> <span data-ttu-id="e3191-188">Použijeme [Queryable.Select](https://msdn.microsoft.com/library/system.linq.queryable.select.aspx) metoda do projektu `Book` instance na `BookDto` instancí.</span><span class="sxs-lookup"><span data-stu-id="e3191-188">We'll use the [Queryable.Select](https://msdn.microsoft.com/library/system.linq.queryable.select.aspx) method to project `Book` instances to `BookDto` instances.</span></span> <span data-ttu-id="e3191-189">Tady je aktualizovaný kód pro třídu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e3191-189">Here is the updated code for the controller class.</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample9.cs)]

> [!NOTE]
> <span data-ttu-id="e3191-190">Odstranil jsem `PutBook`, `PostBook`, a `DeleteBook` metody, protože nejsou potřeba pro účely tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="e3191-190">I deleted the `PutBook`, `PostBook`, and `DeleteBook` methods, because they aren't needed for this tutorial.</span></span>


<span data-ttu-id="e3191-191">Teď Pokud spuštění aplikace a požádat o /api/books/1 tělo odpovědi by měl vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="e3191-191">Now if you run the application and request /api/books/1, the response body should look like this:</span></span>

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample10.json)]

## <a name="add-route-attributes"></a><span data-ttu-id="e3191-192">Přidat atributy trasy</span><span class="sxs-lookup"><span data-stu-id="e3191-192">Add Route Attributes</span></span>

<span data-ttu-id="e3191-193">V dalším kroku se nám budete převést řadiče na použití směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="e3191-193">Next, we'll convert the controller to use attribute routing.</span></span> <span data-ttu-id="e3191-194">Nejprve přidejte **RoutePrefix** atributů kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e3191-194">First, add a **RoutePrefix** attribute to the controller.</span></span> <span data-ttu-id="e3191-195">Tento atribut definuje počáteční identifikátor URI segmenty pro všechny metody v tomto kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e3191-195">This attribute defines the initial URI segments for all methods on this controller.</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample11.cs?highlight=1)]

<span data-ttu-id="e3191-196">Pak přidejte **[trasy]** atributy na akce kontroleru, následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="e3191-196">Then add **[Route]** attributes to the controller actions, as follows:</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample12.cs?highlight=1,7)]

<span data-ttu-id="e3191-197">Šablona trasy pro jednotlivé metody kontroleru je předpona plus řetězec zadaný v **trasy** atribut.</span><span class="sxs-lookup"><span data-stu-id="e3191-197">The route template for each controller method is the prefix plus the string specified in the **Route** attribute.</span></span> <span data-ttu-id="e3191-198">Pro `GetBook` parametrizovaný řetězec obsahuje metodu, šablona trasy &quot;{id: int}&quot;, který odpovídá, pokud segment identifikátoru URI obsahuje celočíselnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e3191-198">For the `GetBook` method, the route template includes the parameterized string &quot;{id:int}&quot;, which matches if the URI segment contains an integer value.</span></span>

| <span data-ttu-id="e3191-199">Metoda</span><span class="sxs-lookup"><span data-stu-id="e3191-199">Method</span></span> | <span data-ttu-id="e3191-200">Šablona trasy</span><span class="sxs-lookup"><span data-stu-id="e3191-200">Route Template</span></span> | <span data-ttu-id="e3191-201">Příklad identifikátoru URI</span><span class="sxs-lookup"><span data-stu-id="e3191-201">Example URI</span></span> |
| --- | --- | --- |
| `GetBooks` | <span data-ttu-id="e3191-202">"api/knihy"</span><span class="sxs-lookup"><span data-stu-id="e3191-202">"api/books"</span></span> | `http://localhost/api/books` |
| `GetBook` | <span data-ttu-id="e3191-203">"api/books / {id: int}"</span><span class="sxs-lookup"><span data-stu-id="e3191-203">"api/books/{id:int}"</span></span> | `http://localhost/api/books/5` |

## <a name="get-book-details"></a><span data-ttu-id="e3191-204">Získat podrobnosti adresáře</span><span class="sxs-lookup"><span data-stu-id="e3191-204">Get Book Details</span></span>

<span data-ttu-id="e3191-205">Pokud chcete získat podrobnosti adresáře, klient odešle požadavek GET na `/api/books/{id}/details`, kde *{id}* je ID knihy.</span><span class="sxs-lookup"><span data-stu-id="e3191-205">To get book details, the client will send a GET request to `/api/books/{id}/details`, where *{id}* is the ID of the book.</span></span>

<span data-ttu-id="e3191-206">Přidejte následující metodu do `BooksController` třídy.</span><span class="sxs-lookup"><span data-stu-id="e3191-206">Add the following method to the `BooksController` class.</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample13.cs)]

<span data-ttu-id="e3191-207">Pokud jste požádali o `/api/books/1/details`, odpověď vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="e3191-207">If you request `/api/books/1/details`, the response looks like this:</span></span>

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample14.json)]

## <a name="get-books-by-genre"></a><span data-ttu-id="e3191-208">Získat knihy podle žánru</span><span class="sxs-lookup"><span data-stu-id="e3191-208">Get Books By Genre</span></span>

<span data-ttu-id="e3191-209">K získání seznamu knih v konkrétní genre, klient odešle požadavek GET na `/api/books/genre`, kde *žánr* je název žánr.</span><span class="sxs-lookup"><span data-stu-id="e3191-209">To get a list of books in a specific genre, the client will send a GET request to `/api/books/genre`, where *genre* is the name of the genre.</span></span> <span data-ttu-id="e3191-210">(Například `/api/books/fantasy`.)</span><span class="sxs-lookup"><span data-stu-id="e3191-210">(For example, `/api/books/fantasy`.)</span></span>

<span data-ttu-id="e3191-211">Přidejte následující metodu do `BooksController`.</span><span class="sxs-lookup"><span data-stu-id="e3191-211">Add the following method to `BooksController`.</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample15.cs)]

<span data-ttu-id="e3191-212">Tady jsme definovat trasu, která obsahuje parametr {žánr} v šablona identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="e3191-212">Here we are defining a route that contains a {genre} parameter in the URI template.</span></span> <span data-ttu-id="e3191-213">Všimněte si, že se k rozlišení těchto dvou identifikátory URI a směrovat na různé metody webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="e3191-213">Notice that Web API is able to distinguish these two URIs and route them to different methods:</span></span>

`/api/books/1`

`/api/books/fantasy`

<span data-ttu-id="e3191-214">Důvodem je, `GetBook` metoda obsahuje omezení, že segment "id" musí být celočíselná hodnota:</span><span class="sxs-lookup"><span data-stu-id="e3191-214">That's because the `GetBook` method includes a constraint that the "id" segment must be an integer value:</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample16.cs?highlight=1)]

<span data-ttu-id="e3191-215">Pokud jste požádali o /api/books/fantasy, odpověď vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="e3191-215">If you request /api/books/fantasy, the response looks like this:</span></span>

`[ { "Title": "Midnight Rain", "Author": "Ralls, Kim", "Genre": "Fantasy" }, { "Title": "Maeve Ascendant", "Author": "Corets, Eva", "Genre": "Fantasy" }, { "Title": "The Sundered Grail", "Author": "Corets, Eva", "Genre": "Fantasy" } ]`

## <a name="get-books-by-author"></a><span data-ttu-id="e3191-216">Získat knihy autorem</span><span class="sxs-lookup"><span data-stu-id="e3191-216">Get Books By Author</span></span>

<span data-ttu-id="e3191-217">Pokud chcete získat seznam webu knihy pro konkrétní Autor, klient odešle požadavek GET na `/api/authors/id/books`, kde *id* je ID autora.</span><span class="sxs-lookup"><span data-stu-id="e3191-217">To get a list of a books for a particular author, the client will send a GET request to `/api/authors/id/books`, where *id* is the ID of the author.</span></span>

<span data-ttu-id="e3191-218">Přidejte následující metodu do `BooksController`.</span><span class="sxs-lookup"><span data-stu-id="e3191-218">Add the following method to `BooksController`.</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample17.cs)]

<span data-ttu-id="e3191-219">V tomto příkladu je zajímavé protože &quot;knihy&quot; je zacházeno podřízený prostředek &quot;autoři&quot;.</span><span class="sxs-lookup"><span data-stu-id="e3191-219">This example is interesting because &quot;books&quot; is treated a child resource of &quot;authors&quot;.</span></span> <span data-ttu-id="e3191-220">Tento model je celkem běžné v rozhraní RESTful API.</span><span class="sxs-lookup"><span data-stu-id="e3191-220">This pattern is quite common in RESTful APIs.</span></span>

<span data-ttu-id="e3191-221">Předpona trasy v přepsání tilda (~) v šabloně trasy **RoutePrefix** atribut.</span><span class="sxs-lookup"><span data-stu-id="e3191-221">The tilde (~) in the route template overrides the route prefix in the **RoutePrefix** attribute.</span></span>

## <a name="get-books-by-publication-date"></a><span data-ttu-id="e3191-222">Získat knihy datum publikace</span><span class="sxs-lookup"><span data-stu-id="e3191-222">Get Books By Publication Date</span></span>

<span data-ttu-id="e3191-223">K získání seznamu knih podle data zveřejnění, klient odešle požadavek GET na `/api/books/date/yyyy-mm-dd`, kde *rrrr mm-dd* datum.</span><span class="sxs-lookup"><span data-stu-id="e3191-223">To get a list of books by publication date, the client will send a GET request to `/api/books/date/yyyy-mm-dd`, where *yyyy-mm-dd* is the date.</span></span>

<span data-ttu-id="e3191-224">Tady je jeden způsob, jak to udělat:</span><span class="sxs-lookup"><span data-stu-id="e3191-224">Here is one way to do this:</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample18.cs)]

<span data-ttu-id="e3191-225">`{pubdate:datetime}` Parametr je omezená tak, aby odpovídaly **data a času** hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e3191-225">The `{pubdate:datetime}` parameter is constrained to match a **DateTime** value.</span></span> <span data-ttu-id="e3191-226">Tento postup funguje, ale je ve skutečnosti mnohem mírnější, než jsme chtěli.</span><span class="sxs-lookup"><span data-stu-id="e3191-226">This works, but it's actually more permissive than we'd like.</span></span> <span data-ttu-id="e3191-227">Například tyto identifikátory URI se taky shodovat trasy:</span><span class="sxs-lookup"><span data-stu-id="e3191-227">For example, these URIs will also match the route:</span></span>

`/api/books/date/Thu, 01 May 2008`

`/api/books/date/2000-12-16T00:00:00`

<span data-ttu-id="e3191-228">Není nic špatného s povolením tyto identifikátory URI.</span><span class="sxs-lookup"><span data-stu-id="e3191-228">There's nothing wrong with allowing these URIs.</span></span> <span data-ttu-id="e3191-229">Můžete však omezit trasy na konkrétní formát přidáním omezení regulární výraz pro šablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="e3191-229">However, you can restrict the route to a particular format by adding a regular-expression constraint to the route template:</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample19.cs?highlight=1)]

<span data-ttu-id="e3191-230">Nyní pouze data ve formě &quot;rrrr mm-dd&quot; bude odpovídat.</span><span class="sxs-lookup"><span data-stu-id="e3191-230">Now only dates in the form &quot;yyyy-mm-dd&quot; will match.</span></span> <span data-ttu-id="e3191-231">Všimněte si, že nebudeme používat k ověření, že jsme získali skutečné datum regulární výraz.</span><span class="sxs-lookup"><span data-stu-id="e3191-231">Notice that we don't use the regex to validate that we got a real date.</span></span> <span data-ttu-id="e3191-232">Při pokusí převést segment identifikátoru URI do webového rozhraní API, která je zpracována **data a času** instance.</span><span class="sxs-lookup"><span data-stu-id="e3191-232">That is handled when Web API tries to convert the URI segment into a **DateTime** instance.</span></span> <span data-ttu-id="e3191-233">Neplatné datum jako je například "2012-47-99 se nepodařilo převést, a klient se zobrazí chyba 404.</span><span class="sxs-lookup"><span data-stu-id="e3191-233">An invalid date such as '2012-47-99' will fail to be converted, and the client will get a 404 error.</span></span>

<span data-ttu-id="e3191-234">Může také podporovat lomítko oddělovačem (`/api/books/date/yyyy/mm/dd`) tak, že přidáte další **[trasy]** atribut s jiný regulární výraz.</span><span class="sxs-lookup"><span data-stu-id="e3191-234">You can also support a slash separator (`/api/books/date/yyyy/mm/dd`) by adding another **[Route]** attribute with a different regex.</span></span>

[!code-html[Main](create-a-rest-api-with-attribute-routing/samples/sample20.html)]

<span data-ttu-id="e3191-235">Je zde podrobností drobným ale důležité.</span><span class="sxs-lookup"><span data-stu-id="e3191-235">There is a subtle but important detail here.</span></span> <span data-ttu-id="e3191-236">Druhá šablona trasy obsahuje zástupný znak (\*) na začátku parametru {pubdate}:</span><span class="sxs-lookup"><span data-stu-id="e3191-236">The second route template has a wildcard character (\*) at the start of the {pubdate} parameter:</span></span>

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample21.json)]

<span data-ttu-id="e3191-237">Modul směrování říká, že {pubdate} by měl odpovídat zbývající části identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="e3191-237">This tells the routing engine that {pubdate} should match the rest of the URI.</span></span> <span data-ttu-id="e3191-238">Ve výchozím nastavení odpovídá parametru šablony jeden segment identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="e3191-238">By default, a template parameter matches a single URI segment.</span></span> <span data-ttu-id="e3191-239">V tomto případě chceme {pubdate} span několik segmentů identifikátor URI:</span><span class="sxs-lookup"><span data-stu-id="e3191-239">In this case, we want {pubdate} to span several URI segments:</span></span>

`/api/books/date/2013/06/17`

## <a name="controller-code"></a><span data-ttu-id="e3191-240">Kódu kontroleru</span><span class="sxs-lookup"><span data-stu-id="e3191-240">Controller Code</span></span>

<span data-ttu-id="e3191-241">Tady je kompletní kód pro třídu BooksController.</span><span class="sxs-lookup"><span data-stu-id="e3191-241">Here is the complete code for the BooksController class.</span></span>

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample22.cs)]

## <a name="summary"></a><span data-ttu-id="e3191-242">Souhrn</span><span class="sxs-lookup"><span data-stu-id="e3191-242">Summary</span></span>

<span data-ttu-id="e3191-243">Směrování atributů nabízí další ovládací prvek a větší flexibilitu při navrhování identifikátory URI pro vaše rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e3191-243">Attribute routing gives you more control and greater flexibility when designing the URIs for your API.</span></span>
