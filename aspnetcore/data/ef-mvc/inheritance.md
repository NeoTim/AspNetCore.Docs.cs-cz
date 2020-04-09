---
title: 'Kurz: Implementace dědičnosti – ASP.NET MVC s EF Core'
description: Tento kurz vám ukáže, jak implementovat dědičnost v datovém modelu pomocí entity framework core v aplikaci ASP.NET core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/inheritance
ms.openlocfilehash: dab3d2b057162f6d986db10e74e3681acc0ada3b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657239"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="998bd-103">Kurz: Implementace dědičnosti – ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="998bd-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="998bd-104">V předchozím kurzu jste zpracovali výjimky souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="998bd-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="998bd-105">Tento kurz vám ukáže, jak implementovat dědičnost v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="998bd-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="998bd-106">V objektově orientovaném programování můžete použít dědičnost k usnadnění opakovaného použití kódu.</span><span class="sxs-lookup"><span data-stu-id="998bd-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="998bd-107">V tomto kurzu změníte `Instructor` a `Student` třídy tak, `Person` aby byly odvozeny `LastName` ze základní třídy, která obsahuje vlastnosti, jako jsou společné pro instruktory i studenty.</span><span class="sxs-lookup"><span data-stu-id="998bd-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="998bd-108">Nebudete přidávat ani měnit žádné webové stránky, ale změníte některé kódy a tyto změny se automaticky projeví v databázi.</span><span class="sxs-lookup"><span data-stu-id="998bd-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="998bd-109">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="998bd-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="998bd-110">Mapování dědičnosti do databáze</span><span class="sxs-lookup"><span data-stu-id="998bd-110">Map inheritance to database</span></span>
> * <span data-ttu-id="998bd-111">Vytvoření třídy Osoba</span><span class="sxs-lookup"><span data-stu-id="998bd-111">Create the Person class</span></span>
> * <span data-ttu-id="998bd-112">Aktualizovat instruktora a studenta</span><span class="sxs-lookup"><span data-stu-id="998bd-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="998bd-113">Přidat osobu do modelu</span><span class="sxs-lookup"><span data-stu-id="998bd-113">Add Person to the model</span></span>
> * <span data-ttu-id="998bd-114">Vytváření a aktualizace migrací</span><span class="sxs-lookup"><span data-stu-id="998bd-114">Create and update migrations</span></span>
> * <span data-ttu-id="998bd-115">Otestujte implementaci</span><span class="sxs-lookup"><span data-stu-id="998bd-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="998bd-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="998bd-116">Prerequisites</span></span>

* [<span data-ttu-id="998bd-117">Souběžnost popisovače</span><span class="sxs-lookup"><span data-stu-id="998bd-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="998bd-118">Mapování dědičnosti do databáze</span><span class="sxs-lookup"><span data-stu-id="998bd-118">Map inheritance to database</span></span>

<span data-ttu-id="998bd-119">A `Instructor` `Student` třídy v datovém modelu škola mají několik vlastností, které jsou identické:</span><span class="sxs-lookup"><span data-stu-id="998bd-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Třídy studentů a instruktorů](inheritance/_static/no-inheritance.png)

<span data-ttu-id="998bd-121">Předpokládejme, že chcete odstranit redundantní kód pro `Instructor` `Student` vlastnosti, které jsou sdíleny a entity.</span><span class="sxs-lookup"><span data-stu-id="998bd-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="998bd-122">Nebo chcete napsat službu, která může formátovat názvy bez ohledu na to, zda jméno pochází od instruktora nebo studenta.</span><span class="sxs-lookup"><span data-stu-id="998bd-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="998bd-123">Můžete vytvořit `Person` základní třídu, která obsahuje pouze `Instructor` tyto `Student` sdílené vlastnosti, pak provést a třídy dědí z této základní třídy, jak je znázorněno na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="998bd-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Třídy studentů a instruktorů odvozené z třídy Osoba](inheritance/_static/inheritance.png)

<span data-ttu-id="998bd-125">Existuje několik způsobů, jak tato struktura dědičnosti může být reprezentována v databázi.</span><span class="sxs-lookup"><span data-stu-id="998bd-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="998bd-126">V jedné tabulce můžete mít tabulku Osoba, která obsahuje informace o studentech i instruktorech.</span><span class="sxs-lookup"><span data-stu-id="998bd-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="998bd-127">Některé sloupce se mohou vztahovat pouze na instruktory (HireDate), některé pouze pro studenty (EnrollmentDate), některé na oba (LastName, FirstName).</span><span class="sxs-lookup"><span data-stu-id="998bd-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="998bd-128">Obvykle byste měli diskriminátor sloupec označující, který typ každý řádek představuje.</span><span class="sxs-lookup"><span data-stu-id="998bd-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="998bd-129">Například diskriminátor sloupec může mít "Instruktor" pro instruktory a "Student" pro studenty.</span><span class="sxs-lookup"><span data-stu-id="998bd-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Příklad tabulky na hierarchii](inheritance/_static/tph.png)

<span data-ttu-id="998bd-131">Tento vzor generování struktury dědičnosti entity z jedné databázové tabulky se nazývá dědičnost tabulky na hierarchii (TPH).</span><span class="sxs-lookup"><span data-stu-id="998bd-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="998bd-132">Alternativou je, aby databáze vypadat spíše jako strukturu dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="998bd-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="998bd-133">Můžete mít například pouze pole název v tabulce Osoba a samostatné tabulky Instruktor a Student s poli data.</span><span class="sxs-lookup"><span data-stu-id="998bd-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Dědičnost tabulky na typ](inheritance/_static/tpt.png)

<span data-ttu-id="998bd-135">Tento vzor vytváření databázové tabulky pro každou třídu entity se nazývá dědičnost tabulky podle typu (TPT).</span><span class="sxs-lookup"><span data-stu-id="998bd-135">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="998bd-136">Další možností je mapování všech neabstraktních typů na jednotlivé tabulky.</span><span class="sxs-lookup"><span data-stu-id="998bd-136">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="998bd-137">Všechny vlastnosti třídy, včetně zděděných vlastností, mapovat na sloupce odpovídající tabulky.</span><span class="sxs-lookup"><span data-stu-id="998bd-137">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="998bd-138">Tento vzor se nazývá Table-per-Concrete Class (TPC) dědičnost.</span><span class="sxs-lookup"><span data-stu-id="998bd-138">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="998bd-139">Pokud jste implementovali dědičnost TPC pro třídy Osoba, Student a Instruktor, jak je uvedeno výše, tabulky Student a Instruktor by po implementaci dědičnosti nevypadaly jinak než dříve.</span><span class="sxs-lookup"><span data-stu-id="998bd-139">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="998bd-140">Vzory dědičnosti TPC a TPH obecně poskytují lepší výkon než vzory dědičnosti TPT, protože vzory TPT mohou mít za následek složité dotazy spojení.</span><span class="sxs-lookup"><span data-stu-id="998bd-140">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="998bd-141">Tento kurz ukazuje, jak implementovat dědičnost TPH.</span><span class="sxs-lookup"><span data-stu-id="998bd-141">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="998bd-142">TPH je jediný způsob dědičnosti, který podporuje jádro entity frameworku.</span><span class="sxs-lookup"><span data-stu-id="998bd-142">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="998bd-143">Co uděláte, je `Person` vytvořit třídu, změnit třídy `Instructor` a `Student` odvozené z `Person`, přidat novou třídu do `DbContext`, a vytvořit migraci.</span><span class="sxs-lookup"><span data-stu-id="998bd-143">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="998bd-144">Před provedením následujících změn zvažte uložení kopie projektu.</span><span class="sxs-lookup"><span data-stu-id="998bd-144">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="998bd-145">Pak, pokud narazíte na problémy a je třeba začít znovu, bude snazší začít z uloženého projektu namísto obrácení kroků provedených pro tento kurz nebo návratu na začátek celé řady.</span><span class="sxs-lookup"><span data-stu-id="998bd-145">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="998bd-146">Vytvoření třídy Osoba</span><span class="sxs-lookup"><span data-stu-id="998bd-146">Create the Person class</span></span>

<span data-ttu-id="998bd-147">Ve složce Modely vytvořte Person.cs a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="998bd-147">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="998bd-148">Aktualizovat instruktora a studenta</span><span class="sxs-lookup"><span data-stu-id="998bd-148">Update Instructor and Student</span></span>

<span data-ttu-id="998bd-149">V *Instructor.cs*odvodit třídu Instruktor z třídy Osoba a odebrat pole klíče a názvu.</span><span class="sxs-lookup"><span data-stu-id="998bd-149">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="998bd-150">Kód bude vypadat jako následující příklad:</span><span class="sxs-lookup"><span data-stu-id="998bd-150">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="998bd-151">Proveďte stejné změny v *Student.cs*.</span><span class="sxs-lookup"><span data-stu-id="998bd-151">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="998bd-152">Přidat osobu do modelu</span><span class="sxs-lookup"><span data-stu-id="998bd-152">Add Person to the model</span></span>

<span data-ttu-id="998bd-153">Přidejte typ entity Osoba do *SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="998bd-153">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="998bd-154">Nové řádky jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="998bd-154">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="998bd-155">To je vše, co rozhraní entity framework potřebuje ke konfiguraci dědičnosti tabulky na hierarchii.</span><span class="sxs-lookup"><span data-stu-id="998bd-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="998bd-156">Jak uvidíte, při aktualizaci databáze bude mít místo tabulek studenta a instruktora tabulku osoba.</span><span class="sxs-lookup"><span data-stu-id="998bd-156">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="998bd-157">Vytváření a aktualizace migrací</span><span class="sxs-lookup"><span data-stu-id="998bd-157">Create and update migrations</span></span>

<span data-ttu-id="998bd-158">Uložte změny a vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="998bd-158">Save your changes and build the project.</span></span> <span data-ttu-id="998bd-159">Potom otevřete příkazové okno ve složce projektu a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="998bd-159">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="998bd-160">Ještě nespouštějte `database update` příkaz.</span><span class="sxs-lookup"><span data-stu-id="998bd-160">Don't run the `database update` command yet.</span></span> <span data-ttu-id="998bd-161">Tento příkaz bude mít za následek ztrátu dat, protože klesne instruktor tabulky a přejmenovat studenta tabulka na osobu.</span><span class="sxs-lookup"><span data-stu-id="998bd-161">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="998bd-162">Chcete-li zachovat existující data, je třeba zadat vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="998bd-162">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="998bd-163">Otevřete *migrace/\<časové razítko>_Inheritance.cs* a nahraďte metodu `Up` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="998bd-163">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="998bd-164">Tento kód se postará o následující úlohy aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="998bd-164">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="998bd-165">Odebere omezení cizího klíče a indexy, které odkazují na tabulku Student.</span><span class="sxs-lookup"><span data-stu-id="998bd-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="998bd-166">Přejmenuje tabulku Instruktor jako osoba a provede změny potřebné k ukládání dat studentů:</span><span class="sxs-lookup"><span data-stu-id="998bd-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="998bd-167">Přidá datum, které lze utnou tzv.</span><span class="sxs-lookup"><span data-stu-id="998bd-167">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="998bd-168">Přidá sloupec Diskriminátor označující, zda je řádek pro studenta nebo instruktora.</span><span class="sxs-lookup"><span data-stu-id="998bd-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="998bd-169">Umožňuje hireDate nullable protože řádky studenta nebude mít data přijetí.</span><span class="sxs-lookup"><span data-stu-id="998bd-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="998bd-170">Přidá dočasné pole, které bude použito k aktualizaci cizích klíčů, které odkazují na studenty.</span><span class="sxs-lookup"><span data-stu-id="998bd-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="998bd-171">Když studenty zkopírujete do tabulky Osoba, získají nové hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="998bd-171">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="998bd-172">Zkopíruje data z tabulky Student do tabulky Osoba.</span><span class="sxs-lookup"><span data-stu-id="998bd-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="998bd-173">To způsobí, že studentům budou přiřazeny nové hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="998bd-173">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="998bd-174">Opravuje hodnoty cizího klíče, které odkazují na studenty.</span><span class="sxs-lookup"><span data-stu-id="998bd-174">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="998bd-175">Znovu vytvoří omezení cizího klíče a indexy, nyní s měnou na osobu tabulky.</span><span class="sxs-lookup"><span data-stu-id="998bd-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="998bd-176">(Pokud jste jako typ primárního klíče použili identifikátor GUID místo celého čísla, hodnoty primárního klíče studenta by se nemusely měnit a několik těchto kroků mohlo být vynecháno.)</span><span class="sxs-lookup"><span data-stu-id="998bd-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="998bd-177">Spusťte `database update` příkaz:</span><span class="sxs-lookup"><span data-stu-id="998bd-177">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="998bd-178">(V produkčním systému byste provedli odpovídající změny `Down` metody v případě, že byste ji někdy museli použít k návratu k předchozí verzi databáze.</span><span class="sxs-lookup"><span data-stu-id="998bd-178">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="998bd-179">Pro tento kurz nebudete používat `Down` metodu.)</span><span class="sxs-lookup"><span data-stu-id="998bd-179">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="998bd-180">Je možné získat další chyby při provádění změn schématu v databázi, která má existující data.</span><span class="sxs-lookup"><span data-stu-id="998bd-180">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="998bd-181">Pokud se vám budou zobrazovat chyby migrace, které nelze vyřešit, můžete změnit název databáze v připojovacím řetězci nebo databázi odstranit.</span><span class="sxs-lookup"><span data-stu-id="998bd-181">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="998bd-182">S novou databází nejsou k dispozici žádná data k migraci a příkaz aktualizovat databázi je pravděpodobnější, že bude dokončen bez chyb.</span><span class="sxs-lookup"><span data-stu-id="998bd-182">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="998bd-183">Chcete-li databázi odstranit, použijte `database drop` příkaz SSOX nebo spusťte příkaz CLI.</span><span class="sxs-lookup"><span data-stu-id="998bd-183">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="998bd-184">Otestujte implementaci</span><span class="sxs-lookup"><span data-stu-id="998bd-184">Test the implementation</span></span>

<span data-ttu-id="998bd-185">Spusťte aplikaci a vyzkoušejte různé stránky.</span><span class="sxs-lookup"><span data-stu-id="998bd-185">Run the app and try various pages.</span></span> <span data-ttu-id="998bd-186">Všechno funguje stejně jako předtím.</span><span class="sxs-lookup"><span data-stu-id="998bd-186">Everything works the same as it did before.</span></span>

<span data-ttu-id="998bd-187">V **Průzkumníku objektů SQL Server**rozbalte **položku Data Connections/SchoolContext** a potom **tabulky**a uvidíte, že tabulky Student a Instruktor byly nahrazeny tabulkou Osoba.</span><span class="sxs-lookup"><span data-stu-id="998bd-187">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="998bd-188">Otevřete návrháře tabulek Osoba a uvidíte, že obsahuje všechny sloupce, které bývaly v tabulkách Student a Instruktor.</span><span class="sxs-lookup"><span data-stu-id="998bd-188">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Osobní stůl ve SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="998bd-190">Klikněte pravým tlačítkem myši na tabulku Osoba a potom kliknutím na **Zobrazit data tabulky** zobrazte sloupec diskriminátoru.</span><span class="sxs-lookup"><span data-stu-id="998bd-190">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Tabulka osob ve SSOX - tabulkové údaje](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="998bd-192">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="998bd-192">Get the code</span></span>

[<span data-ttu-id="998bd-193">Stáhněte nebo zobrazte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="998bd-193">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="998bd-194">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="998bd-194">Additional resources</span></span>

<span data-ttu-id="998bd-195">Další informace o dědičnosti v jádru entity frameworku naleznete v [tématu Dědičnost](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="998bd-195">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="998bd-196">Další kroky</span><span class="sxs-lookup"><span data-stu-id="998bd-196">Next steps</span></span>

<span data-ttu-id="998bd-197">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="998bd-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="998bd-198">Mapovaná dědičnost do databáze</span><span class="sxs-lookup"><span data-stu-id="998bd-198">Mapped inheritance to database</span></span>
> * <span data-ttu-id="998bd-199">Vytvoření třídy Osoba</span><span class="sxs-lookup"><span data-stu-id="998bd-199">Created the Person class</span></span>
> * <span data-ttu-id="998bd-200">Aktualizovaný instruktor a student</span><span class="sxs-lookup"><span data-stu-id="998bd-200">Updated Instructor and Student</span></span>
> * <span data-ttu-id="998bd-201">Přidána osoba do modelu</span><span class="sxs-lookup"><span data-stu-id="998bd-201">Added Person to the model</span></span>
> * <span data-ttu-id="998bd-202">Vytvořené a aktualizovat migrace</span><span class="sxs-lookup"><span data-stu-id="998bd-202">Created and update migrations</span></span>
> * <span data-ttu-id="998bd-203">Testováno provádění</span><span class="sxs-lookup"><span data-stu-id="998bd-203">Tested the implementation</span></span>

<span data-ttu-id="998bd-204">Přejdete k dalšímu kurzu, kde se dozvíte, jak zpracovat různé relativně pokročilé scénáře entity frameworku.</span><span class="sxs-lookup"><span data-stu-id="998bd-204">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="998bd-205">Další: Pokročilá témata</span><span class="sxs-lookup"><span data-stu-id="998bd-205">Next: Advanced topics</span></span>](advanced.md)
