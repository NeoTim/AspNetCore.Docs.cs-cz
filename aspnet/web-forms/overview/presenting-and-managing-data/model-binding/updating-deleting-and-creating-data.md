---
uid: web-forms/overview/presenting-and-managing-data/model-binding/updating-deleting-and-creating-data
title: Aktualizace, odstraňování a vytváření data pomocí vazby modelu a webových formulářů | Dokumentace Microsoftu
author: Rick-Anderson
description: V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Data interakce díky vazby modelu další přímo-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 602baa94-5a4f-46eb-a717-7a9e539c1db4
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/updating-deleting-and-creating-data
msc.type: authoredcontent
ms.openlocfilehash: 127543b0696b01f136b340d07f6f806b6a6fb1eb
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021570"
---
<a name="updating-deleting-and-creating-data-with-model-binding-and-web-forms"></a><span data-ttu-id="a3291-104">Aktualizace, odstraňování a vytváření data pomocí vazby modelu a webové formuláře</span><span class="sxs-lookup"><span data-stu-id="a3291-104">Updating, deleting, and creating data with model binding and web forms</span></span>
====================
<span data-ttu-id="a3291-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="a3291-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="a3291-106">V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="a3291-106">This tutorial series demonstrates basic aspects of using model binding with an ASP.NET Web Forms project.</span></span> <span data-ttu-id="a3291-107">Vazby modelu díky dat interakce více přímočaré než pracující s daty objektů zdroje (například ObjectDataSource nebo SqlDataSource).</span><span class="sxs-lookup"><span data-stu-id="a3291-107">Model binding makes data interaction more straight-forward than dealing with data source objects (such as ObjectDataSource or SqlDataSource).</span></span> <span data-ttu-id="a3291-108">Tato série začíná úvodní materiály a přesune pokročilejších pojmech v budoucích kurzech.</span><span class="sxs-lookup"><span data-stu-id="a3291-108">This series starts with introductory material and moves to more advanced concepts in later tutorials.</span></span>
> 
> <span data-ttu-id="a3291-109">Tento kurz ukazuje, jak vytvářet, aktualizovat a odstranit data pomocí vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="a3291-109">This tutorial shows how to create, update, and delete data with model binding.</span></span> <span data-ttu-id="a3291-110">Bude nastavte následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a3291-110">You will set the following properties:</span></span>
> 
> - <span data-ttu-id="a3291-111">Metoda DeleteMethod</span><span class="sxs-lookup"><span data-stu-id="a3291-111">DeleteMethod</span></span>
> - <span data-ttu-id="a3291-112">Metoda InsertMethod</span><span class="sxs-lookup"><span data-stu-id="a3291-112">InsertMethod</span></span>
> - <span data-ttu-id="a3291-113">Metoda UpdateMethod</span><span class="sxs-lookup"><span data-stu-id="a3291-113">UpdateMethod</span></span>
> 
> <span data-ttu-id="a3291-114">Tyto vlastnosti zobrazí název metody, která zpracovává odpovídající operace.</span><span class="sxs-lookup"><span data-stu-id="a3291-114">These properties receive the name of the method that handles the corresponding operation.</span></span> <span data-ttu-id="a3291-115">V rámci této metody poskytují logiku pro interakci s daty.</span><span class="sxs-lookup"><span data-stu-id="a3291-115">Within that method, you provide the logic for interacting with the data.</span></span>
> 
> <span data-ttu-id="a3291-116">V tomto kurzu vychází z projektu vytvořeného v prvním [část](retrieving-data.md) řady.</span><span class="sxs-lookup"><span data-stu-id="a3291-116">This tutorial builds on the project created in the first [part](retrieving-data.md) of the series.</span></span>
> 
> <span data-ttu-id="a3291-117">Je možné [Stáhnout](https://go.microsoft.com/fwlink/?LinkId=286116) dokončený projekt v jazyce C# nebo VB.</span><span class="sxs-lookup"><span data-stu-id="a3291-117">You can [download](https://go.microsoft.com/fwlink/?LinkId=286116) the complete project in C# or VB.</span></span> <span data-ttu-id="a3291-118">Ke stažení kódu funguje pomocí sady Visual Studio 2012 nebo Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="a3291-118">The downloadable code works with either Visual Studio 2012 or Visual Studio 2013.</span></span> <span data-ttu-id="a3291-119">Používá šablonu Visual Studio 2012, která se trochu liší od sady Visual Studio 2013 šablonu uvedenou v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="a3291-119">It uses the Visual Studio 2012 template, which is slightly different than the Visual Studio 2013 template shown in this tutorial.</span></span>


## <a name="what-youll-build"></a><span data-ttu-id="a3291-120">Co budete vytvářet</span><span class="sxs-lookup"><span data-stu-id="a3291-120">What you'll build</span></span>

<span data-ttu-id="a3291-121">V tomto kurzu budete:</span><span class="sxs-lookup"><span data-stu-id="a3291-121">In this tutorial, you'll:</span></span>

1. <span data-ttu-id="a3291-122">Přidání šablon dynamických dat</span><span class="sxs-lookup"><span data-stu-id="a3291-122">Add dynamic data templates</span></span>
2. <span data-ttu-id="a3291-123">Povolit aktualizaci a odstraňování dat prostřednictvím metody vazby modelu</span><span class="sxs-lookup"><span data-stu-id="a3291-123">Enable updating and deleting data through model binding methods</span></span>
3. <span data-ttu-id="a3291-124">Použít pravidla validace dat – povolit při vytvoření nového záznamu v databázi</span><span class="sxs-lookup"><span data-stu-id="a3291-124">Apply data validation rules - Enable creating a new record in the database</span></span>

## <a name="add-dynamic-data-templates"></a><span data-ttu-id="a3291-125">Přidání šablon dynamických dat</span><span class="sxs-lookup"><span data-stu-id="a3291-125">Add dynamic data templates</span></span>

<span data-ttu-id="a3291-126">Pokud chcete poskytovat nejlepší uživatelské prostředí a minimalizovat opakování kódu, budete používat dynamická data šablony.</span><span class="sxs-lookup"><span data-stu-id="a3291-126">To provide the best user experience and minimize code repetition, you will use dynamic data templates.</span></span> <span data-ttu-id="a3291-127">Vám umožní snadnou integraci dynamických dat předem připravené šablony do existující lokalitu pomocí instalace balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="a3291-127">You can easily integrate pre-built dynamic data templates into your existing site by installing a NuGet package.</span></span>

<span data-ttu-id="a3291-128">Z **spravovat balíčky NuGet**, nainstalujte **DynamicDataTemplatesCS**.</span><span class="sxs-lookup"><span data-stu-id="a3291-128">From the **Manage NuGet Packages**, install the **DynamicDataTemplatesCS**.</span></span>

![dynamické datové šablony](updating-deleting-and-creating-data/_static/image1.png)

<span data-ttu-id="a3291-130">Všimněte si, že váš projekt nyní obsahuje složku s názvem **DynamicData**.</span><span class="sxs-lookup"><span data-stu-id="a3291-130">Notice that your project now includes a folder named **DynamicData**.</span></span> <span data-ttu-id="a3291-131">V této složce najdete šablony, které budou automaticky použita pro dynamické ovládací prvky ve webových formulářů.</span><span class="sxs-lookup"><span data-stu-id="a3291-131">In that folder, you will find the templates that are automatically applied to dynamic controls in your web forms.</span></span>

![Složka dynamických dat](updating-deleting-and-creating-data/_static/image2.png)

## <a name="enable-updating-and-deleting"></a><span data-ttu-id="a3291-133">Povolit aktualizace a odstranění</span><span class="sxs-lookup"><span data-stu-id="a3291-133">Enable updating and deleting</span></span>

<span data-ttu-id="a3291-134">Povolení uživatelům aktualizovat a odstraňovat záznamy v databázi je velmi podobný procesu pro načítání dat.</span><span class="sxs-lookup"><span data-stu-id="a3291-134">Enabling users to update and delete records in the database is very similar to the process for retrieving data.</span></span> <span data-ttu-id="a3291-135">V **UpdateMethod** a **DeleteMethod** vlastnosti, zadejte názvy metod, které provádějí tyto operace.</span><span class="sxs-lookup"><span data-stu-id="a3291-135">In the **UpdateMethod** and **DeleteMethod** properties, you specify the names of the methods that perform those operations.</span></span> <span data-ttu-id="a3291-136">Pomocí ovládacího prvku GridView můžete také zadat automatické generování upravit a odstranit tlačítka.</span><span class="sxs-lookup"><span data-stu-id="a3291-136">With a GridView control, you can also specify the automatic generation of edit and delete buttons.</span></span> <span data-ttu-id="a3291-137">Následující zvýrazněný kód ukazuje budou přidány do kódu ovládacího prvku GridView.</span><span class="sxs-lookup"><span data-stu-id="a3291-137">The following highlighted code shows the additions to the GridView code.</span></span>

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample1.aspx?highlight=4-5)]

<span data-ttu-id="a3291-138">V souboru kódu na pozadí přidat sadu pomocí příkazu pro **System.Data.Entity.Infrastructure**.</span><span class="sxs-lookup"><span data-stu-id="a3291-138">In the code-behind file, add a using statement for **System.Data.Entity.Infrastructure**.</span></span>

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample2.cs)]

<span data-ttu-id="a3291-139">Potom přidejte následující aktualizace a odstranění metody.</span><span class="sxs-lookup"><span data-stu-id="a3291-139">Then, add the following update and delete methods.</span></span>

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample3.cs)]

<span data-ttu-id="a3291-140">**TryUpdateModel** metoda platí odpovídající hodnoty vázané na data z webového formuláře pro datovou položku.</span><span class="sxs-lookup"><span data-stu-id="a3291-140">The **TryUpdateModel** method applies the matching data-bound values from the web form to the data item.</span></span> <span data-ttu-id="a3291-141">Položku dat načte na základě hodnoty parametru id.</span><span class="sxs-lookup"><span data-stu-id="a3291-141">The data item is retrieved based on the value of the id parameter.</span></span>

## <a name="enforce-validation-requirements"></a><span data-ttu-id="a3291-142">Vynutit požadavky na ověření</span><span class="sxs-lookup"><span data-stu-id="a3291-142">Enforce validation requirements</span></span>

<span data-ttu-id="a3291-143">Ověření atributy, které jste použili k vlastnostem FirstName, LastName a rok ve třídě Student automaticky vynucuje při aktualizaci data.</span><span class="sxs-lookup"><span data-stu-id="a3291-143">The validation attributes that you applied to the FirstName, LastName, and Year properties in the Student class are automatically enforced when updating the data.</span></span> <span data-ttu-id="a3291-144">Ovládací prvky DynamicField přidat klientských a serverových validátory na základě atributů ověření.</span><span class="sxs-lookup"><span data-stu-id="a3291-144">The DynamicField controls add client and server validators based on the validation attributes.</span></span> <span data-ttu-id="a3291-145">Vlastnosti FirstName a LastName jsou povinné.</span><span class="sxs-lookup"><span data-stu-id="a3291-145">The FirstName and LastName properties are both required.</span></span> <span data-ttu-id="a3291-146">Jméno nemůže být delší než 20 znaků a příjmení nemůže být delší než 40 znaků.</span><span class="sxs-lookup"><span data-stu-id="a3291-146">FirstName cannot exceed 20 characters in length, and LastName cannot exceed 40 characters.</span></span> <span data-ttu-id="a3291-147">Rok musí být platná hodnota pro výčet AcademicYear.</span><span class="sxs-lookup"><span data-stu-id="a3291-147">Year must be a valid value for the AcademicYear enumeration.</span></span>

<span data-ttu-id="a3291-148">Pokud uživatel je v rozporu mezi požadavky na ověřování, aktualizace nebude pokračovat.</span><span class="sxs-lookup"><span data-stu-id="a3291-148">If the user violates one of the validation requirements, the update does not proceed.</span></span> <span data-ttu-id="a3291-149">Pokud chcete zobrazit chybová zpráva, přidejte ovládací prvek souhrnu ověření nad prvku GridView.</span><span class="sxs-lookup"><span data-stu-id="a3291-149">To see the error message, add a ValidationSummary control above the GridView.</span></span> <span data-ttu-id="a3291-150">Chcete-li zobrazit chyby ověření z vazby modelu, nastavte **ShowModelStateErrors** vlastnost nastavena na hodnotu **true**.</span><span class="sxs-lookup"><span data-stu-id="a3291-150">To display the validation errors from model binding, set the **ShowModelStateErrors** property set to **true**.</span></span> 

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample4.aspx)]

<span data-ttu-id="a3291-151">Spusťte webovou aplikaci a aktualizovat a odstraní všechny záznamy.</span><span class="sxs-lookup"><span data-stu-id="a3291-151">Run the web application, and update and delete any of the records.</span></span>

![aktualizace dat](updating-deleting-and-creating-data/_static/image3.png)

<span data-ttu-id="a3291-153">Při v režimu úprav hodnotu pro vlastnost rok automaticky takto rozevírací seznam, Všimněte si, že.</span><span class="sxs-lookup"><span data-stu-id="a3291-153">Notice that when in the edit mode the value for the Year property is automatically rendered as a drop down list.</span></span> <span data-ttu-id="a3291-154">Vlastnost Year je hodnota výčtu a šablona dynamických dat pro hodnotu výčtu určí rozevírací seznam pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="a3291-154">The Year property is an enumeration value, and the dynamic data template for an enumeration value specifies a drop down list for editing.</span></span> <span data-ttu-id="a3291-155">Tuto šablonu lze najít otevřením **výčet\_Edit.ascx** soubor **DynamicData**/**FieldTemplates** složky.</span><span class="sxs-lookup"><span data-stu-id="a3291-155">You can find that template by opening the **Enumeration\_Edit.ascx** file in the **DynamicData**/**FieldTemplates** folder.</span></span>

<span data-ttu-id="a3291-156">Pokud zadáte platné hodnoty, aktualizace se úspěšně dokončí.</span><span class="sxs-lookup"><span data-stu-id="a3291-156">If you provide valid values, the update completes successfully.</span></span> <span data-ttu-id="a3291-157">Pokud porušují jeden z těchto požadavků ověřování, aktualizace nebude pokračovat a je nad mřížkou zobrazí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="a3291-157">If you violate one of the validation requirements, the update does not proceed and an error message is displayed above the grid.</span></span>

![Chybová zpráva](updating-deleting-and-creating-data/_static/image4.png)

## <a name="add-new-records"></a><span data-ttu-id="a3291-159">Přidat nové záznamy</span><span class="sxs-lookup"><span data-stu-id="a3291-159">Add new records</span></span>

<span data-ttu-id="a3291-160">Ovládací prvek GridView nezahrnuje **InsertMethod** vlastnost a nelze jej proto použít pro přidání nového záznamu s vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="a3291-160">The GridView control does not include the **InsertMethod** property and therefore cannot be used for adding a new record with model binding.</span></span> <span data-ttu-id="a3291-161">Můžete najít vlastnost InsertMethod **FormView**, **DetailsView**, nebo **ListView** ovládacích prvků.</span><span class="sxs-lookup"><span data-stu-id="a3291-161">You can find the InsertMethod property in the **FormView**, **DetailsView**, or **ListView** controls.</span></span> <span data-ttu-id="a3291-162">V tomto kurzu použijete ovládacího prvku FormView přidáte nový záznam.</span><span class="sxs-lookup"><span data-stu-id="a3291-162">In this tutorial, you will use a FormView control to add a new record.</span></span>

<span data-ttu-id="a3291-163">Nejprve přidejte odkaz na novou stránku, kterou vytvoříte pro přidání nového záznamu.</span><span class="sxs-lookup"><span data-stu-id="a3291-163">First, add a link to the new page you will create for adding a new record.</span></span> <span data-ttu-id="a3291-164">Nad ValidationSummary přidejte:</span><span class="sxs-lookup"><span data-stu-id="a3291-164">Above the ValidationSummary, add:</span></span>

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample5.aspx)]

<span data-ttu-id="a3291-165">Nový odkaz se zobrazí v horní části stránky obsahu pro studenty stránky.</span><span class="sxs-lookup"><span data-stu-id="a3291-165">The new link will appear at the top of the content for the Students page.</span></span>

![Nový odkaz](updating-deleting-and-creating-data/_static/image5.png)

<span data-ttu-id="a3291-167">Pak přidejte nový webový formulář používající stránku předlohy a pojmenujte ho **AddStudent**.</span><span class="sxs-lookup"><span data-stu-id="a3291-167">Then, add a new web form using a master page, and name it **AddStudent**.</span></span> <span data-ttu-id="a3291-168">Vyberte Site.Master jako stránky předlohy.</span><span class="sxs-lookup"><span data-stu-id="a3291-168">Select Site.Master as the master page.</span></span>

<span data-ttu-id="a3291-169">Vykreslí se pole pro přidání nového studenta pomocí **DynamicEntity** ovládacího prvku.</span><span class="sxs-lookup"><span data-stu-id="a3291-169">You will render the fields for adding a new student by using a **DynamicEntity** control.</span></span> <span data-ttu-id="a3291-170">Ovládací prvek DynamicEntity vykreslí této upravitelné vlastnosti třídy zadaná ve vlastnosti ItemType.</span><span class="sxs-lookup"><span data-stu-id="a3291-170">The DynamicEntity control renders that editable properties in the class specified in the ItemType property.</span></span> <span data-ttu-id="a3291-171">Vlastnost StudentID byl označen atributem **[ScaffoldColumn(false)]** atribut, takže není vykresleno.</span><span class="sxs-lookup"><span data-stu-id="a3291-171">The StudentID property was marked with the **[ScaffoldColumn(false)]** attribute so it is not rendered.</span></span> <span data-ttu-id="a3291-172">V zástupném symbolu MainContent AddStudent stránky přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="a3291-172">In the MainContent placeholder of the AddStudent page, add the following code.</span></span>

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample6.aspx)]

<span data-ttu-id="a3291-173">V souboru kódu na pozadí (AddStudent.aspx.cs) přidejte **pomocí** příkaz **ContosoUniversityModelBinding.Models** oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="a3291-173">In the code-behind file (AddStudent.aspx.cs), add a **using** statement for the **ContosoUniversityModelBinding.Models** namespace.</span></span>

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample7.cs)]

<span data-ttu-id="a3291-174">Potom přidejte následující metody k určení jak vložit nový záznam a obslužnou rutinu události pro tlačítko Storno.</span><span class="sxs-lookup"><span data-stu-id="a3291-174">Then, add the following methods to specify how to insert a new record and an event handler for the cancel button.</span></span>

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample8.cs)]

<span data-ttu-id="a3291-175">Uložte všechny změny.</span><span class="sxs-lookup"><span data-stu-id="a3291-175">Save all of the changes.</span></span>

<span data-ttu-id="a3291-176">Spusťte webovou aplikaci a vytvoření nového objektu student.</span><span class="sxs-lookup"><span data-stu-id="a3291-176">Run the web application and create a new student.</span></span>

![Přidání nového studenta](updating-deleting-and-creating-data/_static/image6.png)

<span data-ttu-id="a3291-178">Klikněte na tlačítko **vložit** a Všimněte si, že se vytvořila nová studentů.</span><span class="sxs-lookup"><span data-stu-id="a3291-178">Click **Insert** and notice the new student has been created.</span></span>

![zobrazení nového objektu student](updating-deleting-and-creating-data/_static/image7.png)

## <a name="conclusion"></a><span data-ttu-id="a3291-180">Závěr</span><span class="sxs-lookup"><span data-stu-id="a3291-180">Conclusion</span></span>

<span data-ttu-id="a3291-181">V tomto kurzu jste povolili aktualizaci, odstranění a vytvoření data.</span><span class="sxs-lookup"><span data-stu-id="a3291-181">In this tutorial, you enabled updating, deleting, and creating data.</span></span> <span data-ttu-id="a3291-182">Můžete zajistit, že ověřovací pravidla se použijí při interakci s daty.</span><span class="sxs-lookup"><span data-stu-id="a3291-182">You ensured validation rules are applied when interacting with the data.</span></span>

<span data-ttu-id="a3291-183">V dalším [kurzu](sorting-paging-and-filtering-data.md) v této sérii, vám umožní řazení, stránkování a filtrování dat.</span><span class="sxs-lookup"><span data-stu-id="a3291-183">In the next [tutorial](sorting-paging-and-filtering-data.md) in this series, you will enable sorting, paging, and filtering data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a3291-184">[Předchozí](retrieving-data.md)
> [další](sorting-paging-and-filtering-data.md)</span><span class="sxs-lookup"><span data-stu-id="a3291-184">[Previous](retrieving-data.md)
[Next](sorting-paging-and-filtering-data.md)</span></span>
