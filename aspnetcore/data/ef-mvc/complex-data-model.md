---
title: 'Kurz: vytvoření složitého datového modelu – ASP.NET MVC pomocí EF Core'
description: V tomto kurzu přidejte další entity a vztahy a upravte datový model zadáním formátování, ověřování a pravidel mapování.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 91fd09874ecab8bfdb6a38a404faba04aeb73edc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657428"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="5cd6e-103">Kurz: vytvoření složitého datového modelu – ASP.NET MVC pomocí EF Core</span><span class="sxs-lookup"><span data-stu-id="5cd6e-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="5cd6e-104">V předchozích kurzech jste pracovali s jednoduchým datovým modelem, který se skládá ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="5cd6e-105">V tomto kurzu přidáte další entity a vztahy a budete přizpůsobovat datový model zadáním pravidel formátování, ověřování a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="5cd6e-106">Až budete hotovi, třídy entit vytvoří dokončený datový model, který je znázorněn na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="5cd6e-108">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5cd6e-109">Přizpůsobení datového modelu</span><span class="sxs-lookup"><span data-stu-id="5cd6e-109">Customize the Data model</span></span>
> * <span data-ttu-id="5cd6e-110">Provedení změn v entitě studenta</span><span class="sxs-lookup"><span data-stu-id="5cd6e-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="5cd6e-111">Vytvořit entitu instruktora</span><span class="sxs-lookup"><span data-stu-id="5cd6e-111">Create Instructor entity</span></span>
> * <span data-ttu-id="5cd6e-112">Vytvořit entitu OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="5cd6e-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="5cd6e-113">Upravit entitu kurzu</span><span class="sxs-lookup"><span data-stu-id="5cd6e-113">Modify Course entity</span></span>
> * <span data-ttu-id="5cd6e-114">Vytvořit entitu oddělení</span><span class="sxs-lookup"><span data-stu-id="5cd6e-114">Create Department entity</span></span>
> * <span data-ttu-id="5cd6e-115">Upravit entitu registrace</span><span class="sxs-lookup"><span data-stu-id="5cd6e-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="5cd6e-116">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="5cd6e-116">Update the database context</span></span>
> * <span data-ttu-id="5cd6e-117">Počáteční databáze s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="5cd6e-117">Seed database with test data</span></span>
> * <span data-ttu-id="5cd6e-118">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="5cd6e-118">Add a migration</span></span>
> * <span data-ttu-id="5cd6e-119">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="5cd6e-119">Change the connection string</span></span>
> * <span data-ttu-id="5cd6e-120">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="5cd6e-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5cd6e-121">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="5cd6e-121">Prerequisites</span></span>

* [<span data-ttu-id="5cd6e-122">Použití migrace EF Core</span><span class="sxs-lookup"><span data-stu-id="5cd6e-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="5cd6e-123">Přizpůsobení datového modelu</span><span class="sxs-lookup"><span data-stu-id="5cd6e-123">Customize the Data model</span></span>

<span data-ttu-id="5cd6e-124">V této části se dozvíte, jak přizpůsobit datový model pomocí atributů, které určují pravidla formátování, ověřování a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="5cd6e-125">Potom v několika následujících částech vytvoříte kompletní model školních dat přidáním atributů do tříd, které jste už vytvořili, a vytvořením nových tříd pro zbývající typy entit v modelu.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="5cd6e-126">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="5cd6e-126">The DataType attribute</span></span>

<span data-ttu-id="5cd6e-127">Pro data o registraci studenta se aktuálně zobrazují všechny webové stránky, které jsou aktuálně současně s datem, i když jsou pro toto pole k disstará data.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="5cd6e-128">Pomocí atributů datových poznámek můžete vytvořit jednu změnu kódu, která bude opravovat formát zobrazení v každém zobrazení, které zobrazuje data.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="5cd6e-129">Chcete-li zobrazit příklad, jak to provést, přidáte atribut do vlastnosti `EnrollmentDate` ve třídě `Student`.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="5cd6e-130">V *modelech/student. cs*přidejte příkaz `using` pro obor názvů `System.ComponentModel.DataAnnotations` a přidejte `DataType` a `DisplayFormat` atributů do vlastnosti `EnrollmentDate`, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="5cd6e-131">Atribut `DataType` slouží k zadání datového typu, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="5cd6e-132">V tomto případě chceme sledovat pouze datum, nikoli datum a čas.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="5cd6e-133">Výčet `DataType` poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress a další.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="5cd6e-134">Atribut `DataType` může také povolit aplikaci automatické poskytování funkcí specifických pro typ.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="5cd6e-135">Například odkaz `mailto:` lze vytvořit pro `DataType.EmailAddress`a selektor data lze zadat pro `DataType.Date` v prohlížečích, které podporují HTML5.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="5cd6e-136">Atribut `DataType` emituje atributy HTML 5 `data-` (vyslovované datové přerušované), které mohou prohlížeče HTML 5 pochopit.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="5cd6e-137">Atributy `DataType` neposkytují žádné ověřování.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="5cd6e-138">`DataType.Date` neurčuje formát data, které se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="5cd6e-139">Ve výchozím nastavení se datové pole zobrazuje v závislosti na výchozích formátech na základě objektu CultureInfo serveru.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="5cd6e-140">Atribut `DisplayFormat` slouží k explicitnímu zadání formátu data:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="5cd6e-141">Nastavení `ApplyFormatInEditMode` určuje, zda má být formátování použito také v případě, že je hodnota zobrazena v textovém poli pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="5cd6e-142">(Možná nebudete chtít, aby se pro některá pole, například pro hodnoty měny, nemuseli v textovém poli pro úpravy chtít symbol měny.)</span><span class="sxs-lookup"><span data-stu-id="5cd6e-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="5cd6e-143">Můžete použít atribut `DisplayFormat` sám o sobě, ale obecně je vhodné použít atribut `DataType` také.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="5cd6e-144">Atribut `DataType` předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce a poskytuje následující výhody, které nezískáte pomocí `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="5cd6e-145">Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolu měny odpovídající národním prostředí, e-mailových odkazů, některých ověření vstupu na straně klienta atd.).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="5cd6e-146">Ve výchozím nastavení bude prohlížeč data vykreslovat pomocí správného formátu na základě vašeho národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="5cd6e-147">Další informace najdete v dokumentaci k [pomocníka značek\<input >](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="5cd6e-148">Spusťte aplikaci, klikněte na stránku indexu studentů a Všimněte si, že časy se už nezobrazuje pro data registrace.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="5cd6e-149">Totéž platí pro všechna zobrazení, která používají model studenta.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-149">The same will be true for any view that uses the Student model.</span></span>

![Stránka indexu studentů zobrazující data bez časů](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="5cd6e-151">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="5cd6e-151">The StringLength attribute</span></span>

<span data-ttu-id="5cd6e-152">Můžete také zadat pravidla ověřování dat a chybové zprávy ověřování pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="5cd6e-153">Atribut `StringLength` nastaví maximální délku v databázi a poskytuje ověřování na straně klienta a na straně serveru pro ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="5cd6e-154">V tomto atributu můžete také zadat minimální délku řetězce, ale minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="5cd6e-155">Předpokládejme, že chcete zajistit, aby uživatelé nezadali více než 50 znaků pro název.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="5cd6e-156">Chcete-li přidat toto omezení, přidejte `StringLength` atributy do vlastností `LastName` a `FirstMidName`, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="5cd6e-157">Atribut `StringLength` nezabrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="5cd6e-158">Můžete použít atribut `RegularExpression` pro použití omezení na vstup.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="5cd6e-159">Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="5cd6e-160">Atribut `MaxLength` poskytuje podobné funkce jako atribut `StringLength`, ale neposkytuje ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="5cd6e-161">Model databáze se teď změnil způsobem, který vyžaduje změnu ve schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="5cd6e-162">Pomocí migrace aktualizujete schéma bez ztráty dat, která jste mohli přidat do databáze pomocí uživatelského rozhraní aplikace.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="5cd6e-163">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-163">Save your changes and build the project.</span></span> <span data-ttu-id="5cd6e-164">Pak otevřete příkazové okno ve složce projektu a zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="5cd6e-165">Příkaz `migrations add` upozorní na to, že může dojít ke ztrátě dat, protože změna činí maximální délku pro dva sloupce.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="5cd6e-166">Migrace vytvoří soubor s názvem *\<časové razítko > _MaxLengthOnNames. cs*.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="5cd6e-167">Tento soubor obsahuje kód v metodě `Up`, který bude aktualizovat databázi tak, aby odpovídala aktuálnímu datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="5cd6e-168">Příkaz `database update` spustil tento kód.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="5cd6e-169">Časové razítko s předponou názvu souboru migrace se používá Entity Framework k seřazení migrace.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="5cd6e-170">Před spuštěním příkazu Update-Database můžete vytvořit více migrací a všechny migrace pak budou aplikovány v pořadí, ve kterém byly vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="5cd6e-171">Spusťte aplikaci, vyberte kartu **Students** , klikněte na **vytvořit novou**a zkuste zadat název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="5cd6e-172">Aplikace by vám měla zabránit v tom.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="5cd6e-173">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="5cd6e-173">The Column attribute</span></span>

<span data-ttu-id="5cd6e-174">Můžete také použít atributy pro řízení způsobu, jakým jsou třídy a vlastnosti namapovány na databázi.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="5cd6e-175">Předpokládejme, že jste použili název `FirstMidName` pro pole jméno a příjmení, protože pole může obsahovat také prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="5cd6e-176">Ale chcete, aby se sloupec databáze jmenoval `FirstName`, protože uživatelé, kteří budou psát ad hoc dotazy na databázi, jsou zvyklí na tento název.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="5cd6e-177">Chcete-li toto mapování provést, můžete použít atribut `Column`.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="5cd6e-178">Atribut `Column` určuje, že při vytvoření databáze bude sloupec `Student` tabulky, která je mapována na vlastnost `FirstMidName`, pojmenován `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="5cd6e-179">Jinými slovy, pokud váš kód odkazuje na `Student.FirstMidName`, data budou pocházet z nebo aktualizována ve sloupci `FirstName` tabulky `Student`.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="5cd6e-180">Pokud neurčíte názvy sloupců, budou mít stejný název jako název vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="5cd6e-181">V souboru *student.cs* přidejte příkaz `using` pro `System.ComponentModel.DataAnnotations.Schema` a přidejte atribut název sloupce do vlastnosti `FirstMidName`, jak ukazuje následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="5cd6e-182">Přidání atributu `Column` změní model zálohování `SchoolContext`, takže se neshoduje s databází.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="5cd6e-183">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-183">Save your changes and build the project.</span></span> <span data-ttu-id="5cd6e-184">Pak otevřete příkazové okno ve složce projektu a zadáním následujících příkazů vytvořte další migraci:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="5cd6e-185">V **Průzkumník objektů systému SQL Server**otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .</span><span class="sxs-lookup"><span data-stu-id="5cd6e-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="5cd6e-187">Před použitím prvních dvou migrací byly sloupce názvu typu nvarchar (MAX).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="5cd6e-188">Teď jsou nvarchar (50) a název sloupce se změnil z FirstMidName na FirstName.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="5cd6e-189">Pokud se pokusíte kompilovat před dokončením vytváření všech tříd entit v následujících oddílech, může dojít k chybám kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="5cd6e-190">Změny entity studenta</span><span class="sxs-lookup"><span data-stu-id="5cd6e-190">Changes to Student entity</span></span>

![Entita studenta](complex-data-model/_static/student-entity.png)

<span data-ttu-id="5cd6e-192">V *modelu/student. cs*nahraďte kód, který jste přidali dříve, následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="5cd6e-193">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="5cd6e-194">Požadovaný atribut</span><span class="sxs-lookup"><span data-stu-id="5cd6e-194">The Required attribute</span></span>

<span data-ttu-id="5cd6e-195">Atribut `Required` vytváří pole vlastnosti názvu Required.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="5cd6e-196">Atribut `Required` není potřebný pro typy, které neumožňují hodnotu null, jako jsou například typy hodnot (DateTime, int, Double, float atd.).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="5cd6e-197">Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="5cd6e-198">Atribut `Required` musí být použit s `MinimumLength` pro vymáhání `MinimumLength`.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="5cd6e-199">Atribut zobrazení</span><span class="sxs-lookup"><span data-stu-id="5cd6e-199">The Display attribute</span></span>

<span data-ttu-id="5cd6e-200">Atribut `Display` určuje, že titulek pro textová pole by měl být "křestní jméno", "příjmení", "celé jméno" a "datum zápisu" místo názvu vlastnosti v každé instanci (která nemá mezeru dělit slova).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="5cd6e-201">Vypočítaná vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="5cd6e-201">The FullName calculated property</span></span>

<span data-ttu-id="5cd6e-202">`FullName` je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="5cd6e-203">Proto má pouze přistupující objekt get a v databázi nebude vygenerován žádný `FullName` sloupec.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="5cd6e-204">Vytvořit entitu instruktora</span><span class="sxs-lookup"><span data-stu-id="5cd6e-204">Create Instructor entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="5cd6e-206">Vytvořte *modely/Instructor. cs*a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="5cd6e-207">Všimněte si, že několik vlastností je stejné v entitách student a instruktor.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="5cd6e-208">V kurzu [Implementace dědičnosti](inheritance.md) níže v této sérii můžete tento kód Refaktorovat, aby se vyloučila redundance.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="5cd6e-209">Můžete umístit více atributů na jeden řádek, takže můžete také zapsat `HireDate` atributy následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="5cd6e-210">Navigační vlastnosti CourseAssignments a OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="5cd6e-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="5cd6e-211">Vlastnosti `CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="5cd6e-212">Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="5cd6e-213">Pokud navigační vlastnost může obsahovat více entit, musí být její typ seznam, ve kterém je možné přidávat, odstraňovat a aktualizovat položky.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="5cd6e-214">Můžete zadat `ICollection<T>` nebo typ, jako je například `List<T>` nebo `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="5cd6e-215">Pokud zadáte `ICollection<T>`, EF vytvoří ve výchozím nastavení kolekci `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="5cd6e-216">Důvod, proč jsou tyto entity `CourseAssignment`, jsou vysvětleny níže v části o relacích n:n.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="5cd6e-217">Obchodní pravidla společnosti Contoso vysokých firem, že instruktor může mít jenom jednu kancelář, takže vlastnost `OfficeAssignment` obsahuje jednu entitu OfficeAssignment (která může být null, pokud není přiřazená žádná sada Office).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="5cd6e-218">Vytvořit entitu OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="5cd6e-218">Create OfficeAssignment entity</span></span>

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="5cd6e-220">Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="5cd6e-221">Klíčový atribut</span><span class="sxs-lookup"><span data-stu-id="5cd6e-221">The Key attribute</span></span>

<span data-ttu-id="5cd6e-222">Mezi instruktorem a entitami OfficeAssignment existuje vztah 1:1 nebo jedna.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="5cd6e-223">Přiřazení kanceláře existuje pouze ve vztahu k instruktorovi, kterému je přiřazeno, a proto jeho primární klíč je také cizí klíč k entitě instruktor.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="5cd6e-224">Entity Framework ale nemůžou automaticky rozpoznat InstructorID jako primární klíč této entity, protože jeho název nedodržuje zásady vytváření názvů classnameID.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="5cd6e-225">Proto atribut `Key` slouží k identifikaci jako klíč:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="5cd6e-226">Atribut `Key` můžete použít také v případě, že má entita svůj vlastní primární klíč, ale chcete vlastnost pojmenovat jinou než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="5cd6e-227">Ve výchozím nastavení EF považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="5cd6e-228">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="5cd6e-228">The Instructor navigation property</span></span>

<span data-ttu-id="5cd6e-229">Entita instruktora má `OfficeAssignment` navigační vlastnost s možnou hodnotou null (protože instruktor nemusí mít přiřazený Office) a entita OfficeAssignment má vlastnost navigace, která nemůže mít hodnotu null `Instructor` (protože přiřazení kanceláře nemůže existovat bez typu instruktor--`InstructorID` nemůže mít hodnotu null).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="5cd6e-230">Pokud má entita instruktora související entitu OfficeAssignment, bude mít Každá entita odkaz na jinou entitu v její navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="5cd6e-231">Můžete umístit atribut `[Required]` do navigační vlastnosti instruktora a určit tak, že se musí jednat o související instruktor, ale nemusíte to dělat, protože `InstructorID` cizí klíč (což je také klíč k této tabulce), který není null.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="5cd6e-232">Upravit entitu kurzu</span><span class="sxs-lookup"><span data-stu-id="5cd6e-232">Modify Course entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="5cd6e-234">V *modelu/Course. cs*nahraďte kód, který jste přidali dříve, následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="5cd6e-235">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="5cd6e-236">Entita kurzu má vlastnost cizího klíče `DepartmentID` která odkazuje na související entitu oddělení a má `Department` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="5cd6e-237">Entity Framework nevyžaduje, abyste do datového modelu přidali vlastnost cizího klíče, když máte vlastnost navigace pro související entitu.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="5cd6e-238">EF v databázi automaticky vytvoří cizí klíče bez ohledu na to, kde jsou potřeba, a vytvoří pro ně [vlastnosti stínu](/ef/core/modeling/shadow-properties) .</span><span class="sxs-lookup"><span data-stu-id="5cd6e-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="5cd6e-239">Ale při používání cizího klíče v datovém modelu může být aktualizace jednodušší a efektivnější.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="5cd6e-240">Když například načtete entitu kurzu, která se má upravit, entita oddělení má hodnotu null, pokud ji nenačtete, takže když aktualizujete entitu kurzu, budete muset nejdřív načíst entitu oddělení.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="5cd6e-241">Pokud je v datovém modelu zahrnutá vlastnost cizího klíče `DepartmentID`, nemusíte před aktualizací načítat entitu oddělení.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="5cd6e-242">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="5cd6e-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="5cd6e-243">Atribut `DatabaseGenerated` s parametrem `None` u vlastnosti `CourseID` určuje, že uživatel bude poskytovat hodnoty primárního klíče, a ne vygenerované databází.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="5cd6e-244">Ve výchozím nastavení Entity Framework předpokládá, že databáze generuje hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="5cd6e-245">To je to, co chcete ve většině scénářů.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="5cd6e-246">U entit kurzu ale použijete uživatelem zadané číslo kurzu, jako je například řada 1000, pro jedno oddělení, 2000 Series pro jiné oddělení a tak dále.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="5cd6e-247">Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot, jako v případě databázových sloupců použitých k záznamu data vytvoření nebo aktualizace řádku.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="5cd6e-248">Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5cd6e-249">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="5cd6e-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="5cd6e-250">Vlastnosti cizího klíče a navigační vlastnosti v entitě kurzu odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="5cd6e-251">Kurz se přiřadí jednomu oddělení, takže je `DepartmentID` cizí klíč a vlastnost navigace `Department` z výše uvedených důvodů.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="5cd6e-252">Kurz může mít zaregistrovaný libovolný počet studentů, takže navigační vlastnost `Enrollments` je kolekce:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="5cd6e-253">Kurz může být výukou více instruktory, takže `CourseAssignments` navigační vlastnost je kolekce (typ `CourseAssignment` je vysvětlen [později](#many-to-many-relationships)):</span><span class="sxs-lookup"><span data-stu-id="5cd6e-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="5cd6e-254">Vytvořit entitu oddělení</span><span class="sxs-lookup"><span data-stu-id="5cd6e-254">Create Department entity</span></span>

![Entita oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="5cd6e-256">Vytvořte *modely/oddělení. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="5cd6e-257">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="5cd6e-257">The Column attribute</span></span>

<span data-ttu-id="5cd6e-258">Dříve jste použili atribut `Column` ke změně mapování názvu sloupce.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="5cd6e-259">V kódu pro entitu oddělení se atribut `Column` používá ke změně mapování datových typů SQL tak, aby byl sloupec definovaný pomocí SQL Server typu peníze v databázi:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="5cd6e-260">Mapování sloupce není obecně vyžadováno, protože Entity Framework zvolí vhodný SQL Server datový typ založený na typu CLR, který definujete pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="5cd6e-261">Typ CLR `decimal` namapuje na typ `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="5cd6e-262">Ale v tomto případě víte, že se ve sloupci budou držet peněžní částky, a datový typ Money je pro to vhodnější.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5cd6e-263">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="5cd6e-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="5cd6e-264">Vlastnosti cizího klíče a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="5cd6e-265">Oddělení může nebo nemusí mít správce a správce je vždy instruktorem.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="5cd6e-266">Proto je vlastnost `InstructorID` obsažena jako cizí klíč pro entitu instruktor a otazník je přidána za označení `int` typu k označení vlastnosti jako Nullable.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="5cd6e-267">Navigační vlastnost má název `Administrator`, ale obsahuje entitu instruktora:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="5cd6e-268">Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="5cd6e-269">Podle konvence Entity Framework umožňuje odstranit Kaskádové odstraňování cizích klíčů, které neumožňují hodnotu null, a pro relace m:n.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="5cd6e-270">Výsledkem může být cyklická kaskádová odstranění pravidel, která způsobí výjimku při pokusu o přidání migrace.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="5cd6e-271">Pokud jste například nedefinovali vlastnost oddělení. InstructorID jako Nullable, EF by nakonfigurovala pravidlo kaskádového odstranění, které odstraní oddělení, když odstraníte instruktora, což nevede k tomu, co chcete mít.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="5cd6e-272">Pokud vaše obchodní pravidla vyžadují, aby vlastnost `InstructorID` nebyla null, je nutné použít následující příkaz rozhraní API Fluent k zakázání kaskádových odstranění v relaci:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="5cd6e-273">Upravit entitu registrace</span><span class="sxs-lookup"><span data-stu-id="5cd6e-273">Modify Enrollment entity</span></span>

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="5cd6e-275">V *modelů/zápisu. cs*nahraďte kód, který jste přidali dříve, pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5cd6e-276">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="5cd6e-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="5cd6e-277">Vlastnosti cizího klíče a vlastnosti navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="5cd6e-278">Záznam zápisu je pro jeden kurz, takže existuje `CourseID` vlastnost cizího klíče a vlastnost navigace `Course`:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="5cd6e-279">Záznam zápisu je určen pro jednoho studenta, takže existuje `StudentID` vlastnost cizího klíče a `Student` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="5cd6e-280">Relace m:n</span><span class="sxs-lookup"><span data-stu-id="5cd6e-280">Many-to-Many relationships</span></span>

<span data-ttu-id="5cd6e-281">Mezi entitami student a Course existuje vztah n:n a entita registrace funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="5cd6e-282">"S datovou částí" znamená, že tabulka zápisu obsahuje další data kromě cizích klíčů pro Spojené tabulky (v tomto případě primární klíč a vlastnost třídy).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="5cd6e-283">Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="5cd6e-284">(Tento diagram byl vygenerován pomocí Entity Framework nástrojů Power Tools pro EF 6. x; vytvoření diagramu není součástí kurzu, stačí ho použít jako ilustraci.)</span><span class="sxs-lookup"><span data-stu-id="5cd6e-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

<span data-ttu-id="5cd6e-286">Každá čára relace má 1 na jednom konci a hvězdičku (\*) na druhé straně, která indikuje relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="5cd6e-287">Pokud tabulka zápisu neobsahuje informace o třídě, musí obsahovat pouze dva cizí klíče CourseID a StudentID.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="5cd6e-288">V takovém případě by to byla tabulka JOIN typu m:n (celá řada) bez datové části (nebo čistá spojovací tabulka) v databázi.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="5cd6e-289">Entity instruktor a Course mají tento druh relace m:n a vaším dalším krokem je vytvoření třídy entity, která bude fungovat jako spojovací tabulka bez datové části.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="5cd6e-290">(EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="5cd6e-291">Další informace najdete [v diskuzi v úložišti GitHub EF Core](https://github.com/aspnet/EntityFramework/issues/1368).)</span><span class="sxs-lookup"><span data-stu-id="5cd6e-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="5cd6e-292">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="5cd6e-292">The CourseAssignment entity</span></span>

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="5cd6e-294">Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="5cd6e-295">Spojování názvů entit</span><span class="sxs-lookup"><span data-stu-id="5cd6e-295">Join entity names</span></span>

<span data-ttu-id="5cd6e-296">V databázi je vyžadována tabulka JOIN pro relaci n:n typu n:1 a musí být reprezentovaná sadou entit...</span><span class="sxs-lookup"><span data-stu-id="5cd6e-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="5cd6e-297">Je běžné pojmenovat entitu JOIN `EntityName1EntityName2`, což by v tomto případě bylo `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="5cd6e-298">Doporučujeme však zvolit název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="5cd6e-299">Modely dat začínají jednoduchým a roste a často se nepoužívají spojení bez datové části, které později načítá datovou část.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="5cd6e-300">Pokud začnete s popisným názvem entity, nebudete muset později změnit název.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="5cd6e-301">V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="5cd6e-302">Například knihy a zákazníci mohou být propojeni pomocí hodnocení.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="5cd6e-303">Pro tento vztah je `CourseAssignment` lepší volbou než `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="5cd6e-304">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="5cd6e-304">Composite key</span></span>

<span data-ttu-id="5cd6e-305">Vzhledem k tomu, že cizí klíče neumožňují hodnotu null a společně jednoznačně identifikují každý řádek tabulky, není nutné používat samostatný primární klíč.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="5cd6e-306">Vlastnosti *InstructorID* a *CourseID* by měly fungovat jako složený primární klíč.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="5cd6e-307">Jediným způsobem, jak identifikovat složené primární klíče k EF, je použití *rozhraní Fluent API* (nemůže být provedeno pomocí atributů).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="5cd6e-308">V další části se dozvíte, jak nakonfigurovat složený primární klíč.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="5cd6e-309">Složený klíč zajišťuje, že i když můžete mít více řádků pro jeden kurz a více řádků pro jednoho instruktora, nemůžete mít pro stejný instruktor a kurz více řádků.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="5cd6e-310">Entita `Enrollment` JOIN definuje svůj vlastní primární klíč, takže je možné duplicity tohoto řazení provést.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="5cd6e-311">Aby tyto duplicity nedocházelo, mohli byste do polí cizího klíče přidat jedinečný index nebo `Enrollment` nakonfigurovat primární složený klíč podobný `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="5cd6e-312">Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="5cd6e-313">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="5cd6e-313">Update the database context</span></span>

<span data-ttu-id="5cd6e-314">Do souboru *data/SchoolContext. cs* přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="5cd6e-315">Tento kód přidá nové entity a nakonfiguruje složený primární klíč entity CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="5cd6e-316">O alternativní rozhraní API Fluent</span><span class="sxs-lookup"><span data-stu-id="5cd6e-316">About a fluent API alternative</span></span>

<span data-ttu-id="5cd6e-317">Kód v metodě `OnModelCreating` třídy `DbContext` používá *rozhraní Fluent API* ke konfiguraci chování EF.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="5cd6e-318">Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu, jako v tomto příkladu v [dokumentaci EF Core](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span><span class="sxs-lookup"><span data-stu-id="5cd6e-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="5cd6e-319">V tomto kurzu používáte rozhraní Fluent API jenom pro mapování databáze, které nemůžete s atributy dělat.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="5cd6e-320">Rozhraní Fluent API ale můžete použít i k určení většiny pravidel formátování, ověřování a mapování, která můžete provádět pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="5cd6e-321">Některé atributy, jako je `MinimumLength`, se nedají použít s rozhraním API Fluent.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="5cd6e-322">Jak již bylo zmíněno dříve, `MinimumLength` nemění schéma, používá pouze pravidlo ověřování na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="5cd6e-323">Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit".</span><span class="sxs-lookup"><span data-stu-id="5cd6e-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="5cd6e-324">V případě potřeby můžete kombinovat atributy a rozhraní API Fluent a existuje několik úprav, které je možné provést jenom pomocí rozhraní Fluent API, ale obecně doporučujeme zvolit jednu z těchto dvou přístupů a použít ji konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="5cd6e-325">Pokud použijete obojí, mějte na paměti, že pokud dojde ke konfliktu, rozhraní Fluent API Přepisuje atributy.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="5cd6e-326">Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="5cd6e-327">Diagram entit znázorňující vztahy</span><span class="sxs-lookup"><span data-stu-id="5cd6e-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="5cd6e-328">Následující ilustrace znázorňuje diagram, který nástroje Entity Framework Power Tools vytvoří pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="5cd6e-330">Kromě řádků relace 1:1 (1 až \*) se tady můžete podívat na řádek relace 1:1 (1 – 0.1) mezi entitami instruktor a OfficeAssignment a řádkem relace 0.. 1 – n mezi entitami instruktora a oddělení (0.. 1 až \*).</span><span class="sxs-lookup"><span data-stu-id="5cd6e-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="5cd6e-331">Počáteční databáze s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="5cd6e-331">Seed database with test data</span></span>

<span data-ttu-id="5cd6e-332">Nahraďte kód v souboru *data/DbInitializer. cs* následujícím kódem, aby bylo možné poskytnout počáteční data pro nově vytvořené entity.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="5cd6e-333">Jak jste viděli v prvním kurzu, většina tohoto kódu jednoduše vytvoří nové objekty entity a načte vzorová data do vlastností podle potřeby pro testování.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="5cd6e-334">Všimněte si, jak jsou zpracovávány relace m:n: kód vytvoří relace vytvořením entit v `Enrollments` a `CourseAssignment` spojí sady entit.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="5cd6e-335">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="5cd6e-335">Add a migration</span></span>

<span data-ttu-id="5cd6e-336">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-336">Save your changes and build the project.</span></span> <span data-ttu-id="5cd6e-337">Pak otevřete příkazové okno ve složce projektu a zadejte příkaz `migrations add` (zatím neprovádějte příkaz Update-Database):</span><span class="sxs-lookup"><span data-stu-id="5cd6e-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="5cd6e-338">Zobrazí se upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="5cd6e-339">Pokud jste se v tomto okamžiku pokusili spustit příkaz `database update`, měli byste obdržet následující chybu:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="5cd6e-340">Příkaz ALTER TABLE je v konfliktu s omezením CIZÍho klíče FK_dbo. Course_dbo. Department_DepartmentID ".</span><span class="sxs-lookup"><span data-stu-id="5cd6e-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="5cd6e-341">Ke konfliktu došlo v databázi "ContosoUniversity", tabulce "dbo". Oddělení ", sloupec" DepartmentID ".</span><span class="sxs-lookup"><span data-stu-id="5cd6e-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="5cd6e-342">Při provádění migrace s existujícími daty je někdy potřeba vložit do databáze zástupná data, aby bylo možné splnit omezení cizího klíče.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="5cd6e-343">Generovaný kód v metodě `Up` přidá do tabulky Course DepartmentID cizí klíč, který nemůže mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="5cd6e-344">Pokud se v tabulce kurzu již nacházejí řádky, když je kód spuštěn, operace `AddColumn` nebude úspěšná, protože SQL Server neví, jakou hodnotu umístit do sloupce, který nemůže mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="5cd6e-345">Pro účely tohoto kurzu spustíte migraci na nové databázi, ale v produkční aplikaci, kterou byste museli udělat, aby migrace zpracovala existující data, takže následující pokyny ukazují příklad toho, jak to udělat.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="5cd6e-346">Pokud chcete, aby tato migrace fungovala se stávajícími daty, je nutné změnit kód tak, aby nový sloupec poskytoval výchozí hodnotu, a vytvořit oddělení zástupných procedur s názvem "Temp", které bude fungovat jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="5cd6e-347">V důsledku toho budou existující řádky kurzu po spuštění metody `Up` v relaci k "dočasnému" oddělení.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="5cd6e-348">Otevřete soubor *{timestamp} _ComplexDataModel. cs* .</span><span class="sxs-lookup"><span data-stu-id="5cd6e-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="5cd6e-349">Odkomentujte řádek kódu, který přidá sloupec DepartmentID do tabulky Course.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="5cd6e-350">Po kódu, který vytvoří tabulku oddělení, přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="5cd6e-351">V produkční aplikaci byste mohli napsat kód nebo skripty pro přidání řádků oddělení a související řádky kurzu pro nové řádky oddělení.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="5cd6e-352">Nebudete už potřebovat "dočasné" oddělení nebo výchozí hodnotu ve sloupci Course. DepartmentID.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="5cd6e-353">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="5cd6e-354">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="5cd6e-354">Change the connection string</span></span>

<span data-ttu-id="5cd6e-355">Nyní máte nový kód ve třídě `DbInitializer`, který přidá počáteční data pro nové entity do prázdné databáze.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="5cd6e-356">Chcete-li, aby EF vytvořil novou prázdnou databázi, změňte název databáze v připojovacím řetězci v souboru *appSettings. JSON* na ContosoUniversity3 nebo na jiný název, který jste nepoužili na počítači, který používáte.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="5cd6e-357">Uložte změnu do souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="5cd6e-358">Jako alternativu ke změně názvu databáze můžete databázi odstranit.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="5cd6e-359">Použijte **Průzkumník objektů systému SQL Server** (SSOX) nebo příkaz `database drop` CLI:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="5cd6e-360">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="5cd6e-360">Update the database</span></span>

<span data-ttu-id="5cd6e-361">Poté, co jste změnili název databáze nebo odstranili databázi, spusťte příkaz `database update` v příkazovém okně a spusťte tak migrace.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="5cd6e-362">Spusťte aplikaci, aby byla metoda `DbInitializer.Initialize` spuštěna a naplnila novou databázi.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="5cd6e-363">Otevřete databázi v SSOX jako dříve a rozbalte uzel **tabulky** , abyste viděli, že se vytvořily všechny tabulky.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="5cd6e-364">(Pokud máte stále SSOX otevřený ze staršího času, klikněte na tlačítko **aktualizovat** .)</span><span class="sxs-lookup"><span data-stu-id="5cd6e-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="5cd6e-366">Spusťte aplikaci, aby aktivovala inicializační kód, který vysemena databáze.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="5cd6e-367">Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data** a ověřte, zda obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="5cd6e-369">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="5cd6e-369">Get the code</span></span>

[<span data-ttu-id="5cd6e-370">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="5cd6e-371">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5cd6e-371">Next steps</span></span>

<span data-ttu-id="5cd6e-372">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="5cd6e-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5cd6e-373">Přizpůsobení datového modelu</span><span class="sxs-lookup"><span data-stu-id="5cd6e-373">Customized the Data model</span></span>
> * <span data-ttu-id="5cd6e-374">Provedli jsme změny entity studenta</span><span class="sxs-lookup"><span data-stu-id="5cd6e-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="5cd6e-375">Vytvořená entita instruktora</span><span class="sxs-lookup"><span data-stu-id="5cd6e-375">Created Instructor entity</span></span>
> * <span data-ttu-id="5cd6e-376">Vytvořila se entita OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="5cd6e-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="5cd6e-377">Upravená entita kurzu</span><span class="sxs-lookup"><span data-stu-id="5cd6e-377">Modified Course entity</span></span>
> * <span data-ttu-id="5cd6e-378">Entita vytvořeného oddělení</span><span class="sxs-lookup"><span data-stu-id="5cd6e-378">Created Department entity</span></span>
> * <span data-ttu-id="5cd6e-379">Upravená entita registrace</span><span class="sxs-lookup"><span data-stu-id="5cd6e-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="5cd6e-380">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="5cd6e-380">Updated the database context</span></span>
> * <span data-ttu-id="5cd6e-381">Dosazení databáze s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="5cd6e-381">Seeded database with test data</span></span>
> * <span data-ttu-id="5cd6e-382">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="5cd6e-382">Added a migration</span></span>
> * <span data-ttu-id="5cd6e-383">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="5cd6e-383">Changed the connection string</span></span>
> * <span data-ttu-id="5cd6e-384">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="5cd6e-384">Updated the database</span></span>

<span data-ttu-id="5cd6e-385">V dalším kurzu se dozvíte víc o tom, jak přistupovat k souvisejícím datům.</span><span class="sxs-lookup"><span data-stu-id="5cd6e-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5cd6e-386">Další: přístup k datům v relaci</span><span class="sxs-lookup"><span data-stu-id="5cd6e-386">Next: Access related data</span></span>](read-related-data.md)
