---
uid: mvc/overview/getting-started/database-first-development/enhancing-data-validation
title: 'Kurz: Vylepšení ověřování dat pro EF Database First s ASP.NET MVC aplikace'
description: Tento kurz se zaměřuje na přidání anotací dat do datového modelu k určení požadavků na ověření a zobrazení formátování.
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: 0ed5e67a-34c0-4b57-84a6-802b0fb3cd00
msc.legacyurl: /mvc/overview/getting-started/database-first-development/enhancing-data-validation
msc.type: authoredcontent
ms.openlocfilehash: 85299d70c6cba52c1d40a42edfd429c96318134a
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236481"
---
# <a name="tutorial-enhance-data-validation-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="ddf0a-103">Kurz: Vylepšení ověřování dat pro EF Database First s ASP.NET MVC aplikace</span><span class="sxs-lookup"><span data-stu-id="ddf0a-103">Tutorial: Enhance data validation for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="ddf0a-104">Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="ddf0a-105">V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="ddf0a-106">Generovaný kód odpovídá sloupců v tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="ddf0a-107">Tento kurz se zaměřuje na přidání anotací dat do datového modelu k určení požadavků na ověření a zobrazení formátování.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-107">This tutorial focuses on adding data annotations to the data model to specify validation requirements and display formatting.</span></span> <span data-ttu-id="ddf0a-108">Byl vylepšen závislosti na zpětnou vazbu od uživatelů v části komentáře.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-108">It was improved based on feedback from users in the comments section.</span></span>

<span data-ttu-id="ddf0a-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="ddf0a-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ddf0a-110">Přidání anotací dat</span><span class="sxs-lookup"><span data-stu-id="ddf0a-110">Add data annotations</span></span>
> * <span data-ttu-id="ddf0a-111">Přidání třídy metadat</span><span class="sxs-lookup"><span data-stu-id="ddf0a-111">Add metadata classes</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ddf0a-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ddf0a-112">Prerequisites</span></span>

* [<span data-ttu-id="ddf0a-113">Přizpůsobení zobrazení</span><span class="sxs-lookup"><span data-stu-id="ddf0a-113">Customize a view</span></span>](customizing-a-view.md)

## <a name="add-data-annotations"></a><span data-ttu-id="ddf0a-114">Přidání anotací dat</span><span class="sxs-lookup"><span data-stu-id="ddf0a-114">Add data annotations</span></span>

<span data-ttu-id="ddf0a-115">Jak už jste viděli v dřívějším tématu, některá pravidla ověřování dat se automaticky použijí se vstupem uživatele.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-115">As you saw in an earlier topic, some data validation rules are automatically applied to the user input.</span></span> <span data-ttu-id="ddf0a-116">Například můžete pouze zadat číslo pro vlastnost na podnikové úrovni.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-116">For example, you can only provide a number for the Grade property.</span></span> <span data-ttu-id="ddf0a-117">Chcete-li určit další pravidla pro ověření dat, můžete přidat anotacemi dat do vaší třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-117">To specify more data validation rules, you can add data annotations to your model class.</span></span> <span data-ttu-id="ddf0a-118">Tyto poznámky platí v celé vaší webové aplikace pro zadanou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-118">These annotations are applied throughout your web application for the specified property.</span></span> <span data-ttu-id="ddf0a-119">Můžete také použít atributy formátování, které se mění, jak se zobrazují vlastnosti; například změna hodnoty použité pro textové popisky.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-119">You can also apply formatting attributes that change how the properties are displayed; such as, changing the value used for text labels.</span></span>

<span data-ttu-id="ddf0a-120">V tomto kurzu přidáte datových poznámek k omezení délky zadané vlastnosti FirstName, LastName a MiddleName hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-120">In this tutorial, you will add data annotations to restrict the length of the values provided for the FirstName, LastName, and MiddleName properties.</span></span> <span data-ttu-id="ddf0a-121">V databázi tyto hodnoty jsou omezené na 50 znaků. Nicméně ve webové aplikaci tento limit počtu znaků není aktuálně používá.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-121">In the database, these values are limited to 50 characters; however, in your web application that character limit is currently not enforced.</span></span> <span data-ttu-id="ddf0a-122">Pokud uživatel zadá více než 50 znaků. pro jednu z těchto hodnot, na stránce havaruje při pokusu o uložení hodnoty do databáze.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-122">If a user provides more than 50 characters for one of those values, the page will crash when attempting to save the value to the database.</span></span> <span data-ttu-id="ddf0a-123">Bude také omezit na podnikové úrovni pro hodnoty v rozmezí 0 až 4.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-123">You will also restrict Grade to values between 0 and 4.</span></span>

<span data-ttu-id="ddf0a-124">Vyberte **modely** > **ContosoModel.edmx** > **ContosoModel.tt** a otevřete *Student.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-124">Select **Models** > **ContosoModel.edmx** > **ContosoModel.tt** and open the *Student.cs* file.</span></span> <span data-ttu-id="ddf0a-125">Přidejte následující zvýrazněný kód do třídy.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-125">Add the following highlighted code to the class.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample1.cs?highlight=5,15,17,20)]

<span data-ttu-id="ddf0a-126">Otevřít *Enrollment.cs* a přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-126">Open *Enrollment.cs* and add the following highlighted code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample2.cs?highlight=5,10)]

<span data-ttu-id="ddf0a-127">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-127">Build the solution.</span></span>

<span data-ttu-id="ddf0a-128">Klikněte na tlačítko **seznamu studentů** a vyberte **upravit**.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-128">Click **List of students** and select **Edit**.</span></span> <span data-ttu-id="ddf0a-129">Pokud se pokusíte k zadání více než 50 znaků, zobrazí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-129">If you attempt to enter more than 50 characters, an error message is displayed.</span></span>

![zobrazit chybová zpráva](enhancing-data-validation/_static/image1.png)

<span data-ttu-id="ddf0a-131">Přejděte zpět na domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-131">Go back to the home page.</span></span> <span data-ttu-id="ddf0a-132">Klikněte na tlačítko **seznamu registrací** a vyberte **upravit**.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-132">Click **List of enrollments** and select **Edit**.</span></span> <span data-ttu-id="ddf0a-133">Pokus o zajištění kvality nad 4.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-133">Attempt to provide a grade above 4.</span></span> <span data-ttu-id="ddf0a-134">Zobrazí se tato chyba: *Pole na podnikové úrovni musí být mezi 0 a 4.*</span><span class="sxs-lookup"><span data-stu-id="ddf0a-134">You will receive this error: *The field Grade must be between 0 and 4.*</span></span>

## <a name="add-metadata-classes"></a><span data-ttu-id="ddf0a-135">Přidání třídy metadat</span><span class="sxs-lookup"><span data-stu-id="ddf0a-135">Add metadata classes</span></span>

<span data-ttu-id="ddf0a-136">Přidávání atributů ověření přímo do třídy modelu funguje, když nepočítáte databáze, kterou chcete změnit; Nicméně pokud změny databáze a potřebujete se znovu vygenerovat třídu modelu, budou ztraceny všechny atributy, které jste použili pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-136">Adding the validation attributes directly to the model class works when you do not expect the database to change; however, if your database changes and you need to regenerate the model class, you will lose all of the attributes you had applied to the model class.</span></span> <span data-ttu-id="ddf0a-137">Tento přístup může být velmi neefektivní a náchylné ke ztrátě důležitých ověřovacích pravidel.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-137">This approach can be very inefficient and prone to losing important validation rules.</span></span>

<span data-ttu-id="ddf0a-138">K tomuto problému vyhnout, můžete přidat třídu metadat, která obsahuje atributy.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-138">To avoid this problem, you can add a metadata class that contains the attributes.</span></span> <span data-ttu-id="ddf0a-139">Když přiřadíte třídy modelu pro třídu metadat, jsou tyto atributy použité pro model.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-139">When you associate the model class to the metadata class, those attributes are applied to the model.</span></span> <span data-ttu-id="ddf0a-140">V takovém případě může znovu vygenerovat třídu modelu bez ztráty všechny atributy, které se použily třídu metadat.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-140">In this approach, the model class can be regenerated without losing all of the attributes that have been applied to the metadata class.</span></span>

<span data-ttu-id="ddf0a-141">V **modely** složky, přidejte třídu pojmenovanou *Metadata.cs*.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-141">In the **Models** folder, add a class named *Metadata.cs*.</span></span>

<span data-ttu-id="ddf0a-142">Nahraďte kód v *Metadata.cs* následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-142">Replace the code in *Metadata.cs* with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample3.cs)]

<span data-ttu-id="ddf0a-143">Tyto třídy metadata obsahují všechny atributy ověřování, které jste použili předtím na třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-143">These metadata classes contain all of the validation attributes that you had previously applied to the model classes.</span></span> <span data-ttu-id="ddf0a-144">**Zobrazení** atribut se používá ke změně hodnoty používané pro textové popisky.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-144">The **Display** attribute is used to change the value used for text labels.</span></span>

<span data-ttu-id="ddf0a-145">Nyní je třeba přidružit tříd modelu třídy metadat.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-145">Now, you must associate the model classes with the metadata classes.</span></span>

<span data-ttu-id="ddf0a-146">V **modely** složky, přidejte třídu pojmenovanou *PartialClasses.cs*.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-146">In the **Models** folder, add a class named *PartialClasses.cs*.</span></span>

<span data-ttu-id="ddf0a-147">Obsah souboru nahraďte následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-147">Replace the contents of the file with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample4.cs)]

<span data-ttu-id="ddf0a-148">Všimněte si, že každá třída je označena jako `partial` třídy a každý odpovídá názvu a oboru názvů jako třída, která není automaticky vygenerován.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-148">Notice that each class is marked as a `partial` class, and each matches the name and namespace as the class that is automatically generated.</span></span> <span data-ttu-id="ddf0a-149">Použitím atributu metadat na částečné třídy zajistíte, že atributy ověření dat se použije pro automaticky generované třídy.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-149">By applying the metadata attribute to the partial class, you ensure that the data validation attributes will be applied to the automatically-generated class.</span></span> <span data-ttu-id="ddf0a-150">Tyto atributy nesmí být ztraceny po budete znovu generovat třídy modelu, protože metadat atributu se použije v částečné třídy, které se znovu vygeneroval.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-150">These attributes will not be lost when you regenerate the model classes because the metadata attribute is applied in partial classes that are not regenerated.</span></span>

<span data-ttu-id="ddf0a-151">Chcete-li znovu vygenerovat automaticky vygenerované třídy, otevřete *ContosoModel.edmx* souboru.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-151">To regenerate the automatically-generated classes, open the *ContosoModel.edmx* file.</span></span> <span data-ttu-id="ddf0a-152">Ještě jednou klikněte pravým tlačítkem na návrhové ploše a vyberte **aktualizace modelů z databáze**.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-152">Once again, right-click on the design surface and select **Update Model from Database**.</span></span> <span data-ttu-id="ddf0a-153">I když nedošlo ke změně databáze, tento proces se znova vygeneruje třídy.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-153">Even though you have not changed the database, this process will regenerate the classes.</span></span> <span data-ttu-id="ddf0a-154">V **aktualizovat** kartu, vyberte možnost **tabulky** a **Dokončit**.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-154">In the **Refresh** tab, select **Tables** and **Finish**.</span></span>

<span data-ttu-id="ddf0a-155">Uložit *ContosoModel.edmx* souboru, aby se změny projevily.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-155">Save the *ContosoModel.edmx* file to apply the changes.</span></span>

<span data-ttu-id="ddf0a-156">Otevřít *Student.cs* souboru nebo *Enrollment.cs* soubor a Všimněte si, že jste provedli dříve atributy ověření dat už nejsou v souboru.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-156">Open the *Student.cs* file or the *Enrollment.cs* file, and notice that the data validation attributes you applied earlier are no longer in the file.</span></span> <span data-ttu-id="ddf0a-157">Ale spusťte aplikaci a Všimněte si, že ověřovací pravidla, se uplatní při zadávání dat.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-157">However, run the application, and notice that the validation rules are still applied when you enter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ddf0a-158">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ddf0a-158">Additional resources</span></span>

<span data-ttu-id="ddf0a-159">Úplný seznam datových poznámek ověření můžete provést u třídy a vlastnosti, naleznete v tématu [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span><span class="sxs-lookup"><span data-stu-id="ddf0a-159">For a full list of data validation annotations you can apply to properties and classes, see [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span></span>

## <a name="next-steps"></a><span data-ttu-id="ddf0a-160">Další kroky</span><span class="sxs-lookup"><span data-stu-id="ddf0a-160">Next steps</span></span>

<span data-ttu-id="ddf0a-161">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="ddf0a-161">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ddf0a-162">Přidání datových poznámek</span><span class="sxs-lookup"><span data-stu-id="ddf0a-162">Added data annotations</span></span>
> * <span data-ttu-id="ddf0a-163">Přidání metadat třídy</span><span class="sxs-lookup"><span data-stu-id="ddf0a-163">Added metadata classes</span></span>

<span data-ttu-id="ddf0a-164">Přejděte k dalšímu kurzu, kde se naučíte, jak publikovat webovou aplikaci a databázi do Azure.</span><span class="sxs-lookup"><span data-stu-id="ddf0a-164">Advance to the next tutorial to learn how to publish the web app and database to Azure.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="ddf0a-165">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="ddf0a-165">Publish to Azure</span></span>](publish-to-azure.md)