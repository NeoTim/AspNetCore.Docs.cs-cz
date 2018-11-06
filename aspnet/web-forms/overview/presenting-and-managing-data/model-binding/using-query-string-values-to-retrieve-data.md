---
uid: web-forms/overview/presenting-and-managing-data/model-binding/using-query-string-values-to-retrieve-data
title: Použití hodnot řetězce dotazu k filtrování dat pomocí vazby modelu a webových formulářů | Dokumentace Microsoftu
author: Rick-Anderson
description: V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Data interakce díky vazby modelu další přímo-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: b90978bd-795d-4871-9ade-1671caff5730
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/using-query-string-values-to-retrieve-data
msc.type: authoredcontent
ms.openlocfilehash: 490279ec8457535031387e955e67550052764fff
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021115"
---
<a name="using-query-string-values-to-filter-data-with-model-binding-and-web-forms"></a><span data-ttu-id="5f6b2-104">Použití hodnot řetězce dotazu pro filtrování dat pomocí vazby modelu a webové formuláře</span><span class="sxs-lookup"><span data-stu-id="5f6b2-104">Using query string values to filter data with model binding and web forms</span></span>
====================
<span data-ttu-id="5f6b2-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="5f6b2-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="5f6b2-106">V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-106">This tutorial series demonstrates basic aspects of using model binding with an ASP.NET Web Forms project.</span></span> <span data-ttu-id="5f6b2-107">Vazby modelu díky dat interakce více přímočaré než pracující s daty objektů zdroje (například ObjectDataSource nebo SqlDataSource).</span><span class="sxs-lookup"><span data-stu-id="5f6b2-107">Model binding makes data interaction more straight-forward than dealing with data source objects (such as ObjectDataSource or SqlDataSource).</span></span> <span data-ttu-id="5f6b2-108">Tato série začíná úvodní materiály a přesune pokročilejších pojmech v budoucích kurzech.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-108">This series starts with introductory material and moves to more advanced concepts in later tutorials.</span></span>
> 
> <span data-ttu-id="5f6b2-109">Tento kurz ukazuje, jak předat hodnotu v řetězci dotazu a tuto hodnotu použít k načtení dat pomocí vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-109">This tutorial shows how to pass a value in the query string and use that value to retrieve data through model binding.</span></span>
> 
> <span data-ttu-id="5f6b2-110">V tomto kurzu vychází z vytvořeného v projektu [starší](retrieving-data.md) části této série.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-110">This tutorial builds on the project created in the [earlier](retrieving-data.md) parts of the series.</span></span>
> 
> <span data-ttu-id="5f6b2-111">Je možné [Stáhnout](https://go.microsoft.com/fwlink/?LinkId=286116) dokončený projekt v jazyce C# nebo VB.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-111">You can [download](https://go.microsoft.com/fwlink/?LinkId=286116) the complete project in C# or VB.</span></span> <span data-ttu-id="5f6b2-112">Ke stažení kódu funguje pomocí sady Visual Studio 2012 nebo Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-112">The downloadable code works with either Visual Studio 2012 or Visual Studio 2013.</span></span> <span data-ttu-id="5f6b2-113">Používá šablonu Visual Studio 2012, která se trochu liší od sady Visual Studio 2013 šablonu uvedenou v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-113">It uses the Visual Studio 2012 template, which is slightly different than the Visual Studio 2013 template shown in this tutorial.</span></span>


## <a name="what-youll-build"></a><span data-ttu-id="5f6b2-114">Co budete vytvářet</span><span class="sxs-lookup"><span data-stu-id="5f6b2-114">What you'll build</span></span>

<span data-ttu-id="5f6b2-115">V tomto kurzu budete:</span><span class="sxs-lookup"><span data-stu-id="5f6b2-115">In this tutorial, you'll:</span></span>

1. <span data-ttu-id="5f6b2-116">Přidejte novou stránku zobrazíte zaregistrovaná kurzy pro student</span><span class="sxs-lookup"><span data-stu-id="5f6b2-116">Add a new page to show the enrolled courses for a student</span></span>
2. <span data-ttu-id="5f6b2-117">Načíst zaregistrovaná kurzů pro vybranou studenty na základě hodnoty v řetězci dotazu</span><span class="sxs-lookup"><span data-stu-id="5f6b2-117">Retrieve the enrolled courses for the selected student based on a value in the query string</span></span>
3. <span data-ttu-id="5f6b2-118">Přidání hypertextového odkazu s hodnotou řetězce dotazu v zobrazení mřížky na novou stránku</span><span class="sxs-lookup"><span data-stu-id="5f6b2-118">Add a hyperlink with a query string value from the grid view to the new page</span></span>

<span data-ttu-id="5f6b2-119">Kroky v tomto kurzu jsou velmi podobné co jste se naučili v předchozím [kurzu](sorting-paging-and-filtering-data.md) k filtrování zobrazených studenty na základě výběru uživatelem v rozevíracím seznamu.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-119">The steps in this tutorial are fairly similar to what you did in the earlier [tutorial](sorting-paging-and-filtering-data.md) to filter the displayed students based on the user selection in a drop down list.</span></span> <span data-ttu-id="5f6b2-120">V tomto kurzu jste použili **ovládací prvek** atributu v metodě select k určení, že hodnota parametru pochází z ovládacího prvku.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-120">In that tutorial, you used the **Control** attribute in the select method to specify that the parameter value comes from a control.</span></span> <span data-ttu-id="5f6b2-121">V tomto kurzu budete používat **QueryString** atributu v metodě select k určení, že hodnota parametru pochází z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-121">In this tutorial, you'll use the **QueryString** attribute in the select method to specify that the parameter value comes from the query string.</span></span>

## <a name="add-new-page-for-displaying-a-students-courses"></a><span data-ttu-id="5f6b2-122">Přidejte novou stránku pro zobrazení student získal kurzy</span><span class="sxs-lookup"><span data-stu-id="5f6b2-122">Add new page for displaying a student's courses</span></span>

<span data-ttu-id="5f6b2-123">Přidat nový webový formulář používající stránku předlohy Site.master a pojmenujte stránku **kurzy**.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-123">Add a new web form that uses the Site.master master page, and name the page **Courses**.</span></span>

<span data-ttu-id="5f6b2-124">V **Courses.aspx** přidejte zobrazení mřížky kurzy pro vybrané studentů.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-124">In the **Courses.aspx** file, add a grid view to display the courses for the selected student.</span></span>

[!code-aspx[Main](using-query-string-values-to-retrieve-data/samples/sample1.aspx)]

## <a name="define-the-select-method"></a><span data-ttu-id="5f6b2-125">Definování metody select</span><span class="sxs-lookup"><span data-stu-id="5f6b2-125">Define the select method</span></span>

<span data-ttu-id="5f6b2-126">V **Courses.aspx.cs**, přidejte metody select s názvem zadaným v mřížkovém zobrazení **metoda SelectMethod** vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-126">In **Courses.aspx.cs**, you will add the select method with the name you specified in the grid view's **SelectMethod** property.</span></span> <span data-ttu-id="5f6b2-127">V této metodě budete definovat dotaz pro načtení student získal kurzy a určit, že parametr pochází z hodnotu řetězce dotazu se stejným názvem jako parametr.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-127">In that method, you'll define the query for retrieving a student's courses, and specify that the parameter comes from a query string value with the same name as the parameter.</span></span>

<span data-ttu-id="5f6b2-128">Nejprve je nutné přidat následující **pomocí** příkazy.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-128">First, you must add the following **using** statements.</span></span>

[!code-csharp[Main](using-query-string-values-to-retrieve-data/samples/sample2.cs)]

<span data-ttu-id="5f6b2-129">K Courses.aspx.cs, přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="5f6b2-129">Then, add the following code to Courses.aspx.cs:</span></span>

[!code-csharp[Main](using-query-string-values-to-retrieve-data/samples/sample3.cs)]

<span data-ttu-id="5f6b2-130">Atribut QueryString znamená, že hodnotu řetězce dotazu s názvem StudentID automaticky přiřazen k parametru v této metodě.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-130">The QueryString attribute means that a query string value named StudentID is automatically assigned to the parameter in this method.</span></span>

## <a name="add-hyperlink-with-query-string-value"></a><span data-ttu-id="5f6b2-131">Přidání hypertextového odkazu s hodnotou řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="5f6b2-131">Add hyperlink with query string value</span></span>

<span data-ttu-id="5f6b2-132">V zobrazení mřížky na Students.aspx přidáte pole hypertextového odkazu, který odkazuje na vaše nová stránka kurzů.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-132">In the grid view on Students.aspx, you will add a hyperlink field that links to your new Courses page.</span></span> <span data-ttu-id="5f6b2-133">Hypertextový odkaz bude obsahovat hodnotu řetězce dotazu s id studenta.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-133">The hyperlink will include a query string value with the student's id.</span></span>

<span data-ttu-id="5f6b2-134">V Students.aspx přidejte následující pole do sloupce zobrazení mřížky přímo pod pole pro celkový počet kredity.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-134">In Students.aspx, add the following field to the grid view columns just below the field for Total Credits.</span></span>

[!code-aspx[Main](using-query-string-values-to-retrieve-data/samples/sample4.aspx?highlight=7-8)]

<span data-ttu-id="5f6b2-135">Spusťte aplikaci a Všimněte si, že zobrazení mřížky teď obsahuje odkaz na kurzy.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-135">Run the application and notice that the grid view now includes the Courses link.</span></span>

![Přidání hypertextového odkazu](using-query-string-values-to-retrieve-data/_static/image1.png)

<span data-ttu-id="5f6b2-137">Když kliknete na některý z odkazů, zobrazí se vám tento studentů zaregistrovaná kurzy.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-137">When you click one of the links, you'll see that student's enrolled courses.</span></span>

![Zobrazit kurzy](using-query-string-values-to-retrieve-data/_static/image2.png)

## <a name="conclusion"></a><span data-ttu-id="5f6b2-139">Závěr</span><span class="sxs-lookup"><span data-stu-id="5f6b2-139">Conclusion</span></span>

<span data-ttu-id="5f6b2-140">V tomto kurzu jste přidali odkaz s hodnotou řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-140">In this tutorial, you added a link with a query string value.</span></span> <span data-ttu-id="5f6b2-141">Použít tuto hodnotu řetězce dotazu pro parametr metody select.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-141">You used that query string value for the parameter value in the select method.</span></span>

<span data-ttu-id="5f6b2-142">V dalším [kurzu](adding-business-logic-layer.md), kód se přesune z použití modelu code-behind soubory do vrstvy obchodní logiky a vrstva přístupu k datům.</span><span class="sxs-lookup"><span data-stu-id="5f6b2-142">In the next [tutorial](adding-business-logic-layer.md), you will move the code from the code-behind files into a business logic layer and a data access layer.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5f6b2-143">[Předchozí](integrating-jquery-ui.md)
> [další](adding-business-logic-layer.md)</span><span class="sxs-lookup"><span data-stu-id="5f6b2-143">[Previous](integrating-jquery-ui.md)
[Next](adding-business-logic-layer.md)</span></span>
