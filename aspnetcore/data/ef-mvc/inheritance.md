---
title: 'Kurz: Implementace dědičnosti – ASP.NET MVC s EF Core'
description: V tomto kurzu se seznámíte implementace dědičnosti v datovém modelu, s použitím Entity Framework Core v aplikaci ASP.NET Core.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/inheritance
ms.openlocfilehash: f80de595fd23cc9c1065e5257ad1d2376ea40cf3
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900318"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="fc409-103">Kurz: Implementace dědičnosti – ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="fc409-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="fc409-104">V předchozím kurzu zpracování výjimky souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="fc409-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="fc409-105">Tento kurzu se dozvíte, jak implementovat dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="fc409-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="fc409-106">V objektově orientované programování, můžete dědičnost usnadňuje opakované využívání kódu.</span><span class="sxs-lookup"><span data-stu-id="fc409-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="fc409-107">V tomto kurzu změníte `Instructor` a `Student` tak, že jsou odvozeny z třídy `Person` základní třída, která obsahuje vlastnosti, například `LastName` , které jsou společné pro vyučující a studenty.</span><span class="sxs-lookup"><span data-stu-id="fc409-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="fc409-108">Nebude přidat nebo změnit libovolné webové stránky, ale změníte kód, a tyto změny se automaticky projeví v databázi.</span><span class="sxs-lookup"><span data-stu-id="fc409-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="fc409-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="fc409-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fc409-110">Mapovat dědičnosti do databáze</span><span class="sxs-lookup"><span data-stu-id="fc409-110">Map inheritance to database</span></span>
> * <span data-ttu-id="fc409-111">Vytvořte třídu osoby</span><span class="sxs-lookup"><span data-stu-id="fc409-111">Create the Person class</span></span>
> * <span data-ttu-id="fc409-112">Aktualizace instruktorem a studentů</span><span class="sxs-lookup"><span data-stu-id="fc409-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="fc409-113">Do modelu přidat osoby</span><span class="sxs-lookup"><span data-stu-id="fc409-113">Add Person to the model</span></span>
> * <span data-ttu-id="fc409-114">Vytvoření a aktualizaci migrace</span><span class="sxs-lookup"><span data-stu-id="fc409-114">Create and update migrations</span></span>
> * <span data-ttu-id="fc409-115">Provedení testu</span><span class="sxs-lookup"><span data-stu-id="fc409-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fc409-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="fc409-116">Prerequisites</span></span>

* [<span data-ttu-id="fc409-117">Popisovač souběžnosti</span><span class="sxs-lookup"><span data-stu-id="fc409-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="fc409-118">Mapovat dědičnosti do databáze</span><span class="sxs-lookup"><span data-stu-id="fc409-118">Map inheritance to database</span></span>

<span data-ttu-id="fc409-119">`Instructor` a `Student` třídy v datovém modelu školy mají několik vlastností, které jsou shodné:</span><span class="sxs-lookup"><span data-stu-id="fc409-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Třídy pro studenty a instruktorem](inheritance/_static/no-inheritance.png)

<span data-ttu-id="fc409-121">Předpokládejme, že chcete vyloučit redundantní kód pro vlastnosti, které jsou sdíleny `Instructor` a `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="fc409-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="fc409-122">Nebo chcete zadat službu, která dokáže formátovat názvy bez caring, zda název pochází instruktorem nebo student.</span><span class="sxs-lookup"><span data-stu-id="fc409-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="fc409-123">Můžete vytvořit `Person` základní třída, která obsahuje pouze ty sdílené vlastnosti a pak proveďte `Instructor` a `Student` třídy dědí ze základní třídy, jak je znázorněno na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="fc409-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Pro studenty a kurzů vedených třídy odvozené od třídy osoby](inheritance/_static/inheritance.png)

<span data-ttu-id="fc409-125">Existuje několik způsobů, které tato struktura dědičnosti můžou být reprezentované v databázi.</span><span class="sxs-lookup"><span data-stu-id="fc409-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="fc409-126">Můžete mít tabulku osoba, která obsahuje informace o studenty a vyučující v jediné tabulce.</span><span class="sxs-lookup"><span data-stu-id="fc409-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="fc409-127">Některé sloupce může použít pouze na Instruktoři (HireDate), některé jenom pro studenty (EnrollmentDate), některé na obě (LastName, FirstName).</span><span class="sxs-lookup"><span data-stu-id="fc409-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="fc409-128">Obvykle museli jste sloupec diskriminátoru, který označuje, který typ každý řádek představuje.</span><span class="sxs-lookup"><span data-stu-id="fc409-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="fc409-129">Pro sloupec diskriminátoru může mít například "Kurzů vedených" pro vyučující a "Studentů" pro studenty.</span><span class="sxs-lookup"><span data-stu-id="fc409-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Příklad tabulky podle hierarchie](inheritance/_static/tph.png)

<span data-ttu-id="fc409-131">Tento model generování struktury dědičnosti entity z tabulky izolované databáze se nazývá dědičnosti na hierarchii tabulky (TPH).</span><span class="sxs-lookup"><span data-stu-id="fc409-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="fc409-132">Další možností je databázi tak, aby vypadat více jako struktury dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="fc409-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="fc409-133">Například může obsahovat pouze název pole v tabulce osoby a mít samostatné instruktorem a studentů tabulky s poli datum.</span><span class="sxs-lookup"><span data-stu-id="fc409-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Dědičnost za typ tabulky](inheritance/_static/tpt.png)

<span data-ttu-id="fc409-135">Tento model vytváření tabulky databáze pro každou třídu entity se nazývá tabulka na jeden typ (TPT) dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="fc409-135">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="fc409-136">Další možností je ještě všechny typy neabstraktní namapovat jednotlivé tabulky.</span><span class="sxs-lookup"><span data-stu-id="fc409-136">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="fc409-137">Všechny vlastnosti třídy, včetně zděděné vlastnosti mapovat na sloupce příslušné tabulky.</span><span class="sxs-lookup"><span data-stu-id="fc409-137">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="fc409-138">Tento model se nazývá dědičnost tabulky na konkrétní třídy (TPC).</span><span class="sxs-lookup"><span data-stu-id="fc409-138">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="fc409-139">Pokud jste implementovali TPC dědičnosti pro osobu, studenty a kurzů vedených třídy, jak je uvedeno výše, by vypadalo tabulky studentů a kurzů vedených nijak neliší po implementaci dědičnosti než dříve.</span><span class="sxs-lookup"><span data-stu-id="fc409-139">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="fc409-140">TPC a TPH vzory dědičnosti obecně doručit lepší výkon než vzory TPT dědičnosti, protože TPT vzory může vést k dotazům komplexní spojení.</span><span class="sxs-lookup"><span data-stu-id="fc409-140">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="fc409-141">Tento kurz ukazuje, jak implementovat TPH dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="fc409-141">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="fc409-142">TPH je pouze dědičnosti vzor, který podporuje Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="fc409-142">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="fc409-143">Co můžete udělat, je vytvořit `Person` tříd, změnit `Instructor` a `Student` třídy, které jsou odvozeny z `Person`, přidejte novou třídu do `DbContext`a vytvořit migrace.</span><span class="sxs-lookup"><span data-stu-id="fc409-143">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="fc409-144">Zvažte možnost uložení kopie projektu před provedením následující změny.</span><span class="sxs-lookup"><span data-stu-id="fc409-144">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="fc409-145">Pak pokud narazíte na problémy a potřebujete začít znovu, ji bude snazší spuštění z projektu uložený místo vrácení kroků v tomto kurzu nebo že přejdete zpět na začátek celou řadu.</span><span class="sxs-lookup"><span data-stu-id="fc409-145">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="fc409-146">Vytvořte třídu osoby</span><span class="sxs-lookup"><span data-stu-id="fc409-146">Create the Person class</span></span>

<span data-ttu-id="fc409-147">Ve složce modely vytvořit Person.cs a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="fc409-147">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="fc409-148">Aktualizace instruktorem a studentů</span><span class="sxs-lookup"><span data-stu-id="fc409-148">Update Instructor and Student</span></span>

<span data-ttu-id="fc409-149">V *Instructor.cs*, kurzů vedených třída odvozena od třídy osoby a odstraňte klíč a název pole.</span><span class="sxs-lookup"><span data-stu-id="fc409-149">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="fc409-150">Kód bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fc409-150">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="fc409-151">Provést stejné změny v *Student.cs*.</span><span class="sxs-lookup"><span data-stu-id="fc409-151">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="fc409-152">Do modelu přidat osoby</span><span class="sxs-lookup"><span data-stu-id="fc409-152">Add Person to the model</span></span>

<span data-ttu-id="fc409-153">Přidat typ entity osoby k *SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="fc409-153">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="fc409-154">Nové řádky jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="fc409-154">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="fc409-155">To je vše, Entity Framework potřebuje, aby konfigurace tabulky na hierarchii dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="fc409-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="fc409-156">Jak zjistíte, když se aktualizuje databázi, bude mít tabulku osoba místo tabulky studentů a instruktorem.</span><span class="sxs-lookup"><span data-stu-id="fc409-156">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="fc409-157">Vytvoření a aktualizaci migrace</span><span class="sxs-lookup"><span data-stu-id="fc409-157">Create and update migrations</span></span>

<span data-ttu-id="fc409-158">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="fc409-158">Save your changes and build the project.</span></span> <span data-ttu-id="fc409-159">Potom otevřete okno příkazového řádku ve složce projektu a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fc409-159">Then open the command window in the project folder and enter the following command:</span></span>

```console
dotnet ef migrations add Inheritance
```

<span data-ttu-id="fc409-160">Při spuštění `database update` ještě příkazu.</span><span class="sxs-lookup"><span data-stu-id="fc409-160">Don't run the `database update` command yet.</span></span> <span data-ttu-id="fc409-161">Tento příkaz způsobí nedošlo ke ztrátě dat. vzhledem k tomu, že bude tabulku instruktorem a přejmenovat Tabulka Student osobě.</span><span class="sxs-lookup"><span data-stu-id="fc409-161">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="fc409-162">Budete muset zadat vlastní kód, chcete-li zachovat existující data.</span><span class="sxs-lookup"><span data-stu-id="fc409-162">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="fc409-163">Otevřít *migrace /\<časové razítko > _Inheritance.cs* a nahraďte `Up` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="fc409-163">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="fc409-164">Tento kód se postará o tyto úlohy aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="fc409-164">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="fc409-165">Odebere omezení cizího klíče a indexy, které odkazují na tabulce studentů.</span><span class="sxs-lookup"><span data-stu-id="fc409-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="fc409-166">Přejmenuje tabulce kurzů vedených jako osoba a provede změny, které jsou potřeba, aby se uložení údajů studentů:</span><span class="sxs-lookup"><span data-stu-id="fc409-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="fc409-167">Přidá EnrollmentDate s možnou hodnotou Null pro studenty.</span><span class="sxs-lookup"><span data-stu-id="fc409-167">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="fc409-168">Přidá sloupec diskriminátoru k označení, zda je řádek student nebo instruktorem.</span><span class="sxs-lookup"><span data-stu-id="fc409-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="fc409-169">Protože student řádků nebude mít dat, díky HireDate s možnou hodnotou Null.</span><span class="sxs-lookup"><span data-stu-id="fc409-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="fc409-170">Přidá dočasné pole, který se použije k aktualizaci cizí klíče, které odkazují na studenty.</span><span class="sxs-lookup"><span data-stu-id="fc409-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="fc409-171">Při kopírování studenty do tabulky osob dostanou nové hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="fc409-171">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="fc409-172">Kopíruje data z tabulky Student do tabulky osob.</span><span class="sxs-lookup"><span data-stu-id="fc409-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="fc409-173">To způsobí, že studenti mohli přiřadit nové hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="fc409-173">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="fc409-174">Opravy hodnoty cizího klíče, které odkazují na studenty.</span><span class="sxs-lookup"><span data-stu-id="fc409-174">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="fc409-175">Znovu vytvoří omezení cizího klíče a indexy, nyní je odkazující na tabulku osoby.</span><span class="sxs-lookup"><span data-stu-id="fc409-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="fc409-176">(Namísto celého čísla byste použili identifikátor GUID jako typ primárního klíče, student hodnoty primárního klíče nemusí měnit a některé z těchto kroků může vynechat.)</span><span class="sxs-lookup"><span data-stu-id="fc409-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="fc409-177">Spustit `database update` příkaz:</span><span class="sxs-lookup"><span data-stu-id="fc409-177">Run the `database update` command:</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="fc409-178">(V produkční systém by proveďte příslušné změny `Down` metodu v případě někdy museli vrátit k předchozí verzi databáze použít.</span><span class="sxs-lookup"><span data-stu-id="fc409-178">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="fc409-179">V tomto kurzu nebudete používat `Down` metoda.)</span><span class="sxs-lookup"><span data-stu-id="fc409-179">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="fc409-180">Je možné získat další chyby při provedení změn schématu v databázi, která obsahuje už existující data.</span><span class="sxs-lookup"><span data-stu-id="fc409-180">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="fc409-181">Pokud se zobrazí chyby při migraci, které nelze vyřešit, můžete změnit název databáze v připojovacím řetězci nebo odstranit databázi.</span><span class="sxs-lookup"><span data-stu-id="fc409-181">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="fc409-182">S novou databázi nejsou žádná data k migraci a příkazu update databáze má větší pravděpodobnost dokončí bez chyb.</span><span class="sxs-lookup"><span data-stu-id="fc409-182">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="fc409-183">Pokud chcete odstranit databázi, použijte SSOX nebo spusťte `database drop` příkazu rozhraní příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="fc409-183">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="fc409-184">Provedení testu</span><span class="sxs-lookup"><span data-stu-id="fc409-184">Test the implementation</span></span>

<span data-ttu-id="fc409-185">Spusťte aplikaci a vyzkoušejte různé stránky.</span><span class="sxs-lookup"><span data-stu-id="fc409-185">Run the app and try various pages.</span></span> <span data-ttu-id="fc409-186">Všechno, co funguje stejně jako předtím.</span><span class="sxs-lookup"><span data-stu-id="fc409-186">Everything works the same as it did before.</span></span>

<span data-ttu-id="fc409-187">V **Průzkumník objektů systému SQL Server**, rozbalte **datového připojení/SchoolContext** a potom **tabulky**, a uvidíte, že byly nahrazeny tabulky studentů a instruktorem Tabulka osoby.</span><span class="sxs-lookup"><span data-stu-id="fc409-187">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="fc409-188">Otevření Návrháře tabulky osoby a uvidíte, že obsahuje všechny sloupce, které mají být používány v tabulkách studentů a kurzů vedených.</span><span class="sxs-lookup"><span data-stu-id="fc409-188">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Tabulky osob v SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="fc409-190">Klikněte pravým tlačítkem na tabulku osoba a potom klikněte na **zobrazit Data tabulky** zobrazit sloupec diskriminátoru.</span><span class="sxs-lookup"><span data-stu-id="fc409-190">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Osoba tabulky v SSOX - tabulkových dat](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="fc409-192">Získat kód</span><span class="sxs-lookup"><span data-stu-id="fc409-192">Get the code</span></span>

[<span data-ttu-id="fc409-193">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="fc409-193">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="fc409-194">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fc409-194">Additional resources</span></span>

<span data-ttu-id="fc409-195">Další informace o dědičnosti v Entity Framework Core najdete v tématu [dědičnosti](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="fc409-195">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="fc409-196">Další kroky</span><span class="sxs-lookup"><span data-stu-id="fc409-196">Next steps</span></span>

<span data-ttu-id="fc409-197">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="fc409-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fc409-198">Mapovaná dědičnosti do databáze</span><span class="sxs-lookup"><span data-stu-id="fc409-198">Mapped inheritance to database</span></span>
> * <span data-ttu-id="fc409-199">Vytvoření třídy osoby</span><span class="sxs-lookup"><span data-stu-id="fc409-199">Created the Person class</span></span>
> * <span data-ttu-id="fc409-200">Aktualizované instruktorem a studentů</span><span class="sxs-lookup"><span data-stu-id="fc409-200">Updated Instructor and Student</span></span>
> * <span data-ttu-id="fc409-201">Přidání, kdo modelu</span><span class="sxs-lookup"><span data-stu-id="fc409-201">Added Person to the model</span></span>
> * <span data-ttu-id="fc409-202">Vytvoření a aktualizaci migrace</span><span class="sxs-lookup"><span data-stu-id="fc409-202">Created and update migrations</span></span>
> * <span data-ttu-id="fc409-203">Testování implementace</span><span class="sxs-lookup"><span data-stu-id="fc409-203">Tested the implementation</span></span>

<span data-ttu-id="fc409-204">Přejděte k dalšímu kurzu, kde se naučíte, jak zpracovat širokou škálu relativně pokročilé scénáře rozhraní Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="fc409-204">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="fc409-205">Další: Pokročilá témata</span><span class="sxs-lookup"><span data-stu-id="fc409-205">Next: Advanced topics</span></span>](advanced.md)
