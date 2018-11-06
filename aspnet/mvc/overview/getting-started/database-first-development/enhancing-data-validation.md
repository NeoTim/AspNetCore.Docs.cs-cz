---
uid: mvc/overview/getting-started/database-first-development/enhancing-data-validation
title: 'EF Database First s ASP.NET MVC: rozšířené ověřování dat | Dokumentace Microsoftu'
author: Rick-Anderson
description: Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. Tento kurz seri...
ms.author: riande
ms.date: 12/29/2014
ms.assetid: 0ed5e67a-34c0-4b57-84a6-802b0fb3cd00
msc.legacyurl: /mvc/overview/getting-started/database-first-development/enhancing-data-validation
msc.type: authoredcontent
ms.openlocfilehash: df2cd99619f097c9f392e8fe7352c1ce3a69c8df
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021661"
---
<a name="ef-database-first-with-aspnet-mvc-enhancing-data-validation"></a><span data-ttu-id="64a2e-104">EF Database First s ASP.NET MVC: rozšířené ověřování dat</span><span class="sxs-lookup"><span data-stu-id="64a2e-104">EF Database First with ASP.NET MVC: Enhancing Data Validation</span></span>
====================
<span data-ttu-id="64a2e-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="64a2e-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="64a2e-106">Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi.</span><span class="sxs-lookup"><span data-stu-id="64a2e-106">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="64a2e-107">V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="64a2e-107">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="64a2e-108">Generovaný kód odpovídá sloupců v tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="64a2e-108">The generated code corresponds to the columns in the database table.</span></span>
> 
> <span data-ttu-id="64a2e-109">Tato části této série se zaměřuje na přidání anotací dat do datového modelu k určení požadavků na ověření a zobrazení formátování.</span><span class="sxs-lookup"><span data-stu-id="64a2e-109">This part of the series focuses on adding data annotations to the data model to specify validation requirements and display formatting.</span></span> <span data-ttu-id="64a2e-110">Byl vylepšen závislosti na zpětnou vazbu od uživatelů v části komentáře.</span><span class="sxs-lookup"><span data-stu-id="64a2e-110">It was improved based on feedback from users in the comments section.</span></span>


## <a name="add-data-annotations"></a><span data-ttu-id="64a2e-111">Přidání anotací dat</span><span class="sxs-lookup"><span data-stu-id="64a2e-111">Add data annotations</span></span>

<span data-ttu-id="64a2e-112">Jak už jste viděli v dřívějším tématu, některá pravidla ověřování dat se automaticky použijí se vstupem uživatele.</span><span class="sxs-lookup"><span data-stu-id="64a2e-112">As you saw in an earlier topic, some data validation rules are automatically applied to the user input.</span></span> <span data-ttu-id="64a2e-113">Například můžete pouze zadat číslo pro vlastnost na podnikové úrovni.</span><span class="sxs-lookup"><span data-stu-id="64a2e-113">For example, you can only provide a number for the Grade property.</span></span> <span data-ttu-id="64a2e-114">Chcete-li určit další pravidla pro ověření dat, můžete přidat anotacemi dat do vaší třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="64a2e-114">To specify more data validation rules, you can add data annotations to your model class.</span></span> <span data-ttu-id="64a2e-115">Tyto poznámky platí v celé vaší webové aplikace pro zadanou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="64a2e-115">These annotations are applied throughout your web application for the specified property.</span></span> <span data-ttu-id="64a2e-116">Můžete také použít atributy formátování, které se mění, jak se zobrazují vlastnosti; například změna hodnoty použité pro textové popisky.</span><span class="sxs-lookup"><span data-stu-id="64a2e-116">You can also apply formatting attributes that change how the properties are displayed; such as, changing the value used for text labels.</span></span>

<span data-ttu-id="64a2e-117">V tomto kurzu přidáte datových poznámek k omezení délky zadané vlastnosti FirstName, LastName a MiddleName hodnoty.</span><span class="sxs-lookup"><span data-stu-id="64a2e-117">In this tutorial, you will add data annotations to restrict the length of the values provided for the FirstName, LastName, and MiddleName properties.</span></span> <span data-ttu-id="64a2e-118">V databázi tyto hodnoty jsou omezené na 50 znaků. Nicméně ve webové aplikaci tento limit počtu znaků není aktuálně používá.</span><span class="sxs-lookup"><span data-stu-id="64a2e-118">In the database, these values are limited to 50 characters; however, in your web application that character limit is currently not enforced.</span></span> <span data-ttu-id="64a2e-119">Pokud uživatel zadá více než 50 znaků. pro jednu z těchto hodnot, na stránce havaruje při pokusu o uložení hodnoty do databáze.</span><span class="sxs-lookup"><span data-stu-id="64a2e-119">If a user provides more than 50 characters for one of those values, the page will crash when attempting to save the value to the database.</span></span> <span data-ttu-id="64a2e-120">Bude také omezit na podnikové úrovni pro hodnoty v rozmezí 0 až 4.</span><span class="sxs-lookup"><span data-stu-id="64a2e-120">You will also restrict Grade to values between 0 and 4.</span></span>

<span data-ttu-id="64a2e-121">Otevřít **Student.cs** soubor **modely** složky.</span><span class="sxs-lookup"><span data-stu-id="64a2e-121">Open the **Student.cs** file in the **Models** folder.</span></span> <span data-ttu-id="64a2e-122">Přidejte následující zvýrazněný kód do třídy.</span><span class="sxs-lookup"><span data-stu-id="64a2e-122">Add the following highlighted code to the class.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample1.cs?highlight=5,15,17,20)]

<span data-ttu-id="64a2e-123">V Enrollment.cs přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="64a2e-123">In Enrollment.cs, add the following highlighted code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample2.cs?highlight=5,10)]

<span data-ttu-id="64a2e-124">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="64a2e-124">Build the solution.</span></span>

<span data-ttu-id="64a2e-125">Přejděte na stránku pro úpravy nebo vytváření student.</span><span class="sxs-lookup"><span data-stu-id="64a2e-125">Browse to a page for editing or creating a student.</span></span> <span data-ttu-id="64a2e-126">Pokud se pokusíte k zadání více než 50 znaků, zobrazí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="64a2e-126">If you attempt to enter more than 50 characters, an error message is displayed.</span></span>

![zobrazit chybová zpráva](enhancing-data-validation/_static/image1.png)

<span data-ttu-id="64a2e-128">Přejděte na stránku pro úpravy registrace a pokus o poskytují známku vyjádřenou nad 4.</span><span class="sxs-lookup"><span data-stu-id="64a2e-128">Browse to the page for editing enrollments, and attempt to provide a grade above 4.</span></span>

![Chyba rozsahu na podnikové úrovni](enhancing-data-validation/_static/image2.png)

<span data-ttu-id="64a2e-130">Úplný seznam datových poznámek ověření můžete provést u třídy a vlastnosti, naleznete v tématu [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span><span class="sxs-lookup"><span data-stu-id="64a2e-130">For a full list of data validation annotations you can apply to properties and classes, see [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span></span>

## <a name="add-metadata-classes"></a><span data-ttu-id="64a2e-131">Přidání třídy metadat</span><span class="sxs-lookup"><span data-stu-id="64a2e-131">Add metadata classes</span></span>

<span data-ttu-id="64a2e-132">Přidávání atributů ověření přímo do třídy modelu funguje, když nepočítáte databáze, kterou chcete změnit; Nicméně pokud změny databáze a potřebujete se znovu vygenerovat třídu modelu, budou ztraceny všechny atributy, které jste použili pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="64a2e-132">Adding the validation attributes directly to the model class works when you do not expect the database to change; however, if your database changes and you need to regenerate the model class, you will lose all of the attributes you had applied to the model class.</span></span> <span data-ttu-id="64a2e-133">Tento přístup může být velmi neefektivní a náchylné ke ztrátě důležitých ověřovacích pravidel.</span><span class="sxs-lookup"><span data-stu-id="64a2e-133">This approach can be very inefficient and prone to losing important validation rules.</span></span>

<span data-ttu-id="64a2e-134">K tomuto problému vyhnout, můžete přidat třídu metadat, která obsahuje atributy.</span><span class="sxs-lookup"><span data-stu-id="64a2e-134">To avoid this problem, you can add a metadata class that contains the attributes.</span></span> <span data-ttu-id="64a2e-135">Když přiřadíte třídy modelu pro třídu metadat, jsou tyto atributy použité pro model.</span><span class="sxs-lookup"><span data-stu-id="64a2e-135">When you associate the model class to the metadata class, those attributes are applied to the model.</span></span> <span data-ttu-id="64a2e-136">V takovém případě může znovu vygenerovat třídu modelu bez ztráty všechny atributy, které se použily třídu metadat.</span><span class="sxs-lookup"><span data-stu-id="64a2e-136">In this approach, the model class can be regenerated without losing all of the attributes that have been applied to the metadata class.</span></span>

<span data-ttu-id="64a2e-137">V **modely** složky, přidejte třídu pojmenovanou **Metadata.cs**.</span><span class="sxs-lookup"><span data-stu-id="64a2e-137">In the **Models** folder, add a class named **Metadata.cs**.</span></span>

![Přidat třídu metadat](enhancing-data-validation/_static/image3.png)

<span data-ttu-id="64a2e-139">Nahraďte kód v Metadata.cs následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="64a2e-139">Replace the code in Metadata.cs with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample3.cs)]

<span data-ttu-id="64a2e-140">Tyto třídy metadata obsahují všechny atributy ověřování, které jste použili předtím na třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="64a2e-140">These metadata classes contain all of the validation attributes that you had previously applied to the model classes.</span></span> <span data-ttu-id="64a2e-141">**Zobrazení** atribut se používá ke změně hodnoty používané pro textové popisky.</span><span class="sxs-lookup"><span data-stu-id="64a2e-141">The **Display** attribute is used to change the value used for text labels.</span></span>

<span data-ttu-id="64a2e-142">Nyní je třeba přidružit tříd modelu třídy metadat.</span><span class="sxs-lookup"><span data-stu-id="64a2e-142">Now, you must associate the model classes with the metadata classes.</span></span>

<span data-ttu-id="64a2e-143">V **modely** složky, přidejte třídu pojmenovanou **PartialClasses.cs**.</span><span class="sxs-lookup"><span data-stu-id="64a2e-143">In the **Models** folder, add a class named **PartialClasses.cs**.</span></span>

<span data-ttu-id="64a2e-144">Obsah souboru nahraďte následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="64a2e-144">Replace the contents of the file with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample4.cs)]

<span data-ttu-id="64a2e-145">Všimněte si, že každá třída je označena jako `partial` třídy a každý odpovídá názvu a oboru názvů jako třída, která není automaticky vygenerován.</span><span class="sxs-lookup"><span data-stu-id="64a2e-145">Notice that each class is marked as a `partial` class, and each matches the name and namespace as the class that is automatically generated.</span></span> <span data-ttu-id="64a2e-146">Použitím atributu metadat na částečné třídy zajistíte, že atributy ověření dat se použije pro automaticky generované třídy.</span><span class="sxs-lookup"><span data-stu-id="64a2e-146">By applying the metadata attribute to the partial class, you ensure that the data validation attributes will be applied to the automatically-generated class.</span></span> <span data-ttu-id="64a2e-147">Tyto atributy nesmí být ztraceny po budete znovu generovat třídy modelu, protože metadat atributu se použije v částečné třídy, které se znovu vygeneroval.</span><span class="sxs-lookup"><span data-stu-id="64a2e-147">These attributes will not be lost when you regenerate the model classes because the metadata attribute is applied in partial classes that are not regenerated.</span></span>

<span data-ttu-id="64a2e-148">Chcete-li znovu vygenerovat automaticky vygenerované třídy, otevřete soubor ContosoModel.edmx.</span><span class="sxs-lookup"><span data-stu-id="64a2e-148">To regenerate the automatically-generated classes, open the ContosoModel.edmx file.</span></span> <span data-ttu-id="64a2e-149">Ještě jednou klikněte pravým tlačítkem na návrhové ploše a vyberte **aktualizace modelů z databáze**.</span><span class="sxs-lookup"><span data-stu-id="64a2e-149">Once again, right-click on the design surface and select **Update Model from Database**.</span></span> <span data-ttu-id="64a2e-150">I když nedošlo ke změně databáze, tento proces se znova vygeneruje třídy.</span><span class="sxs-lookup"><span data-stu-id="64a2e-150">Even though you have not changed the database, this process will regenerate the classes.</span></span> <span data-ttu-id="64a2e-151">V **aktualizovat** kartu, vyberte možnost **tabulky** a **Dokončit**.</span><span class="sxs-lookup"><span data-stu-id="64a2e-151">In the **Refresh** tab, select **Tables** and **Finish**.</span></span>

![Aktualizovat tabulky](enhancing-data-validation/_static/image4.png)

<span data-ttu-id="64a2e-153">Uložte soubor ContosoModel.edmx, aby se změny projevily.</span><span class="sxs-lookup"><span data-stu-id="64a2e-153">Save the ContosoModel.edmx file to apply the changes.</span></span>

<span data-ttu-id="64a2e-154">Otevřete soubor Student.cs nebo Enrollment.cs a Všimněte si, že atributy ověření dat, které jste použili dříve už nejsou v souboru.</span><span class="sxs-lookup"><span data-stu-id="64a2e-154">Open the Student.cs file or the Enrollment.cs file, and notice that the data validation attributes you applied earlier are no longer in the file.</span></span> <span data-ttu-id="64a2e-155">Ale spusťte aplikaci a Všimněte si, že ověřovací pravidla, se uplatní při zadávání dat.</span><span class="sxs-lookup"><span data-stu-id="64a2e-155">However, run the application, and notice that the validation rules are still applied when you enter data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="64a2e-156">[Předchozí](customizing-a-view.md)
> [další](publish-to-azure.md)</span><span class="sxs-lookup"><span data-stu-id="64a2e-156">[Previous](customizing-a-view.md)
[Next](publish-to-azure.md)</span></span>
