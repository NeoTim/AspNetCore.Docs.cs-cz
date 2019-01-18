---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: Zobrazení datových položek a podrobností | Dokumentace Microsoftu
author: Erikre
description: V této sérii kurzů se seznámíte se základy vytváření aplikace webových formulářů ASP.NET s ASP.NET 4.7 a Microsoft Visual Studio 2017
ms.author: riande
ms.date: 1/04/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: acc2f8e78375ef0455d467e2af750ecbee623224
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396218"
---
<a name="display-data-items-and-details"></a><span data-ttu-id="66112-103">Zobrazení datových položek a podrobnosti</span><span class="sxs-lookup"><span data-stu-id="66112-103">Display data items and details</span></span>
====================
<span data-ttu-id="66112-104">by [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="66112-104">by [Erik Reitan](https://github.com/Erikre)</span></span>

> <span data-ttu-id="66112-105">V této sérii kurzů se naučíte se základy vytváření aplikace webových formulářů ASP.NET s ASP.NET 4.7 a Microsoft Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="66112-105">This tutorial series teaches you the basics of building an ASP.NET Web Forms application with ASP.NET 4.7 and Microsoft Visual Studio 2017.</span></span>

<span data-ttu-id="66112-106">V tomto kurzu se dozvíte, jak zobrazit datové položky a podrobnosti položky dat s webovými formuláři ASP.NET a Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="66112-106">In this tutorial, you'll learn how to display data items and data item details with ASP.NET Web Forms and Entity Framework Code First.</span></span> <span data-ttu-id="66112-107">V tomto kurzu navazuje na předchozí kurz o "Uživatelské rozhraní a navigace" jako část série kurzů Store Wingtip hračka.</span><span class="sxs-lookup"><span data-stu-id="66112-107">This tutorial builds on the previous "UI and Navigation" tutorial as part of the Wingtip Toy Store tutorial series.</span></span> <span data-ttu-id="66112-108">Po dokončení tohoto kurzu, zobrazí se vám produkty na *ProductsList.aspx* stránky a podrobnosti produktu na *ProductDetails.aspx* stránky.</span><span class="sxs-lookup"><span data-stu-id="66112-108">After completing this tutorial, you'll see products on the *ProductsList.aspx* page and a product's details on the *ProductDetails.aspx* page.</span></span>

## <a name="youll-learn-how-to"></a><span data-ttu-id="66112-109">Se dozvíte, jak:</span><span class="sxs-lookup"><span data-stu-id="66112-109">You'll learn how to:</span></span>

- <span data-ttu-id="66112-110">Přidejte ovládací prvek dat pro zobrazení produktů z databáze</span><span class="sxs-lookup"><span data-stu-id="66112-110">Add a data control to display products from the database</span></span>
- <span data-ttu-id="66112-111">Připojení ovládacího prvku na vybraná data</span><span class="sxs-lookup"><span data-stu-id="66112-111">Connect a data control to the selected data</span></span>
- <span data-ttu-id="66112-112">Přidejte ovládací prvek dat zobrazíte podrobnosti o produktu z databáze</span><span class="sxs-lookup"><span data-stu-id="66112-112">Add a data control to display product details from the database</span></span>
- <span data-ttu-id="66112-113">Načíst hodnotu z řetězce dotazu a tuto hodnotu použít k omezení dat načtených z databáze</span><span class="sxs-lookup"><span data-stu-id="66112-113">Retrieve a value from the query string and use that value to limit the data that's retrieved from the database</span></span>

### <a name="features-introduced-in-this-tutorial"></a><span data-ttu-id="66112-114">Vlastnosti představené v tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="66112-114">Features introduced in this tutorial:</span></span>

- <span data-ttu-id="66112-115">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="66112-115">Model binding</span></span>
- <span data-ttu-id="66112-116">Zprostředkovatele hodnot</span><span class="sxs-lookup"><span data-stu-id="66112-116">Value providers</span></span>

## <a name="add-a-data-control"></a><span data-ttu-id="66112-117">Přidejte ovládací prvek dat</span><span class="sxs-lookup"><span data-stu-id="66112-117">Add a data control</span></span>

<span data-ttu-id="66112-118">Vazba dat k ovládacímu prvku serveru, můžete použít několik různých možností.</span><span class="sxs-lookup"><span data-stu-id="66112-118">You can use a few different options to bind data to a server control.</span></span> <span data-ttu-id="66112-119">Nejběžnější patří:</span><span class="sxs-lookup"><span data-stu-id="66112-119">The most common include:</span></span>

 * <span data-ttu-id="66112-120">Přidání ovládacího prvku zdroje dat</span><span class="sxs-lookup"><span data-stu-id="66112-120">Adding a data source control</span></span>
 * <span data-ttu-id="66112-121">Ruční přidání kódu</span><span class="sxs-lookup"><span data-stu-id="66112-121">Adding code by hand</span></span>
 * <span data-ttu-id="66112-122">Pomocí vazby modelu</span><span class="sxs-lookup"><span data-stu-id="66112-122">Using model binding</span></span>

### <a name="use-a-data-source-control-to-bind-data"></a><span data-ttu-id="66112-123">Vytvoření vazby dat pomocí ovládacího prvku zdroje dat</span><span class="sxs-lookup"><span data-stu-id="66112-123">Use a data source control to bind data</span></span>

<span data-ttu-id="66112-124">Přidání ovládacího prvku zdroje dat umožňuje propojit ovládací prvek zdroje dat k ovládacímu prvku, který se zobrazí data.</span><span class="sxs-lookup"><span data-stu-id="66112-124">Adding a data source control allows you to link the data source control to the control that displays the data.</span></span> <span data-ttu-id="66112-125">S tímto přístupem můžete deklarativně, místo prostřednictvím kódu programu, připojení ke zdrojům dat serverové ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="66112-125">With this approach, you can declaratively,  rather than programmatically, connect server-side controls to data sources.</span></span>

### <a name="code-by-hand-to-bind-data"></a><span data-ttu-id="66112-126">Kód ručně pro vytvoření vazby dat</span><span class="sxs-lookup"><span data-stu-id="66112-126">Code by hand to bind data</span></span>

<span data-ttu-id="66112-127">Kódování ručně zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="66112-127">Coding by hand involves:</span></span>

1. <span data-ttu-id="66112-128">Čtení hodnoty</span><span class="sxs-lookup"><span data-stu-id="66112-128">Reading a value</span></span>
2. <span data-ttu-id="66112-129">Kontrola, zda je null</span><span class="sxs-lookup"><span data-stu-id="66112-129">Checking if it's null</span></span>
3. <span data-ttu-id="66112-130">Převod na příslušný typ.</span><span class="sxs-lookup"><span data-stu-id="66112-130">Converting it to an appropriate type</span></span>
4. <span data-ttu-id="66112-131">Kontrola úspěchu převodu</span><span class="sxs-lookup"><span data-stu-id="66112-131">Checking conversion success</span></span>
5. <span data-ttu-id="66112-132">Pomocí hodnoty v dotazu</span><span class="sxs-lookup"><span data-stu-id="66112-132">Using the value in the query</span></span> 

<span data-ttu-id="66112-133">Tento přístup umožňuje mít plnou kontrolu nad logiky přístupu k datům.</span><span class="sxs-lookup"><span data-stu-id="66112-133">This approach lets you have full control over your data-access logic.</span></span>

### <a name="use-model-binding-to-bind-data"></a><span data-ttu-id="66112-134">Svázat data pomocí vazby modelu</span><span class="sxs-lookup"><span data-stu-id="66112-134">Use model binding to bind data</span></span>

<span data-ttu-id="66112-135">Vazby modelu vám umožňuje vytvořit vazbu výsledky s mnohem menším množstvím kódu a poskytuje možnosti opakovaně používat funkce v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="66112-135">Model binding lets you bind results with far less code and gives you the ability to reuse the functionality throughout your application.</span></span> <span data-ttu-id="66112-136">To zjednodušuje práci s logiky zaměřený na kód – přístup k datům při stálém poskytování bohatých a datové vazby framework.</span><span class="sxs-lookup"><span data-stu-id="66112-136">It simplifies working with code-focused data-access logic while still providing a rich, data-binding framework.</span></span>

## <a name="display-products"></a><span data-ttu-id="66112-137">Zobrazit produkty</span><span class="sxs-lookup"><span data-stu-id="66112-137">Display products</span></span>

<span data-ttu-id="66112-138">V tomto kurzu použijete k vytvoření vazby dat vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="66112-138">In this tutorial, you'll use model binding to bind data.</span></span> <span data-ttu-id="66112-139">Chcete-li nakonfigurovat ovládací prvek data pomocí vazby modelu vyberte data, nastavte ovládacího prvku `SelectMethod` vlastnost s názvem metody v kódu stránky.</span><span class="sxs-lookup"><span data-stu-id="66112-139">To configure a data control to use model binding to select data, you set the control's `SelectMethod` property to a method name in the page's code.</span></span> <span data-ttu-id="66112-140">Ovládací prvek dat volá metodu v příslušnou dobu v životním cyklu stránky a automaticky sváže s vrácenými daty.</span><span class="sxs-lookup"><span data-stu-id="66112-140">The data control calls the method at the appropriate time in the page life cycle and automatically binds the returned data.</span></span> <span data-ttu-id="66112-141">Není nutné explicitně volat `DataBind` metody.</span><span class="sxs-lookup"><span data-stu-id="66112-141">There's no need to explicitly call the `DataBind` method.</span></span>

1. <span data-ttu-id="66112-142">V **Průzkumníka řešení**, otevřete *ProductList.aspx*.</span><span class="sxs-lookup"><span data-stu-id="66112-142">In **Solution Explorer**, open *ProductList.aspx*.</span></span>
2. <span data-ttu-id="66112-143">Nahraďte stávající kód tento kód:</span><span class="sxs-lookup"><span data-stu-id="66112-143">Replace the existing markup with this markup:</span></span>   

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

<span data-ttu-id="66112-144">Tento kód používá **ListView** ovládací prvek s názvem `productList` pro zobrazení produktů.</span><span class="sxs-lookup"><span data-stu-id="66112-144">This code uses a **ListView** control named `productList` to display products.</span></span>

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

<span data-ttu-id="66112-145">Styly a šablony, můžete definovat jak **ListView** ovládací prvek zobrazí data.</span><span class="sxs-lookup"><span data-stu-id="66112-145">With templates and styles, you define how the **ListView** control displays data.</span></span> <span data-ttu-id="66112-146">Je vhodné pro data v libovolné opakující se struktura.</span><span class="sxs-lookup"><span data-stu-id="66112-146">It's useful for data in any repeating structure.</span></span> <span data-ttu-id="66112-147">I když to **ListView** příklad jednoduše zobrazí dat z databáze, můžete navíc bez kódu, umožňují uživatelům pro úpravy, vložení a odstranění dat a řadit a stránkovat data.</span><span class="sxs-lookup"><span data-stu-id="66112-147">Though this **ListView** example simply displays database data, you can also, without code, enable users to edit, insert, and delete data, and to sort and page data.</span></span>

<span data-ttu-id="66112-148">Nastavením `ItemType` vlastnost **ListView** řídit vazbový výraz `Item` je k dispozici a ovládací prvek stane silného typu.</span><span class="sxs-lookup"><span data-stu-id="66112-148">By setting the `ItemType` property in the **ListView** control, the data-binding expression `Item` is available and the control becomes strongly typed.</span></span> <span data-ttu-id="66112-149">Jak je uvedeno v předchozím kurzu, můžete vybrat Podrobnosti objektu položky s podporou technologie IntelliSense, jako je například určení `ProductName`:</span><span class="sxs-lookup"><span data-stu-id="66112-149">As mentioned in the previous tutorial, you can select Item object details with IntelliSense, such as specifying the `ProductName`:</span></span>

![Zobrazení dat položek a podrobnosti o – technologie IntelliSense](display_data_items_and_details/_static/image1.png)

<span data-ttu-id="66112-151">Také používáte vazby modelu k určení `SelectMethod` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="66112-151">You're also using model binding to specify a `SelectMethod` value.</span></span> <span data-ttu-id="66112-152">Tato hodnota (`GetProducts`) odpovídá metodu přidejte do kódu vzadu pro zobrazení produktů v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="66112-152">This value (`GetProducts`) corresponds to the method you'll add to the code behind to display products in the next step.</span></span>

### <a name="add-code-to-display-products"></a><span data-ttu-id="66112-153">Přidejte kód pro zobrazení produktů</span><span class="sxs-lookup"><span data-stu-id="66112-153">Add code to display products</span></span>

<span data-ttu-id="66112-154">V tomto kroku přidáte kód k vyplnění **ListView** ovládacího prvku pomocí produktu data z databáze.</span><span class="sxs-lookup"><span data-stu-id="66112-154">In this step, you'll add code to populate the **ListView** control with product data from the database.</span></span> <span data-ttu-id="66112-155">Kód podporuje zobrazení všech produktů a jednotlivé kategorie produktů.</span><span class="sxs-lookup"><span data-stu-id="66112-155">The code supports showing all products and  individual category products.</span></span>

1. <span data-ttu-id="66112-156">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *ProductList.aspx* a pak vyberte **zobrazit kód**.</span><span class="sxs-lookup"><span data-stu-id="66112-156">In **Solution Explorer**, right-click *ProductList.aspx* and then select **View Code**.</span></span>
2. <span data-ttu-id="66112-157">Nahraďte existující kód ve třídě *ProductList.aspx.cs* soubor s tímto:</span><span class="sxs-lookup"><span data-stu-id="66112-157">Replace the existing code in the *ProductList.aspx.cs* file with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

<span data-ttu-id="66112-158">Tento kód ukazuje `GetProducts` metoda, která **ListView** ovládacího prvku `ItemType` odkazuje na vlastnost v *ProductList.aspx* stránky.</span><span class="sxs-lookup"><span data-stu-id="66112-158">This code shows the `GetProducts` method that the **ListView** control's `ItemType` property references in the *ProductList.aspx* page.</span></span> <span data-ttu-id="66112-159">Omezit rozsah výsledků do kategorie konkrétní databáze, nastaví kód `categoryId` hodnotu z hodnotu řetězce dotazu, který je předán *ProductList.aspx* stránce, pokud *ProductList.aspx* stránka je Přejde.</span><span class="sxs-lookup"><span data-stu-id="66112-159">To limit the results to a specific database category, the code sets the `categoryId` value from the query string value passed to the *ProductList.aspx* page when the *ProductList.aspx* page is navigated to.</span></span> <span data-ttu-id="66112-160">`QueryStringAttribute` Třídy v `System.Web.ModelBinding` obor názvů slouží k načtení hodnoty proměnné řetězce dotazu `id`.</span><span class="sxs-lookup"><span data-stu-id="66112-160">The `QueryStringAttribute` class in the `System.Web.ModelBinding` namespace is used to retrieve the value of the query string variable `id`.</span></span> <span data-ttu-id="66112-161">Toto dá pokyn vazby modelu pro pokus o vytvoření vazby hodnotu z řetězce dotazu do `categoryId` parametr v době běhu.</span><span class="sxs-lookup"><span data-stu-id="66112-161">This instructs model binding to try to bind a value from the query string to the `categoryId` parameter at run time.</span></span>

<span data-ttu-id="66112-162">Když platnou kategorii je předán jako řetězec dotazu na stránce výsledky dotazu jsou omezené na tyto produkty v databázi, které odpovídají `categoryId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="66112-162">When a valid category is passed as a query string to the page, the results of the query are limited to those products in the database that match the `categoryId` value.</span></span> <span data-ttu-id="66112-163">Například pokud *ProductsList.aspx* Toto je adresa URL stránky:</span><span class="sxs-lookup"><span data-stu-id="66112-163">For instance, if the *ProductsList.aspx* page URL is this:</span></span>


[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

<span data-ttu-id="66112-164">Na stránce se zobrazí pouze produkty, kde `categoryId` rovná `1`.</span><span class="sxs-lookup"><span data-stu-id="66112-164">The page displays only the products where the `categoryId` equals `1`.</span></span>

<span data-ttu-id="66112-165">Všechny produkty se zobrazí, pokud žádný řetězec dotazu je zahrnuta, když *ProductList.aspx* stránka se nazývá.</span><span class="sxs-lookup"><span data-stu-id="66112-165">All products are displayed if no query string is included when the *ProductList.aspx* page is called.</span></span>

<span data-ttu-id="66112-166">Zdroje hodnoty pro tyto metody jsou označovány jako *hodnota poskytovatelů* (například *QueryString*), a parametr atributy, které označují které zprostředkovatele hodnot pro použití se označují jako *hodnoty atributů poskytovatele* (například `id`).</span><span class="sxs-lookup"><span data-stu-id="66112-166">The sources of values for these methods are referred to as *value providers* (such as *QueryString*), and the parameter attributes that indicate which value provider to use are referred to as *value provider attributes* (such as `id`).</span></span> <span data-ttu-id="66112-167">Technologie ASP.NET obsahuje zprostředkovatele hodnot a odpovídající atributy pro všemi typické zdroji uživatelský vstup v aplikaci webových formulářů, jako je například řetězec dotazu, soubory cookie, hodnot formuláře, ovládací prvky, zobrazit stav, stav relace a vlastnosti profilu.</span><span class="sxs-lookup"><span data-stu-id="66112-167">ASP.NET includes value providers and corresponding attributes for all of the typical sources of user input in a Web Forms application such as the query string, cookies, form values, controls, view state, session state, and profile properties.</span></span> <span data-ttu-id="66112-168">Můžete taky psát vlastní hodnotu poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="66112-168">You can also write custom value providers.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="66112-169">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="66112-169">Run the application</span></span>

<span data-ttu-id="66112-170">Spusťte aplikaci nyní chcete-li zobrazit všechny produkty nebo kategorie produktů.</span><span class="sxs-lookup"><span data-stu-id="66112-170">Run the application now to view all products or a category's products.</span></span>

1. <span data-ttu-id="66112-171">Stisknutím klávesy **F5** během činnosti v sadě Visual Studio ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="66112-171">Press **F5** while in Visual Studio to run the application.</span></span>  
   <span data-ttu-id="66112-172">V prohlížeči se otevře a zobrazí *Default.aspx* stránky.</span><span class="sxs-lookup"><span data-stu-id="66112-172">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="66112-173">Vyberte **auta** z navigační nabídky kategorie produktu.</span><span class="sxs-lookup"><span data-stu-id="66112-173">Select **Cars** from the product category navigation menu.</span></span>  
   <span data-ttu-id="66112-174">*ProductList.aspx* stránce zobrazí pouze **auta** kategorie produktů.</span><span class="sxs-lookup"><span data-stu-id="66112-174">The *ProductList.aspx* page displays showing only **Cars** category products.</span></span> <span data-ttu-id="66112-175">Později v tomto kurzu zobrazíte podrobnosti o produktu.</span><span class="sxs-lookup"><span data-stu-id="66112-175">Later in this tutorial, you'll display product details.</span></span>  

    ![Zobrazení dat položek a podrobnosti - automobilů](display_data_items_and_details/_static/image2.png)

3. <span data-ttu-id="66112-177">Vyberte **produkty** z navigační nabídce v horní části.</span><span class="sxs-lookup"><span data-stu-id="66112-177">Select **Products** from the navigation menu at the top.</span></span>  
   <span data-ttu-id="66112-178">Znovu *ProductList.aspx* se zobrazí stránka, ale tentokrát zobrazuje úplný seznam produktů.</span><span class="sxs-lookup"><span data-stu-id="66112-178">Again, the *ProductList.aspx* page is displayed, however this time it shows the entire list of products.</span></span>   

    ![Zobrazení dat položek a podrobnosti - produkty](display_data_items_and_details/_static/image3.png)

4. <span data-ttu-id="66112-180">Zavřete prohlížeč a vraťte se do sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="66112-180">Close the browser and return to Visual Studio.</span></span>

### <a name="add-a-data-control-to-display-product-details"></a><span data-ttu-id="66112-181">Přidejte ovládací prvek dat zobrazíte podrobnosti o produktu</span><span class="sxs-lookup"><span data-stu-id="66112-181">Add a data control to display product details</span></span>

<span data-ttu-id="66112-182">V dalším kroku upravíte značky *ProductDetails.aspx* stránky, které jste přidali v předchozím kurzu zobrazíte informace o konkrétních produktech.</span><span class="sxs-lookup"><span data-stu-id="66112-182">Next, you'll modify the markup in the *ProductDetails.aspx* page that you added in the previous tutorial to display specific product information.</span></span>

1. <span data-ttu-id="66112-183">V **Průzkumníka řešení**, otevřete *ProductDetails.aspx*.</span><span class="sxs-lookup"><span data-stu-id="66112-183">In **Solution Explorer**, open *ProductDetails.aspx*.</span></span>

2. <span data-ttu-id="66112-184">Nahraďte stávající kód tento kód:</span><span class="sxs-lookup"><span data-stu-id="66112-184">Replace the existing markup with this markup:</span></span>

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

    <span data-ttu-id="66112-185">Tento kód používá **FormView** ovládací prvek pro zobrazení Podrobnosti o konkrétního produktu.</span><span class="sxs-lookup"><span data-stu-id="66112-185">This code uses a **FormView** control to display specific product details.</span></span> <span data-ttu-id="66112-186">Tento kód použije metody jako metody slouží k zobrazení dat v *ProductList.aspx* stránky.</span><span class="sxs-lookup"><span data-stu-id="66112-186">This markup uses methods like the methods used to display data in the *ProductList.aspx* page.</span></span> <span data-ttu-id="66112-187">**FormView** ovládacího prvku se používá k zobrazení jeden záznam v čase ze zdroje dat.</span><span class="sxs-lookup"><span data-stu-id="66112-187">The **FormView** control is used to display a single record at a time from a data source.</span></span> <span data-ttu-id="66112-188">Při použití **FormView** ovládacího prvku, je vytvořit šablony můžete zobrazit a upravit hodnoty vázané na data.</span><span class="sxs-lookup"><span data-stu-id="66112-188">When you use the **FormView** control, you create templates to display and edit data-bound values.</span></span> <span data-ttu-id="66112-189">Tyto šablony obsahují ovládací prvky, výrazy, vazby a formátování, které definují vzhled formuláře a funkce.</span><span class="sxs-lookup"><span data-stu-id="66112-189">These templates contain controls, binding expressions, and formatting that define the form's look and functionality.</span></span>

<span data-ttu-id="66112-190">Předchozí kód s připojením k databázi se vyžaduje další kód.</span><span class="sxs-lookup"><span data-stu-id="66112-190">Connecting the previous markup to the database requires additional code.</span></span>

1. <span data-ttu-id="66112-191">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *ProductDetails.aspx* a potom klikněte na tlačítko **zobrazit kód**.</span><span class="sxs-lookup"><span data-stu-id="66112-191">In **Solution Explorer**, right-click *ProductDetails.aspx* and then click **View Code**.</span></span>  
   <span data-ttu-id="66112-192">*ProductDetails.aspx.cs* soubor se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="66112-192">The *ProductDetails.aspx.cs* file is displayed.</span></span>

2. <span data-ttu-id="66112-193">Nahraďte stávající kód s tímto kódem:</span><span class="sxs-lookup"><span data-stu-id="66112-193">Replace the existing code with this code:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

<span data-ttu-id="66112-194">Tento kód kontroluje "`productID`" hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="66112-194">This code checks for a "`productID`" query-string value.</span></span> <span data-ttu-id="66112-195">Pokud není nalezena hodnota platný řetězec dotazu, zobrazí se odpovídající produkt.</span><span class="sxs-lookup"><span data-stu-id="66112-195">If a valid query-string value is found, the matching product is displayed.</span></span> <span data-ttu-id="66112-196">Pokud řetězec dotazu nebyl nalezen nebo její hodnota není platná, zobrazí se žádné produkty.</span><span class="sxs-lookup"><span data-stu-id="66112-196">If the query-string isn't found, or its value isn't valid, no product is displayed.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="66112-197">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="66112-197">Run the application</span></span>

<span data-ttu-id="66112-198">Nyní můžete spustit aplikaci, abyste viděli zobrazí jednotlivé produkty podle ID produktu.</span><span class="sxs-lookup"><span data-stu-id="66112-198">Now you can run the application to see an individual product displayed based on product ID.</span></span>

1. <span data-ttu-id="66112-199">Stisknutím klávesy **F5** během činnosti v sadě Visual Studio ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="66112-199">Press **F5** while in Visual Studio to run the application.</span></span>  
   <span data-ttu-id="66112-200">V prohlížeči se otevře a zobrazí *Default.aspx* stránky.</span><span class="sxs-lookup"><span data-stu-id="66112-200">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="66112-201">Vyberte **lodě** navigační nabídce kategorie.</span><span class="sxs-lookup"><span data-stu-id="66112-201">Select **Boats** from the category navigation menu.</span></span>  
   <span data-ttu-id="66112-202">*ProductList.aspx* zobrazí se stránka.</span><span class="sxs-lookup"><span data-stu-id="66112-202">The *ProductList.aspx* page is displayed.</span></span>

3. <span data-ttu-id="66112-203">Vyberte **papíru loď** ze seznamu produktů.</span><span class="sxs-lookup"><span data-stu-id="66112-203">Select **Paper Boat** from the product list.</span></span>
   <span data-ttu-id="66112-204">*ProductDetails.aspx* zobrazí se stránka.</span><span class="sxs-lookup"><span data-stu-id="66112-204">The *ProductDetails.aspx* page is displayed.</span></span>

    ![Zobrazení dat položek a podrobnosti - produkty](display_data_items_and_details/_static/image4.png)
    
4. <span data-ttu-id="66112-206">Zavřete prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="66112-206">Close the browser.</span></span>


## <a name="additional-resources"></a><span data-ttu-id="66112-207">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="66112-207">Additional resources</span></span>

[<span data-ttu-id="66112-208">Načtení a zobrazení dat ovládacím prvkem vazby modelu a webové formuláře</span><span class="sxs-lookup"><span data-stu-id="66112-208">Retrieving and displaying data with model binding and web forms</span></span>](../../presenting-and-managing-data/model-binding/retrieving-data.md)

## <a name="next-steps"></a><span data-ttu-id="66112-209">Další kroky</span><span class="sxs-lookup"><span data-stu-id="66112-209">Next steps</span></span>

<span data-ttu-id="66112-210">V tomto kurzu jste přidali značek a kódu zobrazit produkty a podrobnosti o produktu.</span><span class="sxs-lookup"><span data-stu-id="66112-210">In this tutorial, you added markup and code to display products and product details.</span></span> <span data-ttu-id="66112-211">Jste se dozvěděli o ovládací prvky dat silného typu, vazby modelu a zprostředkovatele hodnot.</span><span class="sxs-lookup"><span data-stu-id="66112-211">You learned about strongly typed data controls, model binding, and value providers.</span></span> <span data-ttu-id="66112-212">V dalším kurzu přidáte do nákupního košíku ukázkové aplikace Wingtip Toys.</span><span class="sxs-lookup"><span data-stu-id="66112-212">In the next tutorial, you'll add a shopping cart to the Wingtip Toys sample application.</span></span> 

> [!div class="step-by-step"]
> <span data-ttu-id="66112-213">[Předchozí](ui_and_navigation.md)
> [další](shopping-cart.md)</span><span class="sxs-lookup"><span data-stu-id="66112-213">[Previous](ui_and_navigation.md)
[Next](shopping-cart.md)</span></span>
