---
title: 'Kurz: Implementace dědičnosti – ASP.NET MVC pomocí EF Core'
description: Tento kurz vám ukáže, jak implementovat dědičnost v datovém modelu pomocí Entity Framework Core v ASP.NET Core aplikaci.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/inheritance
ms.openlocfilehash: b57adeb2712eb58907b53809cbd938e8ec0c1f6f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012836"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="81e08-103">Kurz: Implementace dědičnosti – ASP.NET MVC pomocí EF Core</span><span class="sxs-lookup"><span data-stu-id="81e08-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="81e08-104">V předchozím kurzu jste zpracovali výjimky souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="81e08-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="81e08-105">Tento kurz vám ukáže, jak implementovat dědičnost v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="81e08-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="81e08-106">V objektově orientovaném programování můžete použít dědičnost k usnadnění opětovného použití kódu.</span><span class="sxs-lookup"><span data-stu-id="81e08-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="81e08-107">V tomto kurzu změníte `Instructor` `Student` třídy a tak, aby byly odvozeny ze `Person` základní třídy, která obsahuje vlastnosti `LastName` , jako jsou společné pro instruktory i studenty.</span><span class="sxs-lookup"><span data-stu-id="81e08-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="81e08-108">Nepřidáte ani neměníte žádné webové stránky, ale změníte část kódu a tyto změny se automaticky projeví v databázi.</span><span class="sxs-lookup"><span data-stu-id="81e08-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="81e08-109">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="81e08-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="81e08-110">Mapování dědičnosti na databázi</span><span class="sxs-lookup"><span data-stu-id="81e08-110">Map inheritance to database</span></span>
> * <span data-ttu-id="81e08-111">Vytvoření třídy Person</span><span class="sxs-lookup"><span data-stu-id="81e08-111">Create the Person class</span></span>
> * <span data-ttu-id="81e08-112">Aktualizace instruktora a studenta</span><span class="sxs-lookup"><span data-stu-id="81e08-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="81e08-113">Přidat osobu do modelu</span><span class="sxs-lookup"><span data-stu-id="81e08-113">Add Person to the model</span></span>
> * <span data-ttu-id="81e08-114">Vytváření a aktualizace migrací</span><span class="sxs-lookup"><span data-stu-id="81e08-114">Create and update migrations</span></span>
> * <span data-ttu-id="81e08-115">Testování implementace</span><span class="sxs-lookup"><span data-stu-id="81e08-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="81e08-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="81e08-116">Prerequisites</span></span>

* [<span data-ttu-id="81e08-117">Souběžnost popisovačů</span><span class="sxs-lookup"><span data-stu-id="81e08-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="81e08-118">Mapování dědičnosti na databázi</span><span class="sxs-lookup"><span data-stu-id="81e08-118">Map inheritance to database</span></span>

<span data-ttu-id="81e08-119">`Instructor`Třídy a `Student` v modelu školních dat obsahují několik vlastností, které jsou identické:</span><span class="sxs-lookup"><span data-stu-id="81e08-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Třídy student a instruktor](inheritance/_static/no-inheritance.png)

<span data-ttu-id="81e08-121">Předpokládejme, že chcete eliminovat redundantní kód pro vlastnosti, které jsou sdíleny `Instructor` `Student` entitami a.</span><span class="sxs-lookup"><span data-stu-id="81e08-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="81e08-122">Nebo chcete napsat službu, která může formátovat názvy bez caring, jestli název pochází od instruktora nebo studenta.</span><span class="sxs-lookup"><span data-stu-id="81e08-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="81e08-123">Můžete vytvořit `Person` základní třídu, která obsahuje pouze tyto sdílené vlastnosti, a poté nastavit `Instructor` třídy a `Student` dědění z této základní třídy, jak je znázorněno na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="81e08-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Třídy studenta a instruktory odvozené od třídy Person](inheritance/_static/inheritance.png)

<span data-ttu-id="81e08-125">Existuje několik způsobů, jak lze tuto strukturu dědičnosti znázornit v databázi.</span><span class="sxs-lookup"><span data-stu-id="81e08-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="81e08-126">Můžete mít tabulku Person, která obsahuje informace o studentech i instruktorech v jedné tabulce.</span><span class="sxs-lookup"><span data-stu-id="81e08-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="81e08-127">Některé sloupce mohou být použity pouze pro instruktory (ZaměstnánOd), některé pouze pro studenty (EnrollmentDate), některá pro obě (LastName, FirstName).</span><span class="sxs-lookup"><span data-stu-id="81e08-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="81e08-128">Obvykle byste měli sloupec diskriminátoru, který určuje, který typ každý řádek představuje.</span><span class="sxs-lookup"><span data-stu-id="81e08-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="81e08-129">Sloupec diskriminátoru může mít například "instruktor" pro studenty a studenta.</span><span class="sxs-lookup"><span data-stu-id="81e08-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Příklad tabulky na hierarchii](inheritance/_static/tph.png)

<span data-ttu-id="81e08-131">Tento model generování struktury dědičnosti entit z jedné databázové tabulky se nazývá dědičnost typu tabulka-na hierarchii (TPH).</span><span class="sxs-lookup"><span data-stu-id="81e08-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="81e08-132">Alternativou je, že databáze vypadá podobně jako struktura dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="81e08-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="81e08-133">Například můžete mít pouze pole název v tabulce Person a mají samostatné tabulky instruktor a student s poli data.</span><span class="sxs-lookup"><span data-stu-id="81e08-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Dědičnost tabulek podle typu](inheritance/_static/tpt.png)

<span data-ttu-id="81e08-135">Tento model vytvoření tabulky databáze pro každou třídu entity se nazývá dědičnost tabulky podle typu (TPT).</span><span class="sxs-lookup"><span data-stu-id="81e08-135">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="81e08-136">Ještě další možností je mapovat všechny neabstraktní typy na jednotlivé tabulky.</span><span class="sxs-lookup"><span data-stu-id="81e08-136">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="81e08-137">Všechny vlastnosti třídy, včetně děděných vlastností, jsou mapovány na sloupce odpovídající tabulky.</span><span class="sxs-lookup"><span data-stu-id="81e08-137">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="81e08-138">Tento model se nazývá dědičnost tříd (TPC) podle konkrétní třídy.</span><span class="sxs-lookup"><span data-stu-id="81e08-138">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="81e08-139">Pokud jste implementovali TPC dědění pro třídy Person, student a instruktor, jak je uvedeno výše, tabulky student a instruktor by po implementaci dědění nevypadaly jinak než předtím.</span><span class="sxs-lookup"><span data-stu-id="81e08-139">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="81e08-140">Vzorce dědičnosti TPC a TPH obvykle poskytují lepší výkon než vzory dědičnosti TPT, protože vzory TPT mohou mít za následek složité spojení dotazů.</span><span class="sxs-lookup"><span data-stu-id="81e08-140">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="81e08-141">Tento kurz ukazuje, jak implementovat dědičnosti TPH.</span><span class="sxs-lookup"><span data-stu-id="81e08-141">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="81e08-142">TPH je jediný vzorek dědičnosti, který Entity Framework Core podporuje.</span><span class="sxs-lookup"><span data-stu-id="81e08-142">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="81e08-143">To, co uděláte, je vytvořit `Person` třídu, změnit `Instructor` třídy a, `Student` které se mají odvodit z `Person` , přidat novou třídu do a `DbContext` vytvořit migraci.</span><span class="sxs-lookup"><span data-stu-id="81e08-143">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="81e08-144">Zvažte uložení kopie projektu před provedením následujících změn.</span><span class="sxs-lookup"><span data-stu-id="81e08-144">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="81e08-145">Pak Pokud narazíte na problémy a potřebujete začít znovu, bude snazší začít z uloženého projektu místo vrácení kroků provedených pro tento kurz nebo přechod zpět na začátek celé řady.</span><span class="sxs-lookup"><span data-stu-id="81e08-145">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="81e08-146">Vytvoření třídy Person</span><span class="sxs-lookup"><span data-stu-id="81e08-146">Create the Person class</span></span>

<span data-ttu-id="81e08-147">Ve složce modely vytvořte Person.cs a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="81e08-147">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="81e08-148">Aktualizace instruktora a studenta</span><span class="sxs-lookup"><span data-stu-id="81e08-148">Update Instructor and Student</span></span>

<span data-ttu-id="81e08-149">V *Instructor.cs*odvodíte třídu Instructor z třídy Person a odstraňte pole klíč a název.</span><span class="sxs-lookup"><span data-stu-id="81e08-149">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="81e08-150">Kód bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="81e08-150">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="81e08-151">Udělejte stejné změny v *student.cs*.</span><span class="sxs-lookup"><span data-stu-id="81e08-151">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="81e08-152">Přidat osobu do modelu</span><span class="sxs-lookup"><span data-stu-id="81e08-152">Add Person to the model</span></span>

<span data-ttu-id="81e08-153">Do *SchoolContext.cs*přidejte typ entity Person.</span><span class="sxs-lookup"><span data-stu-id="81e08-153">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="81e08-154">Nové řádky jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="81e08-154">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="81e08-155">To je vše, co Entity Framework potřebuje, aby bylo možné nakonfigurovat dědičnost tabulek na hierarchii.</span><span class="sxs-lookup"><span data-stu-id="81e08-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="81e08-156">Jak vidíte, při aktualizaci databáze bude mít uživatelskou tabulku místo tabulek student a instruktor.</span><span class="sxs-lookup"><span data-stu-id="81e08-156">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="81e08-157">Vytváření a aktualizace migrací</span><span class="sxs-lookup"><span data-stu-id="81e08-157">Create and update migrations</span></span>

<span data-ttu-id="81e08-158">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="81e08-158">Save your changes and build the project.</span></span> <span data-ttu-id="81e08-159">Pak otevřete okno příkazového řádku ve složce projektu a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="81e08-159">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="81e08-160">Tento `database update` příkaz ještě nespouštějte.</span><span class="sxs-lookup"><span data-stu-id="81e08-160">Don't run the `database update` command yet.</span></span> <span data-ttu-id="81e08-161">Tento příkaz bude mít za následek ztrátu dat, protože odstraní tabulku instruktora a přejmenuje tabulku student na Person.</span><span class="sxs-lookup"><span data-stu-id="81e08-161">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="81e08-162">Aby bylo možné zachovat existující data, je třeba zadat vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="81e08-162">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="81e08-163">Otevřete *migrace/ \<timestamp> _Inheritance. cs* a nahraďte `Up` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="81e08-163">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="81e08-164">Tento kód má na starosti následující úlohy aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="81e08-164">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="81e08-165">Odebere omezení a indexy cizího klíče, které odkazují na tabulku studenta.</span><span class="sxs-lookup"><span data-stu-id="81e08-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="81e08-166">Přejmenuje tabulku instruktora jako osobu a provede změny potřebné k uložení dat studenta:</span><span class="sxs-lookup"><span data-stu-id="81e08-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="81e08-167">Přidá EnrollmentDate s možnou hodnotou null pro studenty.</span><span class="sxs-lookup"><span data-stu-id="81e08-167">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="81e08-168">Přidá sloupec diskriminátoru, který označuje, zda je řádek určen studentem nebo instruktorem.</span><span class="sxs-lookup"><span data-stu-id="81e08-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="81e08-169">Vytvoří hodnotu Nullable s možnou hodnotou null, protože řádky studenta nebudou mít data přijetí.</span><span class="sxs-lookup"><span data-stu-id="81e08-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="81e08-170">Přidá dočasné pole, které bude použito k aktualizaci cizích klíčů, které odkazují na studenty.</span><span class="sxs-lookup"><span data-stu-id="81e08-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="81e08-171">Když zkopírujete studenty do tabulky Person, získají se nové hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="81e08-171">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="81e08-172">Zkopíruje data z tabulky student do tabulky Person (osoba).</span><span class="sxs-lookup"><span data-stu-id="81e08-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="81e08-173">To způsobí, že studenti získají přiřazené nové hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="81e08-173">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="81e08-174">Opravuje hodnoty cizích klíčů, které odkazují na studenty.</span><span class="sxs-lookup"><span data-stu-id="81e08-174">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="81e08-175">Znovu vytvoří omezení a indexy cizího klíče, které se teď odkazují na tabulku Person.</span><span class="sxs-lookup"><span data-stu-id="81e08-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="81e08-176">(Pokud jste použili GUID místo celého čísla jako typ primárního klíče, hodnoty primárního klíče studenta se nemusejí změnit a některé z těchto kroků by mohly být vynechány.)</span><span class="sxs-lookup"><span data-stu-id="81e08-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="81e08-177">Spusťte `database update` příkaz:</span><span class="sxs-lookup"><span data-stu-id="81e08-177">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="81e08-178">(V produkčním systému provedete odpovídající změny `Down` metody v případě, že byste to museli použít pro návrat k předchozí verzi databáze.</span><span class="sxs-lookup"><span data-stu-id="81e08-178">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="81e08-179">Pro tento kurz nebudete používat `Down` metodu.)</span><span class="sxs-lookup"><span data-stu-id="81e08-179">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="81e08-180">Při provádění změn schématu v databázi, která obsahuje existující data, je možné získat další chyby.</span><span class="sxs-lookup"><span data-stu-id="81e08-180">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="81e08-181">Pokud získáte chyby migrace, které nelze vyřešit, můžete buď změnit název databáze v připojovacím řetězci nebo odstranit databázi.</span><span class="sxs-lookup"><span data-stu-id="81e08-181">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="81e08-182">V případě nové databáze není k dispozici žádná data k migraci a příkaz Update-Database je pravděpodobnější, že se dokončí bez chyb.</span><span class="sxs-lookup"><span data-stu-id="81e08-182">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="81e08-183">Databázi odstraníte tak, že použijete SSOX nebo spustíte `database drop` příkaz CLI.</span><span class="sxs-lookup"><span data-stu-id="81e08-183">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="81e08-184">Testování implementace</span><span class="sxs-lookup"><span data-stu-id="81e08-184">Test the implementation</span></span>

<span data-ttu-id="81e08-185">Spusťte aplikaci a vyzkoušejte si různé stránky.</span><span class="sxs-lookup"><span data-stu-id="81e08-185">Run the app and try various pages.</span></span> <span data-ttu-id="81e08-186">Vše funguje stejně jako dříve.</span><span class="sxs-lookup"><span data-stu-id="81e08-186">Everything works the same as it did before.</span></span>

<span data-ttu-id="81e08-187">V **Průzkumník objektů systému SQL Server**rozbalte **data připojení/SchoolContext** a pak **tabulky**a uvidíte, že tabulky student a instruktor byly nahrazeny tabulkou Person.</span><span class="sxs-lookup"><span data-stu-id="81e08-187">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="81e08-188">Otevřete návrháře tabulky osoba a uvidíte, že obsahuje všechny sloupce, které se používají v tabulkách student a instruktor.</span><span class="sxs-lookup"><span data-stu-id="81e08-188">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Tabulka Person v SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="81e08-190">Klikněte pravým tlačítkem myši na tabulku Person a potom kliknutím na možnost **Zobrazit data tabulky** zobrazte sloupec diskriminátor.</span><span class="sxs-lookup"><span data-stu-id="81e08-190">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Tabulka Person v tabulce SSOX data](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="81e08-192">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="81e08-192">Get the code</span></span>

[<span data-ttu-id="81e08-193">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="81e08-193">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="81e08-194">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="81e08-194">Additional resources</span></span>

<span data-ttu-id="81e08-195">Další informace o dědičnosti v Entity Framework Core naleznete v tématu [Dědičnost](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="81e08-195">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="81e08-196">Další kroky</span><span class="sxs-lookup"><span data-stu-id="81e08-196">Next steps</span></span>

<span data-ttu-id="81e08-197">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="81e08-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="81e08-198">Namapovaná dědičnost na databázi</span><span class="sxs-lookup"><span data-stu-id="81e08-198">Mapped inheritance to database</span></span>
> * <span data-ttu-id="81e08-199">Byla vytvořena třída Person.</span><span class="sxs-lookup"><span data-stu-id="81e08-199">Created the Person class</span></span>
> * <span data-ttu-id="81e08-200">Aktualizovaný instruktor a student</span><span class="sxs-lookup"><span data-stu-id="81e08-200">Updated Instructor and Student</span></span>
> * <span data-ttu-id="81e08-201">Do modelu se přidala osoba.</span><span class="sxs-lookup"><span data-stu-id="81e08-201">Added Person to the model</span></span>
> * <span data-ttu-id="81e08-202">Vytvořené a aktualizované migrace</span><span class="sxs-lookup"><span data-stu-id="81e08-202">Created and update migrations</span></span>
> * <span data-ttu-id="81e08-203">Otestování implementace</span><span class="sxs-lookup"><span data-stu-id="81e08-203">Tested the implementation</span></span>

<span data-ttu-id="81e08-204">Přejděte k dalšímu kurzu, kde se dozvíte, jak zvládnout celou řadu poměrně pokročilých scénářů Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="81e08-204">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="81e08-205">Další: Pokročilá témata</span><span class="sxs-lookup"><span data-stu-id="81e08-205">Next: Advanced topics</span></span>](advanced.md)
