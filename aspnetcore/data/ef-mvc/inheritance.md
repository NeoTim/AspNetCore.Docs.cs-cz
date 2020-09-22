---
title: 'Kurz: Implementace dědičnosti – ASP.NET MVC pomocí EF Core'
description: Tento kurz vám ukáže, jak implementovat dědičnost v datovém modelu pomocí Entity Framework Core v ASP.NET Core aplikaci.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: 34c4ea28103b9eb12601b383a46091084391f120
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847595"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="dea89-103">Kurz: Implementace dědičnosti – ASP.NET MVC pomocí EF Core</span><span class="sxs-lookup"><span data-stu-id="dea89-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="dea89-104">V předchozím kurzu jste zpracovali výjimky souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="dea89-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="dea89-105">Tento kurz vám ukáže, jak implementovat dědičnost v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="dea89-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="dea89-106">V objektově orientovaném programování můžete použít dědičnost k usnadnění opětovného použití kódu.</span><span class="sxs-lookup"><span data-stu-id="dea89-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="dea89-107">V tomto kurzu změníte `Instructor` `Student` třídy a tak, aby byly odvozeny ze `Person` základní třídy, která obsahuje vlastnosti `LastName` , jako jsou společné pro instruktory i studenty.</span><span class="sxs-lookup"><span data-stu-id="dea89-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="dea89-108">Nepřidáte ani neměníte žádné webové stránky, ale změníte část kódu a tyto změny se automaticky projeví v databázi.</span><span class="sxs-lookup"><span data-stu-id="dea89-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="dea89-109">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="dea89-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dea89-110">Mapování dědičnosti na databázi</span><span class="sxs-lookup"><span data-stu-id="dea89-110">Map inheritance to database</span></span>
> * <span data-ttu-id="dea89-111">Vytvoření třídy Person</span><span class="sxs-lookup"><span data-stu-id="dea89-111">Create the Person class</span></span>
> * <span data-ttu-id="dea89-112">Aktualizace instruktora a studenta</span><span class="sxs-lookup"><span data-stu-id="dea89-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="dea89-113">Přidat osobu do modelu</span><span class="sxs-lookup"><span data-stu-id="dea89-113">Add Person to the model</span></span>
> * <span data-ttu-id="dea89-114">Vytváření a aktualizace migrací</span><span class="sxs-lookup"><span data-stu-id="dea89-114">Create and update migrations</span></span>
> * <span data-ttu-id="dea89-115">Testování implementace</span><span class="sxs-lookup"><span data-stu-id="dea89-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dea89-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="dea89-116">Prerequisites</span></span>

* [<span data-ttu-id="dea89-117">Souběžnost popisovačů</span><span class="sxs-lookup"><span data-stu-id="dea89-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="dea89-118">Mapování dědičnosti na databázi</span><span class="sxs-lookup"><span data-stu-id="dea89-118">Map inheritance to database</span></span>

<span data-ttu-id="dea89-119">`Instructor`Třídy a `Student` v modelu školních dat obsahují několik vlastností, které jsou identické:</span><span class="sxs-lookup"><span data-stu-id="dea89-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Třídy student a instruktor](inheritance/_static/no-inheritance.png)

<span data-ttu-id="dea89-121">Předpokládejme, že chcete eliminovat redundantní kód pro vlastnosti, které jsou sdíleny `Instructor` `Student` entitami a.</span><span class="sxs-lookup"><span data-stu-id="dea89-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="dea89-122">Nebo chcete napsat službu, která může formátovat názvy bez caring, jestli název pochází od instruktora nebo studenta.</span><span class="sxs-lookup"><span data-stu-id="dea89-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="dea89-123">Můžete vytvořit `Person` základní třídu, která obsahuje pouze tyto sdílené vlastnosti, a poté nastavit `Instructor` třídy a `Student` dědění z této základní třídy, jak je znázorněno na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="dea89-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Třídy studenta a instruktory odvozené od třídy Person](inheritance/_static/inheritance.png)

<span data-ttu-id="dea89-125">Existuje několik způsobů, jak lze tuto strukturu dědičnosti znázornit v databázi.</span><span class="sxs-lookup"><span data-stu-id="dea89-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="dea89-126">Můžete mít tabulku Person, která obsahuje informace o studentech i instruktorech v jedné tabulce.</span><span class="sxs-lookup"><span data-stu-id="dea89-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="dea89-127">Některé sloupce mohou být použity pouze pro instruktory (ZaměstnánOd), některé pouze pro studenty (EnrollmentDate), některá pro obě (LastName, FirstName).</span><span class="sxs-lookup"><span data-stu-id="dea89-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="dea89-128">Obvykle byste měli sloupec diskriminátoru, který určuje, který typ každý řádek představuje.</span><span class="sxs-lookup"><span data-stu-id="dea89-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="dea89-129">Sloupec diskriminátoru může mít například "instruktor" pro studenty a studenta.</span><span class="sxs-lookup"><span data-stu-id="dea89-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Příklad tabulky na hierarchii](inheritance/_static/tph.png)

<span data-ttu-id="dea89-131">Tento model generování struktury dědičnosti entit z jedné databázové tabulky se nazývá dědičnost typu tabulka-na hierarchii (TPH).</span><span class="sxs-lookup"><span data-stu-id="dea89-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="dea89-132">Alternativou je, že databáze vypadá podobně jako struktura dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="dea89-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="dea89-133">Například můžete mít pouze pole název v tabulce Person a mají samostatné tabulky instruktor a student s poli data.</span><span class="sxs-lookup"><span data-stu-id="dea89-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

> [!WARNING]
> <span data-ttu-id="dea89-134">Tabulka na typ (TPT) není podporována EF Core 3. x, ale je implementována v [EF Core 5,0](https://docs.microsoft.com/ef/core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="dea89-134">Table Per Type (TPT) is not supported by EF Core 3.x, however it is has been implemented in [EF Core 5.0](https://docs.microsoft.com/ef/core/what-is-new/ef-core-5.0/plan).</span></span>

![Dědičnost tabulek podle typu](inheritance/_static/tpt.png)

<span data-ttu-id="dea89-136">Tento model vytvoření tabulky databáze pro každou třídu entity se nazývá dědičnost tabulky podle typu (TPT).</span><span class="sxs-lookup"><span data-stu-id="dea89-136">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="dea89-137">Ještě další možností je mapovat všechny neabstraktní typy na jednotlivé tabulky.</span><span class="sxs-lookup"><span data-stu-id="dea89-137">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="dea89-138">Všechny vlastnosti třídy, včetně děděných vlastností, jsou mapovány na sloupce odpovídající tabulky.</span><span class="sxs-lookup"><span data-stu-id="dea89-138">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="dea89-139">Tento model se nazývá dědičnost tříd (TPC) podle konkrétní třídy.</span><span class="sxs-lookup"><span data-stu-id="dea89-139">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="dea89-140">Pokud jste implementovali TPC dědění pro třídy Person, student a instruktor, jak je uvedeno výše, tabulky student a instruktor by po implementaci dědění nevypadaly jinak než předtím.</span><span class="sxs-lookup"><span data-stu-id="dea89-140">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="dea89-141">Vzorce dědičnosti TPC a TPH obvykle poskytují lepší výkon než vzory dědičnosti TPT, protože vzory TPT mohou mít za následek složité spojení dotazů.</span><span class="sxs-lookup"><span data-stu-id="dea89-141">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="dea89-142">Tento kurz ukazuje, jak implementovat dědičnosti TPH.</span><span class="sxs-lookup"><span data-stu-id="dea89-142">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="dea89-143">TPH je jediný vzorek dědičnosti, který Entity Framework Core podporuje.</span><span class="sxs-lookup"><span data-stu-id="dea89-143">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="dea89-144">To, co uděláte, je vytvořit `Person` třídu, změnit `Instructor` třídy a, `Student` které se mají odvodit z `Person` , přidat novou třídu do a `DbContext` vytvořit migraci.</span><span class="sxs-lookup"><span data-stu-id="dea89-144">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="dea89-145">Zvažte uložení kopie projektu před provedením následujících změn.</span><span class="sxs-lookup"><span data-stu-id="dea89-145">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="dea89-146">Pak Pokud narazíte na problémy a potřebujete začít znovu, bude snazší začít z uloženého projektu místo vrácení kroků provedených pro tento kurz nebo přechod zpět na začátek celé řady.</span><span class="sxs-lookup"><span data-stu-id="dea89-146">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="dea89-147">Vytvoření třídy Person</span><span class="sxs-lookup"><span data-stu-id="dea89-147">Create the Person class</span></span>

<span data-ttu-id="dea89-148">Ve složce modely vytvořte Person.cs a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="dea89-148">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="dea89-149">Aktualizace instruktora a studenta</span><span class="sxs-lookup"><span data-stu-id="dea89-149">Update Instructor and Student</span></span>

<span data-ttu-id="dea89-150">V *Instructor.cs*odvodíte třídu Instructor z třídy Person a odstraňte pole klíč a název.</span><span class="sxs-lookup"><span data-stu-id="dea89-150">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="dea89-151">Kód bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="dea89-151">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="dea89-152">Udělejte stejné změny v *student.cs*.</span><span class="sxs-lookup"><span data-stu-id="dea89-152">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="dea89-153">Přidat osobu do modelu</span><span class="sxs-lookup"><span data-stu-id="dea89-153">Add Person to the model</span></span>

<span data-ttu-id="dea89-154">Do *SchoolContext.cs*přidejte typ entity Person.</span><span class="sxs-lookup"><span data-stu-id="dea89-154">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="dea89-155">Nové řádky jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="dea89-155">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="dea89-156">To je vše, co Entity Framework potřebuje, aby bylo možné nakonfigurovat dědičnost tabulek na hierarchii.</span><span class="sxs-lookup"><span data-stu-id="dea89-156">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="dea89-157">Jak vidíte, při aktualizaci databáze bude mít uživatelskou tabulku místo tabulek student a instruktor.</span><span class="sxs-lookup"><span data-stu-id="dea89-157">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="dea89-158">Vytváření a aktualizace migrací</span><span class="sxs-lookup"><span data-stu-id="dea89-158">Create and update migrations</span></span>

<span data-ttu-id="dea89-159">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="dea89-159">Save your changes and build the project.</span></span> <span data-ttu-id="dea89-160">Pak otevřete okno příkazového řádku ve složce projektu a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="dea89-160">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="dea89-161">Tento `database update` příkaz ještě nespouštějte.</span><span class="sxs-lookup"><span data-stu-id="dea89-161">Don't run the `database update` command yet.</span></span> <span data-ttu-id="dea89-162">Tento příkaz bude mít za následek ztrátu dat, protože odstraní tabulku instruktora a přejmenuje tabulku student na Person.</span><span class="sxs-lookup"><span data-stu-id="dea89-162">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="dea89-163">Aby bylo možné zachovat existující data, je třeba zadat vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="dea89-163">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="dea89-164">Otevřete *migrace/ \<timestamp> _Inheritance. cs* a nahraďte `Up` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="dea89-164">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="dea89-165">Tento kód má na starosti následující úlohy aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="dea89-165">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="dea89-166">Odebere omezení a indexy cizího klíče, které odkazují na tabulku studenta.</span><span class="sxs-lookup"><span data-stu-id="dea89-166">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="dea89-167">Přejmenuje tabulku instruktora jako osobu a provede změny potřebné k uložení dat studenta:</span><span class="sxs-lookup"><span data-stu-id="dea89-167">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="dea89-168">Přidá EnrollmentDate s možnou hodnotou null pro studenty.</span><span class="sxs-lookup"><span data-stu-id="dea89-168">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="dea89-169">Přidá sloupec diskriminátoru, který označuje, zda je řádek určen studentem nebo instruktorem.</span><span class="sxs-lookup"><span data-stu-id="dea89-169">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="dea89-170">Vytvoří hodnotu Nullable s možnou hodnotou null, protože řádky studenta nebudou mít data přijetí.</span><span class="sxs-lookup"><span data-stu-id="dea89-170">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="dea89-171">Přidá dočasné pole, které bude použito k aktualizaci cizích klíčů, které odkazují na studenty.</span><span class="sxs-lookup"><span data-stu-id="dea89-171">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="dea89-172">Když zkopírujete studenty do tabulky Person, získají se nové hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="dea89-172">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="dea89-173">Zkopíruje data z tabulky student do tabulky Person (osoba).</span><span class="sxs-lookup"><span data-stu-id="dea89-173">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="dea89-174">To způsobí, že studenti získají přiřazené nové hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="dea89-174">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="dea89-175">Opravuje hodnoty cizích klíčů, které odkazují na studenty.</span><span class="sxs-lookup"><span data-stu-id="dea89-175">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="dea89-176">Znovu vytvoří omezení a indexy cizího klíče, které se teď odkazují na tabulku Person.</span><span class="sxs-lookup"><span data-stu-id="dea89-176">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="dea89-177">(Pokud jste použili GUID místo celého čísla jako typ primárního klíče, hodnoty primárního klíče studenta se nemusejí změnit a některé z těchto kroků by mohly být vynechány.)</span><span class="sxs-lookup"><span data-stu-id="dea89-177">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="dea89-178">Spusťte `database update` příkaz:</span><span class="sxs-lookup"><span data-stu-id="dea89-178">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="dea89-179">(V produkčním systému provedete odpovídající změny `Down` metody v případě, že byste to museli použít pro návrat k předchozí verzi databáze.</span><span class="sxs-lookup"><span data-stu-id="dea89-179">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="dea89-180">Pro tento kurz nebudete používat `Down` metodu.)</span><span class="sxs-lookup"><span data-stu-id="dea89-180">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="dea89-181">Při provádění změn schématu v databázi, která obsahuje existující data, je možné získat další chyby.</span><span class="sxs-lookup"><span data-stu-id="dea89-181">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="dea89-182">Pokud získáte chyby migrace, které nelze vyřešit, můžete buď změnit název databáze v připojovacím řetězci nebo odstranit databázi.</span><span class="sxs-lookup"><span data-stu-id="dea89-182">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="dea89-183">V případě nové databáze není k dispozici žádná data k migraci a příkaz Update-Database je pravděpodobnější, že se dokončí bez chyb.</span><span class="sxs-lookup"><span data-stu-id="dea89-183">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="dea89-184">Databázi odstraníte tak, že použijete SSOX nebo spustíte `database drop` příkaz CLI.</span><span class="sxs-lookup"><span data-stu-id="dea89-184">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="dea89-185">Testování implementace</span><span class="sxs-lookup"><span data-stu-id="dea89-185">Test the implementation</span></span>

<span data-ttu-id="dea89-186">Spusťte aplikaci a vyzkoušejte si různé stránky.</span><span class="sxs-lookup"><span data-stu-id="dea89-186">Run the app and try various pages.</span></span> <span data-ttu-id="dea89-187">Vše funguje stejně jako dříve.</span><span class="sxs-lookup"><span data-stu-id="dea89-187">Everything works the same as it did before.</span></span>

<span data-ttu-id="dea89-188">V **Průzkumník objektů systému SQL Server**rozbalte **data připojení/SchoolContext** a pak **tabulky**a uvidíte, že tabulky student a instruktor byly nahrazeny tabulkou Person.</span><span class="sxs-lookup"><span data-stu-id="dea89-188">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="dea89-189">Otevřete návrháře tabulky osoba a uvidíte, že obsahuje všechny sloupce, které se používají v tabulkách student a instruktor.</span><span class="sxs-lookup"><span data-stu-id="dea89-189">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Tabulka Person v SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="dea89-191">Klikněte pravým tlačítkem myši na tabulku Person a potom kliknutím na možnost **Zobrazit data tabulky** zobrazte sloupec diskriminátor.</span><span class="sxs-lookup"><span data-stu-id="dea89-191">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Tabulka Person v tabulce SSOX data](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="dea89-193">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="dea89-193">Get the code</span></span>

[<span data-ttu-id="dea89-194">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="dea89-194">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="dea89-195">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="dea89-195">Additional resources</span></span>

<span data-ttu-id="dea89-196">Další informace o dědičnosti v Entity Framework Core naleznete v tématu [Dědičnost](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="dea89-196">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="dea89-197">Další kroky</span><span class="sxs-lookup"><span data-stu-id="dea89-197">Next steps</span></span>

<span data-ttu-id="dea89-198">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="dea89-198">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dea89-199">Namapovaná dědičnost na databázi</span><span class="sxs-lookup"><span data-stu-id="dea89-199">Mapped inheritance to database</span></span>
> * <span data-ttu-id="dea89-200">Byla vytvořena třída Person.</span><span class="sxs-lookup"><span data-stu-id="dea89-200">Created the Person class</span></span>
> * <span data-ttu-id="dea89-201">Aktualizovaný instruktor a student</span><span class="sxs-lookup"><span data-stu-id="dea89-201">Updated Instructor and Student</span></span>
> * <span data-ttu-id="dea89-202">Do modelu se přidala osoba.</span><span class="sxs-lookup"><span data-stu-id="dea89-202">Added Person to the model</span></span>
> * <span data-ttu-id="dea89-203">Vytvořené a aktualizované migrace</span><span class="sxs-lookup"><span data-stu-id="dea89-203">Created and update migrations</span></span>
> * <span data-ttu-id="dea89-204">Otestování implementace</span><span class="sxs-lookup"><span data-stu-id="dea89-204">Tested the implementation</span></span>

<span data-ttu-id="dea89-205">Přejděte k dalšímu kurzu, kde se dozvíte, jak zvládnout celou řadu poměrně pokročilých scénářů Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="dea89-205">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="dea89-206">Další: Pokročilá témata</span><span class="sxs-lookup"><span data-stu-id="dea89-206">Next: Advanced topics</span></span>](advanced.md)
