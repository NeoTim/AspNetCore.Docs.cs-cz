---
uid: web-forms/overview/presenting-and-managing-data/model-binding/adding-business-logic-layer
title: Přidání vrstvy obchodní logiky do projektu, který používá vazbu modelu a webových formulářů | Dokumentace Microsoftu
author: Rick-Anderson
description: V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Data interakce díky vazby modelu další přímo-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 7ef664b3-1cc8-4cbf-bb18-9f0f3a3ada2b
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/adding-business-logic-layer
msc.type: authoredcontent
ms.openlocfilehash: 4ba1830ce51f257e18880f752b249dbeb77f504e
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021648"
---
<a name="adding-business-logic-layer-to-a-project-that-uses-model-binding-and-web-forms"></a><span data-ttu-id="1260b-104">Přidání vrstvy obchodní logiky do projektu, který používá vazbu modelu a webové formuláře</span><span class="sxs-lookup"><span data-stu-id="1260b-104">Adding business logic layer to a project that uses model binding and web forms</span></span>
====================
<span data-ttu-id="1260b-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="1260b-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="1260b-106">V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1260b-106">This tutorial series demonstrates basic aspects of using model binding with an ASP.NET Web Forms project.</span></span> <span data-ttu-id="1260b-107">Vazby modelu díky dat interakce více přímočaré než pracující s daty objektů zdroje (například ObjectDataSource nebo SqlDataSource).</span><span class="sxs-lookup"><span data-stu-id="1260b-107">Model binding makes data interaction more straight-forward than dealing with data source objects (such as ObjectDataSource or SqlDataSource).</span></span> <span data-ttu-id="1260b-108">Tato série začíná úvodní materiály a přesune pokročilejších pojmech v budoucích kurzech.</span><span class="sxs-lookup"><span data-stu-id="1260b-108">This series starts with introductory material and moves to more advanced concepts in later tutorials.</span></span>
> 
> <span data-ttu-id="1260b-109">Tento kurz ukazuje, jak pomocí vazby modelu vrstvy obchodní logiky.</span><span class="sxs-lookup"><span data-stu-id="1260b-109">This tutorial shows how to use model binding with a business logic layer.</span></span> <span data-ttu-id="1260b-110">Nastavíte člen OnCallingDataMethods k určení, že objekt než aktuální stránku slouží k volání metody data.</span><span class="sxs-lookup"><span data-stu-id="1260b-110">You will set the OnCallingDataMethods member to specify that an object other than the current page is used to call the data methods.</span></span>
> 
> <span data-ttu-id="1260b-111">V tomto kurzu vychází z vytvořeného v projektu [starší](retrieving-data.md) části této série.</span><span class="sxs-lookup"><span data-stu-id="1260b-111">This tutorial builds on the project created in the [earlier](retrieving-data.md) parts of the series.</span></span>
> 
> <span data-ttu-id="1260b-112">Je možné [Stáhnout](https://go.microsoft.com/fwlink/?LinkId=286116) dokončený projekt v jazyce C# nebo VB.</span><span class="sxs-lookup"><span data-stu-id="1260b-112">You can [download](https://go.microsoft.com/fwlink/?LinkId=286116) the complete project in C# or VB.</span></span> <span data-ttu-id="1260b-113">Ke stažení kódu funguje pomocí sady Visual Studio 2012 nebo Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="1260b-113">The downloadable code works with either Visual Studio 2012 or Visual Studio 2013.</span></span> <span data-ttu-id="1260b-114">Používá šablonu Visual Studio 2012, která se trochu liší od sady Visual Studio 2013 šablonu uvedenou v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="1260b-114">It uses the Visual Studio 2012 template, which is slightly different than the Visual Studio 2013 template shown in this tutorial.</span></span>


## <a name="what-youll-build"></a><span data-ttu-id="1260b-115">Co budete vytvářet</span><span class="sxs-lookup"><span data-stu-id="1260b-115">What you'll build</span></span>

<span data-ttu-id="1260b-116">Vazby modelu umožňuje ukládat kód interakce data v souboru kódu pro webovou stránku nebo ve třídě samostatné obchodní logiku.</span><span class="sxs-lookup"><span data-stu-id="1260b-116">Model binding enables you to put your data interaction code in either the code-behind file for a web page or in a separate business logic class.</span></span> <span data-ttu-id="1260b-117">Z předchozích kurzů ukázaly, jak používat soubory kódu na pozadí pro kód interakce data.</span><span class="sxs-lookup"><span data-stu-id="1260b-117">The previous tutorials have shown how to use the code-behind files for data interaction code.</span></span> <span data-ttu-id="1260b-118">Tento postup funguje u malých sítích, ale to může vést k kód opakování a větší potíže při udržování velké lokality.</span><span class="sxs-lookup"><span data-stu-id="1260b-118">This approach works for small sites but it can lead to code repetition and greater difficulty when maintaining a large site.</span></span> <span data-ttu-id="1260b-119">To může být velmi obtížné programově testovat kód, který se nachází v soubory kódu na pozadí, protože neexistuje žádný abstraktní vrstvu.</span><span class="sxs-lookup"><span data-stu-id="1260b-119">It can also be very difficult to programmatically test code that resides in code behind files because there is no abstraction layer.</span></span>

<span data-ttu-id="1260b-120">A centralizovat data interakce kód, můžete vytvořit vrstvy obchodní logiky, která obsahuje veškerou logiku pro interakci s daty.</span><span class="sxs-lookup"><span data-stu-id="1260b-120">To centralize the data interaction code, you can create a business logic layer that contains all of the logic for interacting with data.</span></span> <span data-ttu-id="1260b-121">Poté je zapotřebí zavolat vrstvy obchodní logiky z vašich webových stránek.</span><span class="sxs-lookup"><span data-stu-id="1260b-121">You then call the business logic layer from your web pages.</span></span> <span data-ttu-id="1260b-122">Tento kurz ukazuje, jak přesunout veškerý kód, který jste napsali v předchozích kurzech do vrstvy obchodní logiky a pak použít tento kód ze stránek.</span><span class="sxs-lookup"><span data-stu-id="1260b-122">This tutorial shows how to move all of the code you have written in the previous tutorials into a business logic layer, and then use that code from the pages.</span></span>

<span data-ttu-id="1260b-123">V tomto kurzu budete:</span><span class="sxs-lookup"><span data-stu-id="1260b-123">In this tutorial, you'll:</span></span>

1. <span data-ttu-id="1260b-124">Přesunout kód z použití modelu code-behind soubory do vrstvy obchodní logiky</span><span class="sxs-lookup"><span data-stu-id="1260b-124">Move the code from code-behind files to a business logic layer</span></span>
2. <span data-ttu-id="1260b-125">Změňte své ovládací prvky data vázaná k volání metody v vrstvy obchodní logiky</span><span class="sxs-lookup"><span data-stu-id="1260b-125">Change your data bound controls to call the methods in the business logic layer</span></span>

## <a name="create-business-logic-layer"></a><span data-ttu-id="1260b-126">Vytvoření vrstvy obchodní logiky</span><span class="sxs-lookup"><span data-stu-id="1260b-126">Create business logic layer</span></span>

<span data-ttu-id="1260b-127">Teď vytvoříte třídu, která je volána z webových stránek.</span><span class="sxs-lookup"><span data-stu-id="1260b-127">Now, you will create the class that is called from the web pages.</span></span> <span data-ttu-id="1260b-128">Metody v této třídě vypadat podobně jako metody, které jste použili v předchozích kurzech a atributy zprostředkovatele hodnoty.</span><span class="sxs-lookup"><span data-stu-id="1260b-128">The methods in this class look similar to the methods you used in the previous tutorials and include the value provider attributes.</span></span>

<span data-ttu-id="1260b-129">Nejprve přidejte novou složku s názvem **BLL**.</span><span class="sxs-lookup"><span data-stu-id="1260b-129">First, add a new folder called **BLL**.</span></span>

![Přidat složku](adding-business-logic-layer/_static/image1.png)

<span data-ttu-id="1260b-131">Ve složce BLL vytvořte novou třídu s názvem **SchoolBL.cs**.</span><span class="sxs-lookup"><span data-stu-id="1260b-131">In the BLL folder, create a new class named **SchoolBL.cs**.</span></span> <span data-ttu-id="1260b-132">Bude obsahovat všechny operace dat, které původně nacházejí v soubory kódu na pozadí.</span><span class="sxs-lookup"><span data-stu-id="1260b-132">It will contain all of the data operations that originally resided in code-behind files.</span></span> <span data-ttu-id="1260b-133">Metody jsou téměř stejné jako metody v souboru kódu na pozadí, ale bude obsahovat některé změny.</span><span class="sxs-lookup"><span data-stu-id="1260b-133">The methods are almost the same as the methods in the code-behind file, but will include some changes.</span></span>

<span data-ttu-id="1260b-134">Je nejdůležitější změny si uvědomit, že jsou již spuštěny kód z v rámci instance **stránky** třídy.</span><span class="sxs-lookup"><span data-stu-id="1260b-134">The most important change to note is that you are no longer executing the code from within an instance of **Page** class.</span></span> <span data-ttu-id="1260b-135">Obsahuje třídy stránky **TryUpdateModel** metoda a **ModelState** vlastnost.</span><span class="sxs-lookup"><span data-stu-id="1260b-135">The Page class contains the **TryUpdateModel** method and the **ModelState** property.</span></span> <span data-ttu-id="1260b-136">Pokud tento kód je přesunuta do vrstvy obchodní logiky, už žádné instance třídy stránky pro volání těchto členů.</span><span class="sxs-lookup"><span data-stu-id="1260b-136">When this code is moved to a business logic layer, you no longer have an instance of the Page class to call these members.</span></span> <span data-ttu-id="1260b-137">Chcete-li získat chcete tento problém vyřešit, je nutné přidat **ModelMethodContext** parametr na jakoukoli metodu, která přistupuje k TryUpdateModel nebo ModelState.</span><span class="sxs-lookup"><span data-stu-id="1260b-137">To get around this issue, you must add a **ModelMethodContext** parameter to any method that accesses TryUpdateModel or ModelState.</span></span> <span data-ttu-id="1260b-138">Tento parametr ModelMethodContext použijte volání TryUpdateModel nebo načíst ModelState.</span><span class="sxs-lookup"><span data-stu-id="1260b-138">You use this ModelMethodContext parameter to call TryUpdateModel or retrieve ModelState.</span></span> <span data-ttu-id="1260b-139">Chcete-li změnit cokoli, co na webové stránce, aby se zohlednily tento nový parametr nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="1260b-139">You do not need to change anything in the web page to account for this new parameter.</span></span>

<span data-ttu-id="1260b-140">Nahraďte kód v SchoolBL.cs následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="1260b-140">Replace the code in SchoolBL.cs with the following code.</span></span>

[!code-csharp[Main](adding-business-logic-layer/samples/sample1.cs)]

## <a name="revise-existing-pages-to-retrieve-data-from-business-logic-layer"></a><span data-ttu-id="1260b-141">Upravit existující stránky k načtení dat z vrstvy obchodní logiky</span><span class="sxs-lookup"><span data-stu-id="1260b-141">Revise existing pages to retrieve data from business logic layer</span></span>

<span data-ttu-id="1260b-142">Nakonec převede na stránkách Students.aspx AddStudent.aspx a Courses.aspx pomocí dotazů v souboru kódu na pozadí pomocí vrstvy obchodní logiky.</span><span class="sxs-lookup"><span data-stu-id="1260b-142">Finally, you will convert the pages Students.aspx, AddStudent.aspx, and Courses.aspx from using queries in the code-behind file to using the business logic layer.</span></span>

<span data-ttu-id="1260b-143">Soubory kódu na pozadí pro studenty, AddStudent a kurzy odstranit nebo okomentovat následující metody dotazu:</span><span class="sxs-lookup"><span data-stu-id="1260b-143">In the code-behind files for Students, AddStudent, and Courses, delete or comment out the following query methods:</span></span>

- <span data-ttu-id="1260b-144">studentsGrid\_GetData</span><span class="sxs-lookup"><span data-stu-id="1260b-144">studentsGrid\_GetData</span></span>
- <span data-ttu-id="1260b-145">studentsGrid\_UpdateItem</span><span class="sxs-lookup"><span data-stu-id="1260b-145">studentsGrid\_UpdateItem</span></span>
- <span data-ttu-id="1260b-146">studentsGrid\_DeleteItem</span><span class="sxs-lookup"><span data-stu-id="1260b-146">studentsGrid\_DeleteItem</span></span>
- <span data-ttu-id="1260b-147">addStudentForm\_metody InsertItem</span><span class="sxs-lookup"><span data-stu-id="1260b-147">addStudentForm\_InsertItem</span></span>
- <span data-ttu-id="1260b-148">coursesGrid\_GetData</span><span class="sxs-lookup"><span data-stu-id="1260b-148">coursesGrid\_GetData</span></span>

<span data-ttu-id="1260b-149">Teď by měl mít žádný kód v souboru kódu na pozadí, který se týká operace s daty.</span><span class="sxs-lookup"><span data-stu-id="1260b-149">You now should have no code in the code-behind file that pertains to data operations.</span></span>

<span data-ttu-id="1260b-150">**OnCallingDataMethods** obslužná rutina události vám umožní zadat objekt, který použijete pro metody data.</span><span class="sxs-lookup"><span data-stu-id="1260b-150">The **OnCallingDataMethods** event handler enables you to specify an object to use for the data methods.</span></span> <span data-ttu-id="1260b-151">V Students.aspx přidejte hodnotu pro tuto obslužnou rutinu události a změnit názvy metod data na názvy metod ve třídě obchodní logiku.</span><span class="sxs-lookup"><span data-stu-id="1260b-151">In Students.aspx, add a value for that event handler and change the names of the data methods to the names of the methods in the business logic class.</span></span>

[!code-aspx[Main](adding-business-logic-layer/samples/sample2.aspx?highlight=3-4,8)]

<span data-ttu-id="1260b-152">V souboru kódu na pozadí pro Students.aspx Definujte obslužnou rutinu události pro událost CallingDataMethods.</span><span class="sxs-lookup"><span data-stu-id="1260b-152">In the code-behind file for Students.aspx, define the event handler for the CallingDataMethods event.</span></span> <span data-ttu-id="1260b-153">V této obslužné rutiny události zadejte třídu obchodní logiku pro operace s daty.</span><span class="sxs-lookup"><span data-stu-id="1260b-153">In this event handler, you specify the business logic class for data operations.</span></span>

[!code-csharp[Main](adding-business-logic-layer/samples/sample3.cs)]

<span data-ttu-id="1260b-154">V AddStudent.aspx proveďte podobné změny.</span><span class="sxs-lookup"><span data-stu-id="1260b-154">In AddStudent.aspx, make similar changes.</span></span>

[!code-aspx[Main](adding-business-logic-layer/samples/sample4.aspx?highlight=3-4)]

[!code-csharp[Main](adding-business-logic-layer/samples/sample5.cs)]

<span data-ttu-id="1260b-155">V Courses.aspx proveďte podobné změny.</span><span class="sxs-lookup"><span data-stu-id="1260b-155">In Courses.aspx, make similar changes.</span></span>

[!code-aspx[Main](adding-business-logic-layer/samples/sample6.aspx?highlight=3-4)]

[!code-csharp[Main](adding-business-logic-layer/samples/sample7.cs)]

<span data-ttu-id="1260b-156">Spusťte aplikaci a Všimněte si, že všechny stránky fungovat jako předtím měli.</span><span class="sxs-lookup"><span data-stu-id="1260b-156">Run the application and notice that all of the pages function as they had previously.</span></span> <span data-ttu-id="1260b-157">Logiku ověřování také funguje správně.</span><span class="sxs-lookup"><span data-stu-id="1260b-157">The validation logic also works correctly.</span></span>

## <a name="conclusion"></a><span data-ttu-id="1260b-158">Závěr</span><span class="sxs-lookup"><span data-stu-id="1260b-158">Conclusion</span></span>

<span data-ttu-id="1260b-159">V tomto kurzu se znovu strukturované aplikace pomocí vrstvy přístupu k datům a vrstvu obchodní logiky.</span><span class="sxs-lookup"><span data-stu-id="1260b-159">In this tutorial, you re-structured your application to use a data access layer and business logic layer.</span></span> <span data-ttu-id="1260b-160">Zadali jste, že ovládací prvky dat pomocí objektu, který není na aktuální stránce pro operace s daty.</span><span class="sxs-lookup"><span data-stu-id="1260b-160">You specified that the data controls use an object that is not the current page for data operations.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1260b-161">Předchozí</span><span class="sxs-lookup"><span data-stu-id="1260b-161">Previous</span></span>](using-query-string-values-to-retrieve-data.md)
