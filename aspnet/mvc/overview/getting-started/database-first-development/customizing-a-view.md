---
uid: mvc/overview/getting-started/database-first-development/customizing-a-view
title: 'EF Database First s ASP.NET MVC: přizpůsobení zobrazení | Dokumentace Microsoftu'
author: Rick-Anderson
description: Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. Tento kurz seri...
ms.author: riande
ms.date: 10/01/2014
ms.assetid: 269380ff-d7e1-4035-8ad1-fe1316a25f76
msc.legacyurl: /mvc/overview/getting-started/database-first-development/customizing-a-view
msc.type: authoredcontent
ms.openlocfilehash: f66e097d53514ab3842e04cd545ca626c652478a
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021207"
---
<a name="ef-database-first-with-aspnet-mvc-customizing-a-view"></a><span data-ttu-id="6ebdf-104">EF Database First s ASP.NET MVC: přizpůsobení zobrazení</span><span class="sxs-lookup"><span data-stu-id="6ebdf-104">EF Database First with ASP.NET MVC: Customizing a View</span></span>
====================
<span data-ttu-id="6ebdf-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="6ebdf-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="6ebdf-106">Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-106">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="6ebdf-107">V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-107">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="6ebdf-108">Generovaný kód odpovídá sloupců v tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-108">The generated code corresponds to the columns in the database table.</span></span>
> 
> <span data-ttu-id="6ebdf-109">Tato části této série se zaměřuje na změny automaticky generované zobrazení k vylepšení v prezentaci.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-109">This part of the series focuses on changing the automatically-generated views to enhance the presentation.</span></span>


## <a name="add-enrolled-courses-to-student-details"></a><span data-ttu-id="6ebdf-110">Přidat zaregistrovaná kurzy Podrobnosti studenta</span><span class="sxs-lookup"><span data-stu-id="6ebdf-110">Add enrolled courses to student details</span></span>

<span data-ttu-id="6ebdf-111">Generovaný kód poskytuje dobrým výchozím bodem pro vaši aplikaci, ale neposkytuje nutně všechny funkce, které potřebujete ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-111">The generated code provides a good starting point for your application but it does not necessarily provide all of the functionality that you need in your application.</span></span> <span data-ttu-id="6ebdf-112">Můžete upravit kód pro konkrétní požadavkům vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-112">You can customize the code to meet the particular requirements of your application.</span></span> <span data-ttu-id="6ebdf-113">V současné době aplikace zaregistrovaná kurzy pro vybrané student nezobrazuje.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-113">Currently, your application does not display the enrolled courses for the selected student.</span></span> <span data-ttu-id="6ebdf-114">V této části přidáte zaregistrované kurzy pro každého studenta do **podrobnosti** zobrazení pro studenta.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-114">In this section, you will add the enrolled courses for each student to the **Details** view for the student.</span></span>

<span data-ttu-id="6ebdf-115">Otevřít **Students/Details.cshtml**a pod poslední &lt;/dl&gt; kartu, ale před uzavírající &lt;/div&gt; značky, přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-115">Open **Students/Details.cshtml**, and below the last &lt;/dl&gt; tab, but before the closing &lt;/div&gt; tag, add the following code.</span></span>

[!code-cshtml[Main](customizing-a-view/samples/sample1.cshtml)]

<span data-ttu-id="6ebdf-116">Tento kód vytvoří tabulku, která se zobrazí řádek pro každý záznam v tabulce registrace pro vybrané studentů.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-116">This code creates a table that displays a row for each record in the Enrollment table for the selected student.</span></span> <span data-ttu-id="6ebdf-117">**Zobrazení** metoda vykreslí HTML pro objekt (modelItem), který představuje výraz.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-117">The **Display** method renders HTML for the object (modelItem) that represents the expression.</span></span> <span data-ttu-id="6ebdf-118">Použití zobrazení metody (místo v kódu jednoduše vložení hodnoty vlastnosti) do Ujistěte se, že hodnota formátována správně na základě jeho typu a šablonu pro daný typ.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-118">You use the Display method (rather than simply embedding the property value in the code) to make sure the value is formatted correctly based on its type and the template for that type.</span></span> <span data-ttu-id="6ebdf-119">V tomto příkladu každý výraz vrátí jedinou vlastnost z aktuální záznam ve smyčce a hodnoty jsou primitivní typy, které jsou generovány jako text.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-119">In this example, each expression returns a single property from the current record in the loop, and the values are primitive types which are rendered as text.</span></span>

<span data-ttu-id="6ebdf-120">Přejděte do zobrazení pro studenty/Index znovu a vyberte **podrobnosti** pro jeden z studenty.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-120">Browse to the Students/Index view again and select **Details** for one of the students.</span></span> <span data-ttu-id="6ebdf-121">Uvidíte, že registrovaná kurzy byla zahrnuta v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6ebdf-121">You will see the enrolled courses have been included in the view.</span></span>

![studenta s registrací](customizing-a-view/_static/image1.png)

> [!div class="step-by-step"]
> <span data-ttu-id="6ebdf-123">[Předchozí](changing-the-database.md)
> [další](enhancing-data-validation.md)</span><span class="sxs-lookup"><span data-stu-id="6ebdf-123">[Previous](changing-the-database.md)
[Next](enhancing-data-validation.md)</span></span>
