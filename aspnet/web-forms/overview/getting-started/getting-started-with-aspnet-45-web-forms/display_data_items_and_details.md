---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: Zobrazení datových položek a podrobností | Dokumentace Microsoftu
author: Erikre
description: V této sérii kurzů se seznámíte se základy vytváření aplikace webových formulářů ASP.NET s ASP.NET 4.7 a Microsoft Visual Studio Community 2017 pro Web
ms.author: riande
ms.date: 1/09/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: 73ae1660f5d6e3e28c1c155e745a62936e3502df
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207431"
---
<a name="display-data-items-and-details"></a><span data-ttu-id="bdf6d-103">Zobrazení datových položek a podrobnosti</span><span class="sxs-lookup"><span data-stu-id="bdf6d-103">Display data items and details</span></span>
====================
<span data-ttu-id="bdf6d-104">podle [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="bdf6d-104">by [Erik Reitan](https://github.com/Erikre)</span></span>

> <span data-ttu-id="bdf6d-105">V této sérii kurzů se naučíte se základy vytváření aplikace webových formulářů ASP.NET s ASP.NET 4.7 a Microsoft Visual Studio Community 2017 pro Web.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-105">This tutorial series teaches you the basics of building an ASP.NET Web Forms application with ASP.NET 4.7 and Microsoft Visual Studio Community 2017 for the Web.</span></span>

<span data-ttu-id="bdf6d-106">V tomto kurzu se dozvíte, jak k zobrazení datových položek a podrobnosti položky dat s webovými formuláři ASP.NET a Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-106">In this tutorial, you learn how to display data items and data item details with ASP.NET Web Forms and Entity Framework Code First.</span></span> <span data-ttu-id="bdf6d-107">V tomto kurzu navazuje na předchozí kurz o "Uživatelské rozhraní a navigace" jako část série kurzů Store Wingtip hračka.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-107">This tutorial builds on the previous "UI and Navigation" tutorial as part of the Wingtip Toy Store tutorial series.</span></span> <span data-ttu-id="bdf6d-108">V kurzu dokončené produkty na *ProductsList.aspx* stránky a podrobnosti produktu na *ProductDetails.aspx* se zobrazí stránka.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-108">In the completed tutorial, products on the *ProductsList.aspx* page and a product's details on the *ProductDetails.aspx* page are displayed.</span></span>

## <a name="what-you-learn"></a><span data-ttu-id="bdf6d-109">Co jste se naučili</span><span class="sxs-lookup"><span data-stu-id="bdf6d-109">What you learn</span></span>

- <span data-ttu-id="bdf6d-110">Přidejte ovládací prvek dat pro zobrazení databáze produktů.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-110">Add a data control to display database products.</span></span>
- <span data-ttu-id="bdf6d-111">Připojení ovládacího prvku na vybraná data.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-111">Connect a data control to selected data.</span></span>
- <span data-ttu-id="bdf6d-112">Přidejte ovládací prvek dat zobrazíte podrobnosti o produktu.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-112">Add a data control to display product details.</span></span>
- <span data-ttu-id="bdf6d-113">Analyzovat hodnotu řetězce dotazu a použít ho k filtrování dat z načtené databáze.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-113">Parse a query string value and use it to filter retrieved database data.</span></span>

<span data-ttu-id="bdf6d-114">Vlastnosti představené v tomto kurzu zahrnují vazby modelu a zprostředkovatele hodnot.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-114">Features introduced in this tutorial include model binding and value providers.</span></span>

## <a name="add-a-data-control-to-display-products"></a><span data-ttu-id="bdf6d-115">Přidejte ovládací prvek dat pro zobrazení produktů</span><span class="sxs-lookup"><span data-stu-id="bdf6d-115">Add a data control to display products</span></span>
 
<span data-ttu-id="bdf6d-116">Máte několik možností vytvoření vazby dat k ovládacímu prvku serveru.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-116">You have a few options to bind data to a server control.</span></span> <span data-ttu-id="bdf6d-117">Nejběžnější patří:</span><span class="sxs-lookup"><span data-stu-id="bdf6d-117">The most common include:</span></span>

 * <span data-ttu-id="bdf6d-118">Přidání ovládacího prvku zdroje dat</span><span class="sxs-lookup"><span data-stu-id="bdf6d-118">Adding a data source control</span></span>
 * <span data-ttu-id="bdf6d-119">Ruční přidání kódu</span><span class="sxs-lookup"><span data-stu-id="bdf6d-119">Adding code by hand</span></span>
 * <span data-ttu-id="bdf6d-120">Implementace vazby modelu</span><span class="sxs-lookup"><span data-stu-id="bdf6d-120">Implementing model binding</span></span>

### <a name="use-a-data-source-control-to-bind-data"></a><span data-ttu-id="bdf6d-121">Vytvoření vazby dat pomocí ovládacího prvku zdroje dat</span><span class="sxs-lookup"><span data-stu-id="bdf6d-121">Use a data source control to bind data</span></span>

<span data-ttu-id="bdf6d-122">Přidání ovládacího prvku zdroje dat odkazuje ovládací prvek zdroje dat na ovládací prvek, který zobrazuje data.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-122">Adding a data source control links the data source control to the control that displays the data.</span></span> <span data-ttu-id="bdf6d-123">S tímto přístupem můžete deklarativně, místo prostřednictvím kódu programu, připojení ke zdrojům dat serverové ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-123">With this approach, you can declaratively, rather than programmatically, connect server-side controls to data sources.</span></span>

### <a name="code-by-hand-to-bind-data"></a><span data-ttu-id="bdf6d-124">Kód ručně pro vytvoření vazby dat</span><span class="sxs-lookup"><span data-stu-id="bdf6d-124">Code by hand to bind data</span></span>

<span data-ttu-id="bdf6d-125">Kódování ručně zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="bdf6d-125">Coding by hand involves:</span></span>

1. <span data-ttu-id="bdf6d-126">Čtení hodnoty</span><span class="sxs-lookup"><span data-stu-id="bdf6d-126">Reading a value</span></span>
2. <span data-ttu-id="bdf6d-127">Kontrola, zda je null</span><span class="sxs-lookup"><span data-stu-id="bdf6d-127">Checking if it's null</span></span>
3. <span data-ttu-id="bdf6d-128">Převod na příslušný typ.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-128">Converting it to an appropriate type</span></span>
4. <span data-ttu-id="bdf6d-129">Kontrola úspěchu převodu</span><span class="sxs-lookup"><span data-stu-id="bdf6d-129">Checking conversion success</span></span>
5. <span data-ttu-id="bdf6d-130">Provádění dotazu se převedená hodnota</span><span class="sxs-lookup"><span data-stu-id="bdf6d-130">Making a query with the converted value</span></span> 

<span data-ttu-id="bdf6d-131">S tímto přístupem budete mít plnou kontrolu nad logiky přístupu k datům.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-131">With this approach, you have full control over your data-access logic.</span></span>

### <a name="use-model-binding-to-bind-data"></a><span data-ttu-id="bdf6d-132">Svázat data pomocí vazby modelu</span><span class="sxs-lookup"><span data-stu-id="bdf6d-132">Use model binding to bind data</span></span>

<span data-ttu-id="bdf6d-133">Vazby modelu svážete výsledky s mnohem menším množstvím kódu a poskytuje možnosti opakovaně používat funkce v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-133">With model binding, you bind results with far less code and it gives you the ability to reuse the functionality throughout your application.</span></span> <span data-ttu-id="bdf6d-134">To zjednodušuje práci s logiky zaměřený na kód – přístup k datům při stálém poskytování bohatých a datové vazby framework.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-134">It simplifies working with code-focused data-access logic while still providing a rich, data-binding framework.</span></span>

## <a name="display-products"></a><span data-ttu-id="bdf6d-135">Zobrazit produkty</span><span class="sxs-lookup"><span data-stu-id="bdf6d-135">Display products</span></span>

<span data-ttu-id="bdf6d-136">V tomto kurzu použijete k vytvoření vazby dat vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-136">In this tutorial, you use model binding to bind data.</span></span> <span data-ttu-id="bdf6d-137">Chcete-li nakonfigurovat ovládací prvek data pomocí vazby modelu vyberte data, nastavte ovládacího prvku `SelectMethod` vlastnost na metodu v kódu stránky.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-137">To configure a data control to use model binding to select data, you set the control's `SelectMethod` property to a method in the page's code.</span></span> <span data-ttu-id="bdf6d-138">Ovládací prvek dat volá metodu v příslušnou dobu v životním cyklu stránky a automaticky sváže s vrácenými daty.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-138">The data control calls the method at the appropriate time in the page life cycle and automatically binds the returned data.</span></span> <span data-ttu-id="bdf6d-139">Není nutné explicitně volat `DataBind` metody.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-139">There's no need to explicitly call the `DataBind` method.</span></span>

<span data-ttu-id="bdf6d-140">Práce pomocí následujících kroků, upravíte *ProductList.aspx* značek pro zobrazení produktů.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-140">Working through the following steps, you modify *ProductList.aspx* markup to display products.</span></span>

1. <span data-ttu-id="bdf6d-141">V **Průzkumníka řešení**, otevřete *ProductList.aspx*.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-141">In **Solution Explorer**, open *ProductList.aspx*.</span></span>

2. <span data-ttu-id="bdf6d-142">Nahraďte stávající kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="bdf6d-142">Replace the existing markup with the following markup:</span></span> 

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

<span data-ttu-id="bdf6d-143">Předchozí kód používá **ListView** ovládací prvek s názvem `productList` pro zobrazení produktů.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-143">The preceding markup uses a **ListView** control named `productList` to display products.</span></span>

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

<span data-ttu-id="bdf6d-144">Styly a šablony, můžete definovat jak **ListView** ovládací prvek zobrazí data.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-144">With templates and styles, you define how the **ListView** control displays data.</span></span> <span data-ttu-id="bdf6d-145">Je vhodné pro data v libovolné opakující se struktura.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-145">It's useful for data in any repeating structure.</span></span> <span data-ttu-id="bdf6d-146">I když to **ListView** příklad jednoduše zobrazí dat z databáze, můžete navíc bez kódu, umožňují uživatelům pro úpravy, vložení a odstranění dat a řadit a stránkovat data.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-146">Though this **ListView** example simply displays database data, you can also, without code, enable users to edit, insert, and delete data, and to sort and page data.</span></span>

<span data-ttu-id="bdf6d-147">Při nastavení `ItemType` vlastnost **ListView** řídit vazbový výraz `Item` je k dispozici a ovládací prvek stane silného typu.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-147">When you set the `ItemType` property in the **ListView** control, the data-binding expression `Item` is available and the control becomes strongly typed.</span></span> <span data-ttu-id="bdf6d-148">Jak je uvedeno v předchozím kurzu, můžete vybrat Podrobnosti objektu položky s podporou technologie IntelliSense, jako je například určení `ProductName`:</span><span class="sxs-lookup"><span data-stu-id="bdf6d-148">As mentioned in the previous tutorial, you can select Item object details with IntelliSense, such as specifying the `ProductName`:</span></span>

![Zobrazení dat položek a podrobnosti o – technologie IntelliSense](display_data_items_and_details/_static/image1.png)

<span data-ttu-id="bdf6d-150">S vazbou modelu, zadáváte `SelectMethod` hodnotu (`GetProducts`).</span><span class="sxs-lookup"><span data-stu-id="bdf6d-150">With model binding, you're specifying a `SelectMethod` value (`GetProducts`).</span></span> <span data-ttu-id="bdf6d-151">Toto je metoda, přidejte do kódu vzadu pro zobrazení produktů v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-151">This is the method you add to the code behind to display products in the next step.</span></span>

### <a name="add-code-to-display-products"></a><span data-ttu-id="bdf6d-152">Přidejte kód pro zobrazení produktů</span><span class="sxs-lookup"><span data-stu-id="bdf6d-152">Add code to display products</span></span>

<span data-ttu-id="bdf6d-153">V tomto kroku přidáte kód k naplnění **ListView** ovládacího prvku pomocí dat z databáze produktů.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-153">In this step, you add code to populate the **ListView** control with database product data.</span></span> <span data-ttu-id="bdf6d-154">Kód podporuje zobrazení všech produktů a jednotlivé kategorie produktů.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-154">The code supports showing all products and individual category products.</span></span>

1. <span data-ttu-id="bdf6d-155">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *ProductList.aspx* a pak vyberte **zobrazit kód**.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-155">In **Solution Explorer**, right-click *ProductList.aspx* and then select **View Code**.</span></span>
2. <span data-ttu-id="bdf6d-156">Nahraďte existující kód ve třídě *ProductList.aspx.cs* soubor s tímto:</span><span class="sxs-lookup"><span data-stu-id="bdf6d-156">Replace the existing code in the *ProductList.aspx.cs* file with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

<span data-ttu-id="bdf6d-157">Tento kód ukazuje `GetProducts` metoda, která **ListView** ovládacího prvku `ItemType` odkazuje na vlastnost v *ProductList.aspx*.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-157">This code shows the `GetProducts` method that the **ListView** control's `ItemType` property references in *ProductList.aspx*.</span></span> <span data-ttu-id="bdf6d-158">Omezit rozsah výsledků do kategorie konkrétní databáze, nastaví kód `categoryId` hodnotu z řetězce dotazu, který je předán *ProductList.aspx*.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-158">To limit the results to a specific database category, the code sets the `categoryId` value from the query string passed to *ProductList.aspx*.</span></span> <span data-ttu-id="bdf6d-159">`QueryStringAttribute` Třídy v `System.Web.ModelBinding` obor názvů slouží k načtení proměnné řetězce dotazu `id`hodnotu.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-159">The `QueryStringAttribute` class in the `System.Web.ModelBinding` namespace is used to retrieve the query string variable `id`'s value.</span></span> <span data-ttu-id="bdf6d-160">Toto dá pokyn modelu, v době běhu, kterým se sváže vazba hodnotu řetězce dotazu do `categoryId` parametru.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-160">This instructs model binding to, at run time, bind a query string value to the `categoryId` parameter.</span></span>

<span data-ttu-id="bdf6d-161">Když platnou kategorii (`categoryId`) je předán, výsledky jsou omezeny na databázi produkty dané kategorie.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-161">When a valid category (`categoryId`) is passed, the results are limited to that category's database products.</span></span> <span data-ttu-id="bdf6d-162">Například pokud *ProductsList.aspx* Toto je adresa URL stránky:</span><span class="sxs-lookup"><span data-stu-id="bdf6d-162">For instance, if the *ProductsList.aspx* page URL is this:</span></span>

[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

<span data-ttu-id="bdf6d-163">Na stránce se zobrazí pouze produkty, kde `categoryId` rovná `1`.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-163">The page displays only the products where the `categoryId` equals `1`.</span></span>

<span data-ttu-id="bdf6d-164">Pokud není předán žádný řetězec dotazu, zobrazí se všechny produkty.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-164">All products are displayed if no query string is passed.</span></span>

<span data-ttu-id="bdf6d-165">Zdrojů hodnot pro tyto metody jsou volány *hodnota poskytovatelé* (například `QueryString`), a parametr atributy, které označují které zprostředkovatele hodnot pro použití se nazývají *hodnoty atributů poskytovatele* ( například `id`).</span><span class="sxs-lookup"><span data-stu-id="bdf6d-165">The value sources for these methods are called *value providers* (such as `QueryString`), and the parameter attributes that indicate which value provider to use are called *value provider attributes* (such as `id`).</span></span> <span data-ttu-id="bdf6d-166">Technologie ASP.NET obsahuje zprostředkovatele hodnot a atributů pro všechny typické webové formuláře aplikace uživatele vstupního zdroje.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-166">ASP.NET includes value providers and attributes for all typical Web Forms application user input sources.</span></span> <span data-ttu-id="bdf6d-167">Patří mezi ně řetězec dotazu, soubory cookie, hodnot formuláře, ovládací prvky, zobrazit stav, stav relace a vlastnosti profilu.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-167">These include the query string, cookies, form values, controls, view state, session state, and profile properties.</span></span> <span data-ttu-id="bdf6d-168">Můžete taky psát vlastní hodnotu poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-168">You can also write custom value providers.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="bdf6d-169">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="bdf6d-169">Run the application</span></span>

<span data-ttu-id="bdf6d-170">Spusťte aplikaci nyní chcete-li zobrazit všechny produkty nebo kategorie produktů.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-170">Run the application now to view all products or a category's products.</span></span>

1. <span data-ttu-id="bdf6d-171">V sadě Visual Studio, stiskněte klávesu **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-171">In Visual Studio, press **F5** to run the application.</span></span>
 <span data-ttu-id="bdf6d-172">V prohlížeči se otevře a zobrazí *Default.aspx* stránky.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-172">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="bdf6d-173">V nabídce kategorie produktu, vyberte **auta**.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-173">From the product category menu, select **Cars**.</span></span>

   <span data-ttu-id="bdf6d-174">*ProductList.aspx* se zobrazí stránka zobrazující pouze produkty **auta** kategorie.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-174">The *ProductList.aspx* page appears, showing only products from the **Cars** category.</span></span> <span data-ttu-id="bdf6d-175">Dále v tomto kurzu se zobrazí podrobnosti o produktu.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-175">Later in this tutorial, you display product details.</span></span>

    ![Zobrazení dat položek a podrobnosti - automobilů](display_data_items_and_details/_static/image2.png)

3. <span data-ttu-id="bdf6d-177">Vyberte **produkty** z hlavní nabídky.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-177">Select **Products** from the top menu.</span></span>
 <span data-ttu-id="bdf6d-178">*ProductList.aspx* stránka nyní zobrazuje všechny produkty.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-178">The *ProductList.aspx* page now displays all products.</span></span> 

    ![Zobrazení dat položek a podrobnosti - produkty](display_data_items_and_details/_static/image3.png)

4. <span data-ttu-id="bdf6d-180">Zavřete prohlížeč a vraťte se do sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-180">Close the browser and return to Visual Studio.</span></span>

### <a name="add-a-data-control-to-display-product-details"></a><span data-ttu-id="bdf6d-181">Přidejte ovládací prvek dat zobrazíte podrobnosti o produktu</span><span class="sxs-lookup"><span data-stu-id="bdf6d-181">Add a Data Control to display product details</span></span>

<span data-ttu-id="bdf6d-182">Upravit *ProductDetails.aspx* značky, které jste přidali v předchozím kurzu zobrazíte informace o konkrétním produktu:</span><span class="sxs-lookup"><span data-stu-id="bdf6d-182">Modify the *ProductDetails.aspx* markup that you added in the previous tutorial to display specific product information:</span></span>

1. <span data-ttu-id="bdf6d-183">V **Průzkumníka řešení**, otevřete *ProductDetails.aspx*.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-183">In **Solution Explorer**, open *ProductDetails.aspx*.</span></span>

2. <span data-ttu-id="bdf6d-184">Nahraďte stávající kód tento kód:</span><span class="sxs-lookup"><span data-stu-id="bdf6d-184">Replace the existing markup with this markup:</span></span>

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

<span data-ttu-id="bdf6d-185">Tento kód používá **FormView** ovládací prvek pro zobrazení Podrobnosti o konkrétního produktu.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-185">This markup uses a **FormView** control to display specific product details.</span></span> <span data-ttu-id="bdf6d-186">Používá metody, stejně jako se používají k zobrazení dat v *ProductList.aspx*.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-186">It uses methods like those used to display data in *ProductList.aspx*.</span></span> <span data-ttu-id="bdf6d-187">**FormView** ovládacího prvku se používá k zobrazení jeden záznam v čase ze zdroje dat.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-187">The **FormView** control is used to display a single record at a time from a data source.</span></span> <span data-ttu-id="bdf6d-188">Při použití **FormView** ovládacího prvku, je vytvořit šablony můžete zobrazit a upravit hodnoty vázané na data.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-188">When you use the **FormView** control, you create templates to display and edit data-bound values.</span></span> <span data-ttu-id="bdf6d-189">Tyto šablony obsahují ovládací prvky, výrazy, vazby a formátování, které definují vzhled formuláře a funkce.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-189">These templates contain controls, binding expressions, and formatting that define the form's look and functionality.</span></span>

<span data-ttu-id="bdf6d-190">Předchozí kód s připojením k databázi se vyžaduje další kód.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-190">Connecting the previous markup to the database requires additional code.</span></span>

1. <span data-ttu-id="bdf6d-191">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *ProductDetails.aspx* a pak vyberte **zobrazit kód**.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-191">In **Solution Explorer**, right-click *ProductDetails.aspx* and then select **View Code**.</span></span>  
   <span data-ttu-id="bdf6d-192">*ProductDetails.aspx.cs* soubor se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-192">The *ProductDetails.aspx.cs* file is displayed.</span></span>

2. <span data-ttu-id="bdf6d-193">Nahraďte stávající kód s tímto:</span><span class="sxs-lookup"><span data-stu-id="bdf6d-193">Replace the existing code with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

<span data-ttu-id="bdf6d-194">Tento kód kontroluje "`productID`" hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-194">This code checks for a "`productID`" query string value.</span></span> <span data-ttu-id="bdf6d-195">Pokud platnou hodnotu nenajde, zobrazí se odpovídající produkt.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-195">If a valid value is found, the matching product is displayed.</span></span> <span data-ttu-id="bdf6d-196">Pokud řetězec dotazu nebyl nalezen nebo její hodnota není platná, zobrazí se žádné produkty.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-196">If the query string isn't found, or its value isn't valid, no product is displayed.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="bdf6d-197">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="bdf6d-197">Run the application</span></span>

<span data-ttu-id="bdf6d-198">Nyní můžete spustit aplikaci chcete zobrazit podrobnosti konkrétního produktu na základě ID produktu.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-198">Now you can run the application to see specific product details based on product ID.</span></span>

1. <span data-ttu-id="bdf6d-199">V sadě Visual Studio, stiskněte klávesu **F5** ke spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-199">In Visual Studio, press **F5** to run the application.</span></span>  
 <span data-ttu-id="bdf6d-200">V prohlížeči se otevře na *Default.aspx*.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-200">The browser opens to *Default.aspx*.</span></span>

2. <span data-ttu-id="bdf6d-201">V nabídce Kategorie vyberte **lodě**.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-201">From the category menu, select **Boats**.</span></span>  
 <span data-ttu-id="bdf6d-202">*ProductList.aspx* zobrazí se stránka.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-202">The *ProductList.aspx* page is displayed.</span></span>

3. <span data-ttu-id="bdf6d-203">Vyberte **papír loď**.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-203">Select **Paper Boat**.</span></span>  
 <span data-ttu-id="bdf6d-204">*ProductDetails.aspx* zobrazí se stránka.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-204">The *ProductDetails.aspx* page is displayed.</span></span>   

    ![Zobrazení dat položek a podrobnosti - produkty](display_data_items_and_details/_static/image4.png)

<span data-ttu-id="bdf6d-206">V dalším kurzu přidáte do aplikace Wingtip Toys nákupního košíku.</span><span class="sxs-lookup"><span data-stu-id="bdf6d-206">In the next tutorial, you add a shopping cart to the Wingtip Toys application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bdf6d-207">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bdf6d-207">Additional resources</span></span>

[<span data-ttu-id="bdf6d-208">Načtení a zobrazení dat ovládacím prvkem vazby modelu a webové formuláře</span><span class="sxs-lookup"><span data-stu-id="bdf6d-208">Retrieving and displaying data with model binding and web forms</span></span>](../../presenting-and-managing-data/model-binding/retrieving-data.md)

> [!div class="step-by-step"]
> <span data-ttu-id="bdf6d-209">[Předchozí](ui_and_navigation.md)
> [další](shopping-cart.md)</span><span class="sxs-lookup"><span data-stu-id="bdf6d-209">[Previous](ui_and_navigation.md)
[Next](shopping-cart.md)</span></span>
