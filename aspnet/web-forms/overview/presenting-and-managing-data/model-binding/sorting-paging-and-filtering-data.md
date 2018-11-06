---
uid: web-forms/overview/presenting-and-managing-data/model-binding/sorting-paging-and-filtering-data
title: Řazení, stránkování a filtrování dat pomocí vazby modelu a webových formulářů | Dokumentace Microsoftu
author: Rick-Anderson
description: V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Data interakce díky vazby modelu další přímo-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 266e7866-e327-4687-b29d-627a0925e87d
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/sorting-paging-and-filtering-data
msc.type: authoredcontent
ms.openlocfilehash: 624f98cea6030e0b7b022f86c4c1aa37f1db9726
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020947"
---
<a name="sorting-paging-and-filtering-data-with-model-binding-and-web-forms"></a><span data-ttu-id="54db9-104">Řazení, stránkování a filtrování dat pomocí vazby modelu a webové formuláře</span><span class="sxs-lookup"><span data-stu-id="54db9-104">Sorting, paging, and filtering data with model binding and web forms</span></span>
====================
<span data-ttu-id="54db9-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="54db9-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="54db9-106">V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="54db9-106">This tutorial series demonstrates basic aspects of using model binding with an ASP.NET Web Forms project.</span></span> <span data-ttu-id="54db9-107">Vazby modelu díky dat interakce více přímočaré než pracující s daty objektů zdroje (například ObjectDataSource nebo SqlDataSource).</span><span class="sxs-lookup"><span data-stu-id="54db9-107">Model binding makes data interaction more straight-forward than dealing with data source objects (such as ObjectDataSource or SqlDataSource).</span></span> <span data-ttu-id="54db9-108">Tato série začíná úvodní materiály a přesune pokročilejších pojmech v budoucích kurzech.</span><span class="sxs-lookup"><span data-stu-id="54db9-108">This series starts with introductory material and moves to more advanced concepts in later tutorials.</span></span>
> 
> <span data-ttu-id="54db9-109">Tento kurz ukazuje, jak přidat řazení, stránkování a filtrování dat pomocí vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="54db9-109">This tutorial shows how to add sorting, paging, and filtering of the data through model binding.</span></span>
> 
> <span data-ttu-id="54db9-110">V tomto kurzu vychází z projektu vytvořeného v prvním [část](retrieving-data.md) řady.</span><span class="sxs-lookup"><span data-stu-id="54db9-110">This tutorial builds on the project created in the first [part](retrieving-data.md) of the series.</span></span>
> 
> <span data-ttu-id="54db9-111">Je možné [Stáhnout](https://go.microsoft.com/fwlink/?LinkId=286116) dokončený projekt v jazyce C# nebo VB.</span><span class="sxs-lookup"><span data-stu-id="54db9-111">You can [download](https://go.microsoft.com/fwlink/?LinkId=286116) the complete project in C# or VB.</span></span> <span data-ttu-id="54db9-112">Ke stažení kódu funguje pomocí sady Visual Studio 2012 nebo Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="54db9-112">The downloadable code works with either Visual Studio 2012 or Visual Studio 2013.</span></span> <span data-ttu-id="54db9-113">Používá šablonu Visual Studio 2012, která se trochu liší od sady Visual Studio 2013 šablonu uvedenou v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="54db9-113">It uses the Visual Studio 2012 template, which is slightly different than the Visual Studio 2013 template shown in this tutorial.</span></span>


## <a name="what-youll-build"></a><span data-ttu-id="54db9-114">Co budete vytvářet</span><span class="sxs-lookup"><span data-stu-id="54db9-114">What you'll build</span></span>

<span data-ttu-id="54db9-115">V tomto kurzu budete:</span><span class="sxs-lookup"><span data-stu-id="54db9-115">In this tutorial, you'll:</span></span>

1. <span data-ttu-id="54db9-116">Povolit řazení a stránkování dat</span><span class="sxs-lookup"><span data-stu-id="54db9-116">Enable sorting and paging of the data</span></span>
2. <span data-ttu-id="54db9-117">Povolit filtrování dat na základě výběru uživatelem.</span><span class="sxs-lookup"><span data-stu-id="54db9-117">Enable filtering of the data based on a selection by the user</span></span>

## <a name="add-sorting"></a><span data-ttu-id="54db9-118">Přidání řazení</span><span class="sxs-lookup"><span data-stu-id="54db9-118">Add sorting</span></span>

<span data-ttu-id="54db9-119">Je velmi snadno povolit řazení v prvku GridView.</span><span class="sxs-lookup"><span data-stu-id="54db9-119">Enabling sorting in the GridView is very easy.</span></span> <span data-ttu-id="54db9-120">V souboru Student.aspx, stačí nastavit **AllowSorting** k **true** v prvku GridView.</span><span class="sxs-lookup"><span data-stu-id="54db9-120">In the Student.aspx file, simply set **AllowSorting** to **true** in the GridView.</span></span> <span data-ttu-id="54db9-121">Není nutné nastavovat **SortExpression** hodnotu pro každý sloupec jako hodnota se automaticky použije.</span><span class="sxs-lookup"><span data-stu-id="54db9-121">You do not need to set a **SortExpression** value for each column as the DataField is automatically used.</span></span> <span data-ttu-id="54db9-122">GridView upraví dotaz pro přidání řazení dat podle vybrané hodnoty.</span><span class="sxs-lookup"><span data-stu-id="54db9-122">The GridView modifies the query to include ordering the data by the selected value.</span></span> <span data-ttu-id="54db9-123">Zvýrazněný kód uvedený níže ukazuje přidání, že budete muset udělat umožňující řazení.</span><span class="sxs-lookup"><span data-stu-id="54db9-123">The highlighted code below shows the addition you need to make to enable sorting.</span></span>

[!code-aspx[Main](sorting-paging-and-filtering-data/samples/sample1.aspx?highlight=5)]

<span data-ttu-id="54db9-124">Spuštění webové aplikace a testování řazení záznamech studentů podle hodnot v různých sloupcích.</span><span class="sxs-lookup"><span data-stu-id="54db9-124">Run the web application, and test sorting student records by the values in different columns.</span></span>

![studenti řazení](sorting-paging-and-filtering-data/_static/image2.png)

## <a name="add-paging"></a><span data-ttu-id="54db9-126">Přidání stránkování</span><span class="sxs-lookup"><span data-stu-id="54db9-126">Add paging</span></span>

<span data-ttu-id="54db9-127">Povolení stránkování je také velmi snadné.</span><span class="sxs-lookup"><span data-stu-id="54db9-127">Enabling paging is also very easy.</span></span> <span data-ttu-id="54db9-128">V prvku GridView, nastavte **vlastnost AllowPaging** vlastnost **true** a nastavit **PageSize** na počet záznamů, které chcete zobrazit na jednotlivých stránkách.</span><span class="sxs-lookup"><span data-stu-id="54db9-128">In the GridView, set the **AllowPaging** property to **true** and set the **PageSize** property to the number of records you wish to display on each page.</span></span> <span data-ttu-id="54db9-129">V tomto kurzu můžete ho nastavit na 4.</span><span class="sxs-lookup"><span data-stu-id="54db9-129">In this tutorial, you can set it to 4.</span></span>

[!code-aspx[Main](sorting-paging-and-filtering-data/samples/sample2.aspx?highlight=5)]

<span data-ttu-id="54db9-130">Spusťte webovou aplikaci a Všimněte si, že teď záznamy jsou rozdělit přes více stránek s více než 4 záznamy zobrazené na jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="54db9-130">Run the web application, and notice that now the records are divided over multiple pages with no more than 4 records displayed on a single page.</span></span>

![Přidání stránkování](sorting-paging-and-filtering-data/_static/image4.png)

<span data-ttu-id="54db9-132">Odložený dotaz zvyšuje efektivitu aplikace.</span><span class="sxs-lookup"><span data-stu-id="54db9-132">Deferred query execution improves the application efficiency.</span></span> <span data-ttu-id="54db9-133">Místo načtení celé datové sady, upraví prvku GridView. dotaz pro načtení záznamů pouze pro aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="54db9-133">Instead of retrieving the entire data set, the GridView modifies the query to retrieve only the records for the current page.</span></span>

## <a name="filter-records-by-user-selection"></a><span data-ttu-id="54db9-134">Záznamy filtrovaly podle výběru uživatele</span><span class="sxs-lookup"><span data-stu-id="54db9-134">Filter records by user selection</span></span>

<span data-ttu-id="54db9-135">Vazby modelu přidá několik atributů, které vám umožní určit, jak nastavit hodnotu pro parametr metody vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="54db9-135">Model binding adds several attributes which enable you to designate how to set the value for a parameter in a model binding method.</span></span> <span data-ttu-id="54db9-136">Tyto atributy jsou v **System.Web.ModelBinding** oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="54db9-136">These attributes are in the **System.Web.ModelBinding** namespace.</span></span> <span data-ttu-id="54db9-137">Mezi ně patří:</span><span class="sxs-lookup"><span data-stu-id="54db9-137">They include:</span></span>

- <span data-ttu-id="54db9-138">Ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="54db9-138">Control</span></span>
- <span data-ttu-id="54db9-139">Soubor cookie</span><span class="sxs-lookup"><span data-stu-id="54db9-139">Cookie</span></span>
- <span data-ttu-id="54db9-140">Formulář</span><span class="sxs-lookup"><span data-stu-id="54db9-140">Form</span></span>
- <span data-ttu-id="54db9-141">Profil</span><span class="sxs-lookup"><span data-stu-id="54db9-141">Profile</span></span>
- <span data-ttu-id="54db9-142">Řetězec dotazu</span><span class="sxs-lookup"><span data-stu-id="54db9-142">QueryString</span></span>
- <span data-ttu-id="54db9-143">Parametr RouteData</span><span class="sxs-lookup"><span data-stu-id="54db9-143">RouteData</span></span>
- <span data-ttu-id="54db9-144">Relace</span><span class="sxs-lookup"><span data-stu-id="54db9-144">Session</span></span>
- <span data-ttu-id="54db9-145">UserProfile</span><span class="sxs-lookup"><span data-stu-id="54db9-145">UserProfile</span></span>
- <span data-ttu-id="54db9-146">Stav zobrazení</span><span class="sxs-lookup"><span data-stu-id="54db9-146">ViewState</span></span>

<span data-ttu-id="54db9-147">V tomto kurzu použijete hodnotu ovládacího prvku k filtrování záznamů, které se zobrazují v prvku GridView.</span><span class="sxs-lookup"><span data-stu-id="54db9-147">In this tutorial, you will use a control's value to filter which records are displayed in the GridView.</span></span> <span data-ttu-id="54db9-148">Přidáte **ovládací prvek** atributu na metodu dotazu, který jste vytvořili dříve.</span><span class="sxs-lookup"><span data-stu-id="54db9-148">You will add the **Control** attribute to the query method you had created earlier.</span></span> <span data-ttu-id="54db9-149">V [později](using-query-string-values-to-retrieve-data.md) kurz, se použijí **QueryString** atribut pro parametr určující, zda je hodnota parametru pochází od hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="54db9-149">In a [later](using-query-string-values-to-retrieve-data.md) tutorial, you will apply the **QueryString** attribute to a parameter to specify that the parameter value comes from a query string value.</span></span>

<span data-ttu-id="54db9-150">Nejprve přidejte výše ValidationSummary, rozevírací seznam pro filtrování, které studenty jsou uvedeny.</span><span class="sxs-lookup"><span data-stu-id="54db9-150">First, above the ValidationSummary, add a drop down list for filtering which students are shown.</span></span>

[!code-aspx[Main](sorting-paging-and-filtering-data/samples/sample3.aspx?highlight=3-11)]

<span data-ttu-id="54db9-151">V souboru kódu na pozadí upravte metodu vyberte získat hodnotu z ovládacího prvku a nastavte název parametru název ovládacího prvku, který obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="54db9-151">In the code-behind file, modify the select method to receive a value from the control, and set the name of the parameter to the name of the control that provides the value.</span></span>

<span data-ttu-id="54db9-152">Je nutné přidat **pomocí** příkaz pro **System.Web.ModelBinding** obor názvů, chcete-li vyřešit atribut ovládacího prvku.</span><span class="sxs-lookup"><span data-stu-id="54db9-152">You must add a **using** statement for the **System.Web.ModelBinding** namespace to resolve the Control attribute.</span></span>

[!code-csharp[Main](sorting-paging-and-filtering-data/samples/sample4.cs)]

<span data-ttu-id="54db9-153">Následující kód ukazuje metody select znovu pracoval pro filtrování vrácená data na základě hodnoty z rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="54db9-153">The following code shows the select method re-worked to filter the returned data based on the value of the drop down list.</span></span> <span data-ttu-id="54db9-154">Přidání atributu ovládacího prvku před parametr určuje, že hodnota pro tento parametr pochází z ovládacího prvku se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="54db9-154">Adding a control attribute before a parameter specifies that the value for this parameter comes from a control with the same name.</span></span>

[!code-csharp[Main](sorting-paging-and-filtering-data/samples/sample5.cs)]

<span data-ttu-id="54db9-155">Spusťte webovou aplikaci a vyberte jiné hodnoty z rozevíracího seznamu pro filtrování seznamu studentů.</span><span class="sxs-lookup"><span data-stu-id="54db9-155">Run the web application and select different values from the drop down list to filter the list of students.</span></span>

![Filtr pro studenty](sorting-paging-and-filtering-data/_static/image6.png)

## <a name="conclusion"></a><span data-ttu-id="54db9-157">Závěr</span><span class="sxs-lookup"><span data-stu-id="54db9-157">Conclusion</span></span>

<span data-ttu-id="54db9-158">V tomto kurzu jste povolili, řazení a stránkování dat.</span><span class="sxs-lookup"><span data-stu-id="54db9-158">In this tutorial, you enabled sorting and paging of the data.</span></span> <span data-ttu-id="54db9-159">Také jste povolili, filtrování dat podle hodnoty ovládacího prvku.</span><span class="sxs-lookup"><span data-stu-id="54db9-159">You also enabled filtering the data by the value of a control.</span></span>

<span data-ttu-id="54db9-160">V dalším [kurzu](integrating-jquery-ui.md) uživatelské rozhraní bude vylepšit integrací uživatelské rozhraní JQuery widgetu do šablony dynamická data.</span><span class="sxs-lookup"><span data-stu-id="54db9-160">In the next [tutorial](integrating-jquery-ui.md) you will enhance the UI by integrating a JQuery UI widget into the dynamic data template.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="54db9-161">[Předchozí](updating-deleting-and-creating-data.md)
> [další](integrating-jquery-ui.md)</span><span class="sxs-lookup"><span data-stu-id="54db9-161">[Previous](updating-deleting-and-creating-data.md)
[Next](integrating-jquery-ui.md)</span></span>
