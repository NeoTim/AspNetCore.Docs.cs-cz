---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Šablony: Implementace dědičnosti s EF v aplikacích pro rozhraní ASP.NET MVC 5'
description: Tento kurzu se dozvíte, jak implementovat dědičnosti v datovém modelu.
author: tdykstra
ms.author: riande
ms.date: 01/21/2019
ms.topic: tutorial
ms.assetid: 08834147-77ec-454a-bb7a-d931d2a40dab
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 79513edce7ac3044f6f547149400cba7d307edfa
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889891"
---
# <a name="template-implement-inheritance-with-ef-in-an-aspnet-mvc-5-app"></a><span data-ttu-id="6ed39-103">Šablony: Implementace dědičnosti s EF v aplikaci ASP.NET MVC 5</span><span class="sxs-lookup"><span data-stu-id="6ed39-103">Template: Implement Inheritance with EF in an ASP.NET MVC 5 app</span></span>

<span data-ttu-id="6ed39-104">V předchozím kurzu zpracování výjimky souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="6ed39-104">In the previous tutorial you handled concurrency exceptions.</span></span> <span data-ttu-id="6ed39-105">Tento kurzu se dozvíte, jak implementovat dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="6ed39-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="6ed39-106">V objektově orientované programování, můžete použít [dědičnosti](http://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) usnadnit [opakované použití kódu](http://en.wikipedia.org/wiki/Code_reuse).</span><span class="sxs-lookup"><span data-stu-id="6ed39-106">In object-oriented programming, you can use [inheritance](http://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) to facilitate [code reuse](http://en.wikipedia.org/wiki/Code_reuse).</span></span> <span data-ttu-id="6ed39-107">V tomto kurzu změníte `Instructor` a `Student` tak, že jsou odvozeny z třídy `Person` základní třída, která obsahuje vlastnosti, například `LastName` , které jsou společné pro vyučující a studenty.</span><span class="sxs-lookup"><span data-stu-id="6ed39-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="6ed39-108">Nebude přidat nebo změnit libovolné webové stránky, ale změníte kód, a tyto změny se automaticky projeví v databázi.</span><span class="sxs-lookup"><span data-stu-id="6ed39-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="6ed39-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="6ed39-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6ed39-110">Zjistěte, jak mapovat dědičnosti do databáze</span><span class="sxs-lookup"><span data-stu-id="6ed39-110">Learn to map inheritance to database</span></span>
> * <span data-ttu-id="6ed39-111">Vytvořte třídu osoby</span><span class="sxs-lookup"><span data-stu-id="6ed39-111">Create the Person class</span></span>
> * <span data-ttu-id="6ed39-112">Aktualizace instruktorem a studentů</span><span class="sxs-lookup"><span data-stu-id="6ed39-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="6ed39-113">Do modelu přidat osoby</span><span class="sxs-lookup"><span data-stu-id="6ed39-113">Add Person to the Model</span></span>
> * <span data-ttu-id="6ed39-114">Vytvoření a aktualizaci migrace</span><span class="sxs-lookup"><span data-stu-id="6ed39-114">Create and Update Migrations</span></span>
> * <span data-ttu-id="6ed39-115">Provedení testu</span><span class="sxs-lookup"><span data-stu-id="6ed39-115">Test the implementation</span></span>
> * <span data-ttu-id="6ed39-116">Nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="6ed39-116">Deploy to Azure</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ed39-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6ed39-117">Prerequisites</span></span>

* [<span data-ttu-id="6ed39-118">Implementace dědičnosti</span><span class="sxs-lookup"><span data-stu-id="6ed39-118">Implementing Inheritance</span></span>](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="6ed39-119">Mapovat dědičnosti do databáze</span><span class="sxs-lookup"><span data-stu-id="6ed39-119">Map inheritance to database</span></span>

<span data-ttu-id="6ed39-120">`Instructor` a `Student` tříd v `School` datovém modelu mají několik vlastností, které jsou shodné:</span><span class="sxs-lookup"><span data-stu-id="6ed39-120">The `Instructor` and `Student` classes in the `School` data model have several properties that are identical:</span></span>

![Student_and_Instructor_classes](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

<span data-ttu-id="6ed39-122">Předpokládejme, že chcete vyloučit redundantní kód pro vlastnosti, které jsou sdíleny `Instructor` a `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="6ed39-122">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="6ed39-123">Nebo chcete zadat službu, která dokáže formátovat názvy bez caring, zda název pochází instruktorem nebo student.</span><span class="sxs-lookup"><span data-stu-id="6ed39-123">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="6ed39-124">Můžete vytvořit `Person` základní třída, která obsahuje pouze ty sdílené vlastnosti a pak proveďte `Instructor` a `Student` entit dědí ze základní třídy, jak je znázorněno na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="6ed39-124">You could create a `Person` base class which contains only those shared properties, then make the `Instructor` and `Student` entities inherit from that base class, as shown in the following illustration:</span></span>

![Student_and_Instructor_classes_deriving_from_Person_class](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

<span data-ttu-id="6ed39-126">Existuje několik způsobů, které tato struktura dědičnosti můžou být reprezentované v databázi.</span><span class="sxs-lookup"><span data-stu-id="6ed39-126">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="6ed39-127">Mohli byste mít `Person` tabulku, která obsahuje informace o studenty a vyučující v jediné tabulce.</span><span class="sxs-lookup"><span data-stu-id="6ed39-127">You could have a `Person` table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="6ed39-128">Některé sloupce, které může používat jenom u Instruktoři (`HireDate`), některé jenom pro studenty (`EnrollmentDate`), některé obě (`LastName`, `FirstName`).</span><span class="sxs-lookup"><span data-stu-id="6ed39-128">Some of the columns could apply only to instructors (`HireDate`), some only to students (`EnrollmentDate`), some to both (`LastName`, `FirstName`).</span></span> <span data-ttu-id="6ed39-129">Obvykle byste měli *diskriminátoru* sloupec, aby označoval, jaký typ každý řádek představuje.</span><span class="sxs-lookup"><span data-stu-id="6ed39-129">Typically, you'd have a *discriminator* column to indicate which type each row represents.</span></span> <span data-ttu-id="6ed39-130">Pro sloupec diskriminátoru může mít například "Kurzů vedených" pro vyučující a "Studentů" pro studenty.</span><span class="sxs-lookup"><span data-stu-id="6ed39-130">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Table-per-hierarchy_example](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

<span data-ttu-id="6ed39-132">Tento model generování struktury dědičnosti entity z tabulky izolovanou databázi nazvali *na hierarchii tabulky* dědičnost (TPH).</span><span class="sxs-lookup"><span data-stu-id="6ed39-132">This pattern of generating an entity inheritance structure from a single database table is called *table-per-hierarchy* (TPH) inheritance.</span></span>

<span data-ttu-id="6ed39-133">Další možností je databázi tak, aby vypadat více jako struktury dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="6ed39-133">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="6ed39-134">Například může mít pouze název pole `Person` tabulky a mít samostatné `Instructor` a `Student` tabulky s poli datum.</span><span class="sxs-lookup"><span data-stu-id="6ed39-134">For example, you could have only the name fields in the `Person` table and have separate `Instructor` and `Student` tables with the date fields.</span></span>

![Table-per-type_inheritance](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

<span data-ttu-id="6ed39-136">Tento model vytváření tabulky databáze pro každou třídu entity se nazývá *tabulky podle typu* dědičnosti (TPT).</span><span class="sxs-lookup"><span data-stu-id="6ed39-136">This pattern of making a database table for each entity class is called *table per type* (TPT) inheritance.</span></span>

<span data-ttu-id="6ed39-137">Další možností je ještě všechny typy neabstraktní namapovat jednotlivé tabulky.</span><span class="sxs-lookup"><span data-stu-id="6ed39-137">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="6ed39-138">Všechny vlastnosti třídy, včetně zděděné vlastnosti mapovat na sloupce příslušné tabulky.</span><span class="sxs-lookup"><span data-stu-id="6ed39-138">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="6ed39-139">Tento model se nazývá dědičnost tabulky na konkrétní třídy (TPC).</span><span class="sxs-lookup"><span data-stu-id="6ed39-139">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="6ed39-140">Pokud jste implementovali TPC dědičnosti pro `Person`, `Student`, a `Instructor` třídy, jak je uvedeno výše, `Student` a `Instructor` tabulky by vypadalo nijak neliší po implementaci dědičnosti než dříve.</span><span class="sxs-lookup"><span data-stu-id="6ed39-140">If you implemented TPC inheritance for the `Person`, `Student`, and `Instructor` classes as shown earlier, the `Student` and `Instructor` tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="6ed39-141">TPC a vzory dědičnosti TPH obecně poskytují lepší výkon v Entity Framework než vzory TPT dědičnosti, protože TPT vzory může vést k dotazům komplexní spojení.</span><span class="sxs-lookup"><span data-stu-id="6ed39-141">TPC and TPH inheritance patterns generally deliver better performance in the Entity Framework than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="6ed39-142">Tento kurz ukazuje, jak implementovat TPH dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="6ed39-142">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="6ed39-143">TPH je výchozí model dědičnosti v Entity Framework, aby vše, co musíte udělat, je vytvořit `Person` tříd, změnit `Instructor` a `Student` třídy, které jsou odvozeny z `Person`, přidejte novou třídu do `DbContext`a vytvořit migrace.</span><span class="sxs-lookup"><span data-stu-id="6ed39-143">TPH is the default inheritance pattern in the Entity Framework, so all you have to do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span> <span data-ttu-id="6ed39-144">(Informace o tom, jak implementovat další vzory dědičnosti, naleznete v tématu [mapování dědičnosti na typ tabulky (TPT)](https://msdn.microsoft.com/data/jj591617#2.5) a [mapování tabulky na konkrétní třídy (TPC) dědičnosti](https://msdn.microsoft.com/data/jj591617#2.6) v MSDN Dokumentace Entity Framework.)</span><span class="sxs-lookup"><span data-stu-id="6ed39-144">(For information about how to implement the other inheritance patterns, see [Mapping the Table-Per-Type (TPT) Inheritance](https://msdn.microsoft.com/data/jj591617#2.5) and [Mapping the Table-Per-Concrete Class (TPC) Inheritance](https://msdn.microsoft.com/data/jj591617#2.6) in the MSDN Entity Framework documentation.)</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="6ed39-145">Vytvořte třídu osoby</span><span class="sxs-lookup"><span data-stu-id="6ed39-145">Create the Person class</span></span>

<span data-ttu-id="6ed39-146">V *modely* složku, vytvořte *Person.cs* a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="6ed39-146">In the *Models* folder, create *Person.cs* and replace the template code with the following code:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="6ed39-147">Aktualizace instruktorem a studentů</span><span class="sxs-lookup"><span data-stu-id="6ed39-147">Update Instructor and Student</span></span>

<span data-ttu-id="6ed39-148">Teď aktualizovat *Instructor.cs* a *Sudent.cs* dědit z hodnoty *Person.sc*.</span><span class="sxs-lookup"><span data-stu-id="6ed39-148">Now update the *Instructor.cs* and *Sudent.cs* to inherit values from the *Person.sc*.</span></span>

<span data-ttu-id="6ed39-149">V *Instructor.cs*, odvozovat `Instructor` třídy z `Person` třídy a odstraňte klíč a název pole.</span><span class="sxs-lookup"><span data-stu-id="6ed39-149">In *Instructor.cs*, derive the `Instructor` class from the `Person` class and remove the key and name fields.</span></span> <span data-ttu-id="6ed39-150">Kód bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="6ed39-150">The code will look like the following example:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

<span data-ttu-id="6ed39-151">Provádět podobné změny *Student.cs*.</span><span class="sxs-lookup"><span data-stu-id="6ed39-151">Make similar changes to *Student.cs*.</span></span> <span data-ttu-id="6ed39-152">`Student` Třídy bude vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="6ed39-152">The `Student` class will look like the following example:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="6ed39-153">Do modelu přidat osoby</span><span class="sxs-lookup"><span data-stu-id="6ed39-153">Add Person to the Model</span></span>

<span data-ttu-id="6ed39-154">V *SchoolContext.cs*, přidejte `DbSet` vlastnost `Person` typ entity:</span><span class="sxs-lookup"><span data-stu-id="6ed39-154">In *SchoolContext.cs*, add a `DbSet` property for the `Person` entity type:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

<span data-ttu-id="6ed39-155">To je vše, Entity Framework potřebuje, aby konfigurace tabulky na hierarchii dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="6ed39-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="6ed39-156">Jak zjistíte, když se aktualizuje databázi, bude mít `Person` tabulky místo `Student` a `Instructor` tabulky.</span><span class="sxs-lookup"><span data-stu-id="6ed39-156">As you'll see, when the database is updated, it will have a `Person` table in place of the `Student` and `Instructor` tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="6ed39-157">Vytvoření a aktualizaci migrace</span><span class="sxs-lookup"><span data-stu-id="6ed39-157">Create and Update Migrations</span></span>

<span data-ttu-id="6ed39-158">V balíčku správce konzoly (konzolu PMC), zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="6ed39-158">In the Package Manager Console (PMC), enter the following command:</span></span>

`Add-Migration Inheritance`

<span data-ttu-id="6ed39-159">Spustit `Update-Database` příkazu v konzole PMC.</span><span class="sxs-lookup"><span data-stu-id="6ed39-159">Run the `Update-Database` command in the PMC.</span></span> <span data-ttu-id="6ed39-160">Příkaz se nezdaří v tomto okamžiku, když máme existující data, která migrace nebude vědět, jak zpracovat.</span><span class="sxs-lookup"><span data-stu-id="6ed39-160">The command will fail at this point because we have existing data that migrations doesn't know how to handle.</span></span> <span data-ttu-id="6ed39-161">Získáte třeba následující chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="6ed39-161">You get an error message like the following one:</span></span>

> <span data-ttu-id="6ed39-162">*Nelze vyřadit objekt "dbo. Instruktorem ' protože se odkazuje omezení FOREIGN KEY.*</span><span class="sxs-lookup"><span data-stu-id="6ed39-162">*Could not drop object 'dbo.Instructor' because it is referenced by a FOREIGN KEY constraint.*</span></span>


<span data-ttu-id="6ed39-163">Otevřít *migrace\&lt; časové razítko&gt;\_Inheritance.cs* a nahraďte `Up` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="6ed39-163">Open *Migrations\&lt;timestamp&gt;\_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

<span data-ttu-id="6ed39-164">Tento kód se postará o tyto úlohy aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="6ed39-164">This code takes care of the following database update tasks:</span></span>

- <span data-ttu-id="6ed39-165">Odebere omezení cizího klíče a indexy, které odkazují na tabulce studentů.</span><span class="sxs-lookup"><span data-stu-id="6ed39-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>
- <span data-ttu-id="6ed39-166">Přejmenuje tabulce kurzů vedených jako osoba a provede změny, které jsou potřeba, aby se uložení údajů studentů:</span><span class="sxs-lookup"><span data-stu-id="6ed39-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

    - <span data-ttu-id="6ed39-167">Přidá EnrollmentDate s možnou hodnotou Null pro studenty.</span><span class="sxs-lookup"><span data-stu-id="6ed39-167">Adds nullable EnrollmentDate for students.</span></span>
    - <span data-ttu-id="6ed39-168">Přidá sloupec diskriminátoru k označení, zda je řádek student nebo instruktorem.</span><span class="sxs-lookup"><span data-stu-id="6ed39-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>
    - <span data-ttu-id="6ed39-169">Protože student řádků nebude mít dat, díky HireDate s možnou hodnotou Null.</span><span class="sxs-lookup"><span data-stu-id="6ed39-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>
    - <span data-ttu-id="6ed39-170">Přidá dočasné pole, který se použije k aktualizaci cizí klíče, které odkazují na studenty.</span><span class="sxs-lookup"><span data-stu-id="6ed39-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="6ed39-171">Při kopírování do tabulky osob studenti získají první nové hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="6ed39-171">When you copy students into the Person table they'll get new primary key values.</span></span>
- <span data-ttu-id="6ed39-172">Kopíruje data z tabulky Student do tabulky osob.</span><span class="sxs-lookup"><span data-stu-id="6ed39-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="6ed39-173">To způsobí, že studenti mohli přiřadit nové hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="6ed39-173">This causes students to get assigned new primary key values.</span></span>
- <span data-ttu-id="6ed39-174">Opravy hodnoty cizího klíče, které odkazují na studenty.</span><span class="sxs-lookup"><span data-stu-id="6ed39-174">Fixes foreign key values that point to students.</span></span>
- <span data-ttu-id="6ed39-175">Znovu vytvoří omezení cizího klíče a indexy, nyní je odkazující na tabulku osoby.</span><span class="sxs-lookup"><span data-stu-id="6ed39-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="6ed39-176">(Namísto celého čísla byste použili identifikátor GUID jako typ primárního klíče, student hodnoty primárního klíče nemusí měnit a některé z těchto kroků může vynechat.)</span><span class="sxs-lookup"><span data-stu-id="6ed39-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="6ed39-177">Spustit `update-database` příkaz znovu.</span><span class="sxs-lookup"><span data-stu-id="6ed39-177">Run the `update-database` command again.</span></span>

<span data-ttu-id="6ed39-178">(V produkční systém by změníte odpovídající metody dolů v případě, že někdy museli vrátit k předchozí verzi databáze použít.</span><span class="sxs-lookup"><span data-stu-id="6ed39-178">(In a production system you would make corresponding changes to the Down method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="6ed39-179">V tomto kurzu nebudete používat metody dolů.)</span><span class="sxs-lookup"><span data-stu-id="6ed39-179">For this tutorial you won't be using the Down method.)</span></span>

> [!NOTE]
> <span data-ttu-id="6ed39-180">Je možné získat další chyby při migraci dat a provádění změn schématu.</span><span class="sxs-lookup"><span data-stu-id="6ed39-180">It's possible to get other errors when migrating data and making schema changes.</span></span> <span data-ttu-id="6ed39-181">Pokud se zobrazí chyby při migraci nevyřešíte sami, můžete pokračovat v kurzu tak, že změníte připojovací řetězec *Web.config* souboru nebo odstraněním databáze.</span><span class="sxs-lookup"><span data-stu-id="6ed39-181">If you get migration errors you can't resolve, you can continue with the tutorial by changing the connection string in the *Web.config* file or by deleting the database.</span></span> <span data-ttu-id="6ed39-182">Nejjednodušším způsobem je přejmenovat databázi *Web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ed39-182">The simplest approach is to rename the database in the *Web.config* file.</span></span> <span data-ttu-id="6ed39-183">Například změňte název databáze do ContosoUniversity2, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="6ed39-183">For example, change the database name to ContosoUniversity2 as shown in the following example:</span></span>
>
> [!code-xml[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.xml?highlight=2)]
>
> <span data-ttu-id="6ed39-184">S novou databázi, nejsou žádná data chcete migrovat a `update-database` příkaz je mnohem pravděpodobnější k dokončení bez chyb.</span><span class="sxs-lookup"><span data-stu-id="6ed39-184">With a new database, there is no data to migrate, and the `update-database` command is much more likely to complete without errors.</span></span> <span data-ttu-id="6ed39-185">Pokyny k odstranění databáze najdete v tématu [jak vyřadit databázi ze sady Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/).</span><span class="sxs-lookup"><span data-stu-id="6ed39-185">For instructions on how to delete the database, see [How to Drop a Database from Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/).</span></span> <span data-ttu-id="6ed39-186">Pokud budete postupovat tímto přístupem budete pokračovat v kurzu, na konci tohoto kurzu přeskočit krok nasazení nebo nasazení do nové lokality a databáze.</span><span class="sxs-lookup"><span data-stu-id="6ed39-186">If you take this approach in order to continue with the tutorial, skip the deployment step at the end of this tutorial or deploy to a new site and database.</span></span> <span data-ttu-id="6ed39-187">Pokud nasazujete aktualizace do stejné lokality, které jste se nasazujete do již, EF dojde ke stejné chybě migrace automaticky spustí.</span><span class="sxs-lookup"><span data-stu-id="6ed39-187">If you deploy an update to the same site you've been deploying to already, EF will get the same error there when it runs migrations automatically.</span></span> <span data-ttu-id="6ed39-188">Pokud chcete odstranění chyby migrace, je nejlepší prostředek Entity Framework fóra nebo StackOverflow.com.</span><span class="sxs-lookup"><span data-stu-id="6ed39-188">If you want to troubleshoot a migrations error, the best resource is one of the Entity Framework forums or StackOverflow.com.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="6ed39-189">Provedení testu</span><span class="sxs-lookup"><span data-stu-id="6ed39-189">Test the implementation</span></span>

<span data-ttu-id="6ed39-190">Spuštění tohoto webu a vyzkoušejte různé stránky.</span><span class="sxs-lookup"><span data-stu-id="6ed39-190">Run the site and try various pages.</span></span> <span data-ttu-id="6ed39-191">Všechno, co funguje stejně jako předtím.</span><span class="sxs-lookup"><span data-stu-id="6ed39-191">Everything works the same as it did before.</span></span>

<span data-ttu-id="6ed39-192">V **Průzkumníka serveru** rozbalte **Data Connections\SchoolContext** a potom **tabulky**, a uvidíte, že **Student** a **Kurzů vedených** byly nahrazeny tabulky **osoba** tabulky.</span><span class="sxs-lookup"><span data-stu-id="6ed39-192">In **Server Explorer,** expand **Data Connections\SchoolContext** and then **Tables**, and you see that the **Student** and **Instructor** tables have been replaced by a **Person** table.</span></span> <span data-ttu-id="6ed39-193">Rozbalte **osoba** tabulky a najdete v článku, že obsahuje všechny sloupce, které mají být používány v **Student** a **kurzů vedených** tabulky.</span><span class="sxs-lookup"><span data-stu-id="6ed39-193">Expand the **Person** table and you see that it has all of the columns that used to be in the **Student** and **Instructor** tables.</span></span>

<span data-ttu-id="6ed39-194">Klikněte pravým tlačítkem na tabulku osoba a potom klikněte na **zobrazit Data tabulky** zobrazit sloupec diskriminátoru.</span><span class="sxs-lookup"><span data-stu-id="6ed39-194">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

<span data-ttu-id="6ed39-195">Následující diagram znázorňuje strukturu nové databáze školy:</span><span class="sxs-lookup"><span data-stu-id="6ed39-195">The following diagram illustrates the structure of the new School database:</span></span>

![School_database_diagram](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

## <a name="deploy-to-azure"></a><span data-ttu-id="6ed39-197">Nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="6ed39-197">Deploy to Azure</span></span>

<span data-ttu-id="6ed39-198">Tato část vyžaduje, abyste dokončili nepovinný **nasazení aplikace do Azure** tématu [část 3, řazení, filtrování a stránkování](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md) této série kurzů.</span><span class="sxs-lookup"><span data-stu-id="6ed39-198">This section requires you to have completed the optional **Deploying the app to Azure** section in [Part 3, Sorting, Filtering, and Paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md) of this tutorial series.</span></span> <span data-ttu-id="6ed39-199">Pokud jste měli migrace chyby, které jste vyřešili tak, že odstraníte databáze v projektu pro místní, přeskočte tento krok. nebo vytvořte novou lokalitu a databázi a nasadit do nového prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ed39-199">If you had migrations errors that you resolved by deleting the database in your local project, skip this step; or create a new site and database, and deploy to the new environment.</span></span>

1. <span data-ttu-id="6ed39-200">Ve Visual Studiu klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat** v místní nabídce.</span><span class="sxs-lookup"><span data-stu-id="6ed39-200">In Visual Studio, right-click the project in **Solution Explorer** and select **Publish** from the context menu.</span></span>

2. <span data-ttu-id="6ed39-201">Klikněte na tlačítko **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="6ed39-201">Click **Publish**.</span></span>

    <span data-ttu-id="6ed39-202">Webové aplikace se otevře ve vašem výchozím prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="6ed39-202">The Web app opens in your default browser.</span></span>

3. <span data-ttu-id="6ed39-203">Otestování aplikace ověří, že funguje.</span><span class="sxs-lookup"><span data-stu-id="6ed39-203">Test the application to verify it's working.</span></span>

    <span data-ttu-id="6ed39-204">Při prvním spuštění stránky, který přistupuje k databázi, Entity Framework provozovat všechny migrace `Up` metod požadovaných k aktuální s aktuálním datovým modelem databáze.</span><span class="sxs-lookup"><span data-stu-id="6ed39-204">The first time you run a page that accesses the database, the Entity Framework runs all of the migrations `Up` methods required to bring the database up to date with the current data model.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="6ed39-205">Získat kód</span><span class="sxs-lookup"><span data-stu-id="6ed39-205">Get the code</span></span>

[<span data-ttu-id="6ed39-206">Stáhnout dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="6ed39-206">Download Completed Project</span></span>](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a><span data-ttu-id="6ed39-207">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6ed39-207">Additional resources</span></span>

<span data-ttu-id="6ed39-208">Odkazy na další zdroje Entity Framework najdete v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="6ed39-208">Links to other Entity Framework resources can be found in the [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

<span data-ttu-id="6ed39-209">Další informace o tomto a dalších struktury dědičnosti najdete v tématu [model dědičnosti TPT](https://msdn.microsoft.com/data/jj618293) a [model dědičnosti TPH](https://msdn.microsoft.com/data/jj618292) na webové stránce MSDN.</span><span class="sxs-lookup"><span data-stu-id="6ed39-209">For more information about this and other inheritance structures, see [TPT Inheritance Pattern](https://msdn.microsoft.com/data/jj618293) and [TPH Inheritance Pattern](https://msdn.microsoft.com/data/jj618292) on MSDN.</span></span> <span data-ttu-id="6ed39-210">V dalším kurzu uvidíte, jak zpracovat širokou škálu relativně pokročilé scénáře rozhraní Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="6ed39-210">In the next tutorial you'll see how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6ed39-211">Další kroky</span><span class="sxs-lookup"><span data-stu-id="6ed39-211">Next steps</span></span>

<span data-ttu-id="6ed39-212">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="6ed39-212">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6ed39-213">Zjistili, jak mapovat dědičnosti do databáze</span><span class="sxs-lookup"><span data-stu-id="6ed39-213">Learned to map inheritance to database</span></span>
> * <span data-ttu-id="6ed39-214">Vytvoření třídy osoby</span><span class="sxs-lookup"><span data-stu-id="6ed39-214">Created the Person class</span></span>
> * <span data-ttu-id="6ed39-215">Aktualizované instruktorem a studentů</span><span class="sxs-lookup"><span data-stu-id="6ed39-215">Updated Instructor and Student</span></span>
> * <span data-ttu-id="6ed39-216">Přidání, kdo modelu</span><span class="sxs-lookup"><span data-stu-id="6ed39-216">Added Person to the Model</span></span>
> * <span data-ttu-id="6ed39-217">Vytvoření a aktualizaci migrace</span><span class="sxs-lookup"><span data-stu-id="6ed39-217">Created and Update Migrations</span></span>
> * <span data-ttu-id="6ed39-218">Testování implementace</span><span class="sxs-lookup"><span data-stu-id="6ed39-218">Tested the implementation</span></span>
> * <span data-ttu-id="6ed39-219">Nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="6ed39-219">Deployed to Azure</span></span>

<span data-ttu-id="6ed39-220">Přejděte k dalším článku se dozvíte o tématech, které jsou užitečné mít na paměti při nad rámec základní informace o vývoji webových aplikací ASP.NET, které používají Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="6ed39-220">Advance to the next article to learn about topics that are useful to be aware of when you go beyond the basics of developing ASP.NET web applications that use Entity Framework Code First.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="6ed39-221">Pokročilé scénáře pro Entity Framework</span><span class="sxs-lookup"><span data-stu-id="6ed39-221">Advanced Entity Framework Scenarios</span></span>](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)