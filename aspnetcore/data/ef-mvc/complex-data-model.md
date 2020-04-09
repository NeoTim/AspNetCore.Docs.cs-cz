---
title: 'Kurz: Vytvoření komplexního datového modelu - ASP.NET MVC s EF Core'
description: V tomto kurzu přidejte další entity a vztahy a přizpůsobte datový model zadáním pravidel formátování, ověření a mapování.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 91fd09874ecab8bfdb6a38a404faba04aeb73edc
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657428"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="fe66e-103">Kurz: Vytvoření komplexního datového modelu - ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="fe66e-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="fe66e-104">V předchozích kurzech jste pracovali s jednoduchým datovým modelem, který se skládal ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="fe66e-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="fe66e-105">V tomto kurzu přidáte další entity a vztahy a přizpůsobíte datový model zadáním pravidel formátování, ověření a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="fe66e-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="fe66e-106">Po dokončení budou třídy entit tvořit dokončený datový model, který je znázorněn na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="fe66e-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="fe66e-108">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="fe66e-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe66e-109">Přizpůsobení datového modelu</span><span class="sxs-lookup"><span data-stu-id="fe66e-109">Customize the Data model</span></span>
> * <span data-ttu-id="fe66e-110">Provádění změn v entitě Student</span><span class="sxs-lookup"><span data-stu-id="fe66e-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="fe66e-111">Entita Vytvořit instruktora</span><span class="sxs-lookup"><span data-stu-id="fe66e-111">Create Instructor entity</span></span>
> * <span data-ttu-id="fe66e-112">Entita Vytvořit officeassignment</span><span class="sxs-lookup"><span data-stu-id="fe66e-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="fe66e-113">Upravit entitu kurzu</span><span class="sxs-lookup"><span data-stu-id="fe66e-113">Modify Course entity</span></span>
> * <span data-ttu-id="fe66e-114">Entita Vytvořit oddělení</span><span class="sxs-lookup"><span data-stu-id="fe66e-114">Create Department entity</span></span>
> * <span data-ttu-id="fe66e-115">Změnit entitu registrace</span><span class="sxs-lookup"><span data-stu-id="fe66e-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="fe66e-116">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="fe66e-116">Update the database context</span></span>
> * <span data-ttu-id="fe66e-117">Databáze osiva s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="fe66e-117">Seed database with test data</span></span>
> * <span data-ttu-id="fe66e-118">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="fe66e-118">Add a migration</span></span>
> * <span data-ttu-id="fe66e-119">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="fe66e-119">Change the connection string</span></span>
> * <span data-ttu-id="fe66e-120">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="fe66e-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fe66e-121">Požadavky</span><span class="sxs-lookup"><span data-stu-id="fe66e-121">Prerequisites</span></span>

* [<span data-ttu-id="fe66e-122">Použití migrace EF Core</span><span class="sxs-lookup"><span data-stu-id="fe66e-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="fe66e-123">Přizpůsobení datového modelu</span><span class="sxs-lookup"><span data-stu-id="fe66e-123">Customize the Data model</span></span>

<span data-ttu-id="fe66e-124">V této části uvidíte, jak přizpůsobit datový model pomocí atributů, které určují pravidla formátování, ověřování a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="fe66e-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="fe66e-125">Pak v několika z následujících částí vytvoříte kompletní školní datový model přidáním atributů do tříd, které jste již vytvořili, a vytvořením nových tříd pro zbývající typy entit v modelu.</span><span class="sxs-lookup"><span data-stu-id="fe66e-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="fe66e-126">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="fe66e-126">The DataType attribute</span></span>

<span data-ttu-id="fe66e-127">U dat zápisu studenta se na všech webových stránkách aktuálně zobrazuje čas spolu s datem, ačkoli jediné, na čem se zajímáte pro toto pole, je datum.</span><span class="sxs-lookup"><span data-stu-id="fe66e-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="fe66e-128">Pomocí atributů anotace dat můžete provést jednu změnu kódu, která opraví formát zobrazení v každém zobrazení, které zobrazuje data.</span><span class="sxs-lookup"><span data-stu-id="fe66e-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="fe66e-129">Chcete-li zobrazit příklad, jak to udělat, přidáte `EnrollmentDate` atribut do `Student` vlastnosti ve třídě.</span><span class="sxs-lookup"><span data-stu-id="fe66e-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="fe66e-130">V *části Models/Student.cs*přidejte `using` příkaz pro obor `System.ComponentModel.DataAnnotations` názvů a přidejte `DataType` a `DisplayFormat` atributy do `EnrollmentDate` vlastnosti, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fe66e-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="fe66e-131">Atribut `DataType` se používá k určení datového typu, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="fe66e-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="fe66e-132">V tomto případě chceme pouze sledovat datum, ne datum a čas.</span><span class="sxs-lookup"><span data-stu-id="fe66e-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="fe66e-133">`DataType` Výčet poskytuje mnoho datových typů, jako je například datum, čas, telefonní číslo, měna, e-mailová adresa a další.</span><span class="sxs-lookup"><span data-stu-id="fe66e-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="fe66e-134">Atribut `DataType` může také povolit aplikaci automaticky poskytovat funkce specifické pro daný typ.</span><span class="sxs-lookup"><span data-stu-id="fe66e-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="fe66e-135">Lze například `mailto:` vytvořit odkaz `DataType.EmailAddress`pro aplikaci a `DataType.Date` v prohlížečích podporujících html5 lze zadat volič data.</span><span class="sxs-lookup"><span data-stu-id="fe66e-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="fe66e-136">Atribut `DataType` vyzařuje atributy HTML 5 `data-` (vyslovuje se pomlčka dat), kterým prohlížeče HTML 5 rozumějí.</span><span class="sxs-lookup"><span data-stu-id="fe66e-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="fe66e-137">Atributy `DataType` neposkytují žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="fe66e-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="fe66e-138">`DataType.Date`neurčuje formát zobrazeného data.</span><span class="sxs-lookup"><span data-stu-id="fe66e-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="fe66e-139">Ve výchozím nastavení je datové pole zobrazeno podle výchozích formátů založených na serveru CultureInfo.</span><span class="sxs-lookup"><span data-stu-id="fe66e-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="fe66e-140">Atribut `DisplayFormat` se používá k explicitnímu zadání formátu data:</span><span class="sxs-lookup"><span data-stu-id="fe66e-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="fe66e-141">Toto `ApplyFormatInEditMode` nastavení určuje, že formátování by mělo být použito také při zobrazení hodnoty v textovém poli pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="fe66e-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="fe66e-142">(To to možná nebudete chtít u některých polí – například pro hodnoty měny, pravděpodobně nechcete, aby symbol měny v textovém poli pro úpravy.)</span><span class="sxs-lookup"><span data-stu-id="fe66e-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="fe66e-143">Atribut můžete `DisplayFormat` použít samostatně, ale obecně je vhodné použít `DataType` atribut také.</span><span class="sxs-lookup"><span data-stu-id="fe66e-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="fe66e-144">Atribut `DataType` vyjadřuje sémantiku dat na rozdíl od způsobu jejich vykreslení na obrazovce a poskytuje následující výhody, které nezískáte s `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="fe66e-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="fe66e-145">Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolměny odpovídající národnímu prostředí, e-mailové odkazy, některé ověření vstupu na straně klienta atd.).</span><span class="sxs-lookup"><span data-stu-id="fe66e-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="fe66e-146">Ve výchozím nastavení prohlížeč vykreslí data ve správném formátu na základě národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="fe66e-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="fe66e-147">Další informace naleznete v [ \<dokumentaci ke vstupní> značky .](../../mvc/views/working-with-forms.md#the-input-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="fe66e-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="fe66e-148">Spusťte aplikaci, přejděte na stránku Studentindex a všimněte si, že časy se již nezobrazují pro data registrace.</span><span class="sxs-lookup"><span data-stu-id="fe66e-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="fe66e-149">Totéž platí pro všechny pohledy, které používá model Student.</span><span class="sxs-lookup"><span data-stu-id="fe66e-149">The same will be true for any view that uses the Student model.</span></span>

![Studenty index stránku zobrazující data bez časů](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="fe66e-151">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="fe66e-151">The StringLength attribute</span></span>

<span data-ttu-id="fe66e-152">Můžete také zadat pravidla ověření dat a chybové zprávy ověření pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="fe66e-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="fe66e-153">Atribut `StringLength` nastaví maximální délku v databázi a poskytuje ověření na straně klienta a serveru pro ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="fe66e-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="fe66e-154">Můžete také zadat minimální délku řetězce v tomto atributu, ale minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="fe66e-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="fe66e-155">Předpokládejme, že chcete zajistit, aby uživatelé nezadali více než 50 znaků pro název.</span><span class="sxs-lookup"><span data-stu-id="fe66e-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="fe66e-156">Chcete-li přidat `StringLength` toto omezení, přidejte atributy do vlastností `LastName` a, `FirstMidName` jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fe66e-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="fe66e-157">Atribut `StringLength` nezabrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="fe66e-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="fe66e-158">Atribut můžete `RegularExpression` použít omezení pro vstup.</span><span class="sxs-lookup"><span data-stu-id="fe66e-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="fe66e-159">Například následující kód vyžaduje, aby první znak byl velkými písmeny a zbývající znaky byly abecední:</span><span class="sxs-lookup"><span data-stu-id="fe66e-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="fe66e-160">Atribut `MaxLength` poskytuje funkce podobné `StringLength` atributu, ale neposkytuje ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="fe66e-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="fe66e-161">Model databáze se nyní změnil způsobem, který vyžaduje změnu ve schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="fe66e-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="fe66e-162">Migrace budete používat k aktualizaci schématu bez ztráty dat, která jste přidali do databáze pomocí uj.</span><span class="sxs-lookup"><span data-stu-id="fe66e-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="fe66e-163">Uložte změny a vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="fe66e-163">Save your changes and build the project.</span></span> <span data-ttu-id="fe66e-164">Potom otevřete příkazové okno ve složce projektu a zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="fe66e-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="fe66e-165">Příkaz `migrations add` varuje, že může dojít ke ztrátě dat, protože změna zkracuje maximální délku pro dva sloupce.</span><span class="sxs-lookup"><span data-stu-id="fe66e-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="fe66e-166">Migrace vytvoří soubor s názvem \* \<timeStamp>_MaxLengthOnNames.cs\*.</span><span class="sxs-lookup"><span data-stu-id="fe66e-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="fe66e-167">Tento soubor obsahuje `Up` kód v metodě, která bude aktualizovat databázi tak, aby odpovídala aktuálnímu datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="fe66e-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="fe66e-168">Příkaz `database update` spustil tento kód.</span><span class="sxs-lookup"><span data-stu-id="fe66e-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="fe66e-169">Předponou časového razítka k názvu souboru migrace framework používá k objednání migrace.</span><span class="sxs-lookup"><span data-stu-id="fe66e-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="fe66e-170">Před spuštěním příkazu aktualizovat databázi můžete vytvořit více migrace a potom všechny migrace budou použity v pořadí, ve kterém byly vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="fe66e-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="fe66e-171">Spusťte aplikaci, vyberte kartu **Studenti,** klikněte na **Vytvořit nový**a zkuste zadat jedno jméno delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="fe66e-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="fe66e-172">Aplikace by vám měla zabránit v tom.</span><span class="sxs-lookup"><span data-stu-id="fe66e-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="fe66e-173">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="fe66e-173">The Column attribute</span></span>

<span data-ttu-id="fe66e-174">Atributy můžete také určit, jak jsou vaše třídy a vlastnosti mapovány do databáze.</span><span class="sxs-lookup"><span data-stu-id="fe66e-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="fe66e-175">Předpokládejme, že `FirstMidName` jste použili název pole křestního jména, protože toto pole může také obsahovat prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="fe66e-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="fe66e-176">Chcete však, aby byl `FirstName`sloupec databáze pojmenován , protože uživatelé, kteří budou psát dotazy ad hoc proti databázi, jsou na tento název zvyklí.</span><span class="sxs-lookup"><span data-stu-id="fe66e-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="fe66e-177">Chcete-li toto mapování, `Column` můžete použít atribut.</span><span class="sxs-lookup"><span data-stu-id="fe66e-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="fe66e-178">Atribut `Column` určuje, že při vytvoření databáze bude `Student` pojmenován `FirstMidName` `FirstName`sloupec tabulky, která se mapuje na vlastnost .</span><span class="sxs-lookup"><span data-stu-id="fe66e-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="fe66e-179">Jinými slovy, pokud váš `Student.FirstMidName`kód odkazuje na , data budou `FirstName` pocházet `Student` z nebo aktualizovat ve sloupci tabulky.</span><span class="sxs-lookup"><span data-stu-id="fe66e-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="fe66e-180">Pokud nezadáte názvy sloupců, budou mít stejný název jako název vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="fe66e-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="fe66e-181">V *souboru Student.cs* `using` přidejte `System.ComponentModel.DataAnnotations.Schema` příkaz pro `FirstMidName` vlastnost a přidejte atribut název sloupce, jak je znázorněno v následujícím zvýrazněném kódu:</span><span class="sxs-lookup"><span data-stu-id="fe66e-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="fe66e-182">Přidání atributu `Column` změní model, `SchoolContext`který podporuje , takže se nebude shodovat s databází.</span><span class="sxs-lookup"><span data-stu-id="fe66e-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="fe66e-183">Uložte změny a vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="fe66e-183">Save your changes and build the project.</span></span> <span data-ttu-id="fe66e-184">Potom otevřete příkazové okno ve složce projektu a zadejte následující příkazy pro vytvoření další migrace:</span><span class="sxs-lookup"><span data-stu-id="fe66e-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="fe66e-185">V **Průzkumníku objektů serveru SQL Server**otevřete návrháře tabulek Student poklepáním na tabulku **Student.**</span><span class="sxs-lookup"><span data-stu-id="fe66e-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů ve SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="fe66e-187">Před použitím prvních dvou migrací byly sloupce názvů typu nvarchar(MAX).</span><span class="sxs-lookup"><span data-stu-id="fe66e-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="fe66e-188">Nyní jsou nvarchar(50) a název sloupce se změnil z FirstMidName na FirstName.</span><span class="sxs-lookup"><span data-stu-id="fe66e-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="fe66e-189">Pokud se pokusíte zkompilovat před dokončením vytváření všech tříd entit v následujících částech, může dojít k chybám kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="fe66e-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="fe66e-190">Změny studentské entity</span><span class="sxs-lookup"><span data-stu-id="fe66e-190">Changes to Student entity</span></span>

![Studentská entita](complex-data-model/_static/student-entity.png)

<span data-ttu-id="fe66e-192">V *části Models/Student.cs*nahraďte kód, který jste přidali dříve, následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="fe66e-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="fe66e-193">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="fe66e-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="fe66e-194">Atribut Povinné</span><span class="sxs-lookup"><span data-stu-id="fe66e-194">The Required attribute</span></span>

<span data-ttu-id="fe66e-195">Atribut `Required` vytvoří pole vlastností názvu jako povinná.</span><span class="sxs-lookup"><span data-stu-id="fe66e-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="fe66e-196">Atribut `Required` není potřeba pro typy s nulou, jako jsou typy hodnot (DateTime, int, double, float atd.).</span><span class="sxs-lookup"><span data-stu-id="fe66e-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="fe66e-197">Typy, které nemohou být nulové, jsou automaticky považovány za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="fe66e-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="fe66e-198">Atribut `Required` musí být `MinimumLength` použit `MinimumLength` s pro vynucení.</span><span class="sxs-lookup"><span data-stu-id="fe66e-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="fe66e-199">Atribut Zobrazit</span><span class="sxs-lookup"><span data-stu-id="fe66e-199">The Display attribute</span></span>

<span data-ttu-id="fe66e-200">Atribut `Display` určuje, že titulek pro textová pole by měl být "Jméno", "Příjmení", "Celé jméno" a "Datum zápisu" namísto názvu vlastnosti v každé instanci (která nemá prostor dělení slov).</span><span class="sxs-lookup"><span data-stu-id="fe66e-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="fe66e-201">Vypočtená vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="fe66e-201">The FullName calculated property</span></span>

<span data-ttu-id="fe66e-202">`FullName`je vypočtená vlastnost, která vrací hodnotu vytvořenou zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="fe66e-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="fe66e-203">Proto má pouze get přistupujícího oru a žádný `FullName` sloupec bude generován v databázi.</span><span class="sxs-lookup"><span data-stu-id="fe66e-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="fe66e-204">Entita Vytvořit instruktora</span><span class="sxs-lookup"><span data-stu-id="fe66e-204">Create Instructor entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="fe66e-206">Vytvořte *models/instructor.cs*a nahrazujte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="fe66e-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="fe66e-207">Všimněte si, že několik vlastností jsou stejné v studentských a instruktor subjekty.</span><span class="sxs-lookup"><span data-stu-id="fe66e-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="fe66e-208">V [kurzě implementace dědičnosti](inheritance.md) dále v této řadě refaktorujete tento kód, abyste eliminovali redundanci.</span><span class="sxs-lookup"><span data-stu-id="fe66e-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="fe66e-209">Na jeden řádek můžete umístit více atributů, `HireDate` takže můžete také zapsat atributy následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="fe66e-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="fe66e-210">Vlastnosti navigace CourseAssignments a OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="fe66e-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="fe66e-211">`CourseAssignments` Vlastnosti `OfficeAssignment` a jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="fe66e-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="fe66e-212">Instruktor může vyučovat `CourseAssignments` libovolný počet kurzů, takže je definován jako sbírka.</span><span class="sxs-lookup"><span data-stu-id="fe66e-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="fe66e-213">Pokud navigační vlastnost může obsahovat více entit, musí být jejím typem seznam, ve kterém lze položky přidávat, odstraňovat a aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="fe66e-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="fe66e-214">Můžete zadat `ICollection<T>` nebo typ, `List<T>` `HashSet<T>`například nebo .</span><span class="sxs-lookup"><span data-stu-id="fe66e-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="fe66e-215">Pokud zadáte `ICollection<T>`, EF `HashSet<T>` vytvoří kolekci ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="fe66e-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="fe66e-216">Důvod, proč `CourseAssignment` se jedná o entity, je vysvětlen níže v části o vztazích N:N.</span><span class="sxs-lookup"><span data-stu-id="fe66e-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="fe66e-217">Obchodní pravidla Contoso University uvádějí, že instruktor může mít `OfficeAssignment` maximálně jednu kancelář, takže vlastnost obsahuje jednu entitu OfficeAssignment (která může být nulová, pokud není přiřazena žádná kancelář).</span><span class="sxs-lookup"><span data-stu-id="fe66e-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="fe66e-218">Entita Vytvořit officeassignment</span><span class="sxs-lookup"><span data-stu-id="fe66e-218">Create OfficeAssignment entity</span></span>

![Entita OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="fe66e-220">Vytvořit *modely/OfficeAssignment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="fe66e-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="fe66e-221">Atribut Key</span><span class="sxs-lookup"><span data-stu-id="fe66e-221">The Key attribute</span></span>

<span data-ttu-id="fe66e-222">Existuje vztah 1:0 nebo jeden mezi entitami Instruktor a OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="fe66e-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="fe66e-223">Přiřazení kanceláře existuje pouze ve vztahu k instruktorovi, ke kterým je přiřazen, a proto je jeho primárním klíčem také jeho cizí klíč k entitě Instruktor.</span><span class="sxs-lookup"><span data-stu-id="fe66e-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="fe66e-224">Ale entity Framework nelze automaticky rozpoznat InstructorID jako primární klíč této entity, protože jeho název nedodržuje ID nebo classnameID konvence pojmenování.</span><span class="sxs-lookup"><span data-stu-id="fe66e-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="fe66e-225">Proto `Key` atribut slouží k identifikaci jako klíč:</span><span class="sxs-lookup"><span data-stu-id="fe66e-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="fe66e-226">`Key` Atribut můžete také použít, pokud entita má svůj vlastní primární klíč, ale chcete pojmenovat vlastnost jinak než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="fe66e-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="fe66e-227">Ve výchozím nastavení ef zachází s klíčem jako s nedatabasegenerovaným, protože sloupec je určen pro identifikační vztah.</span><span class="sxs-lookup"><span data-stu-id="fe66e-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="fe66e-228">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="fe66e-228">The Instructor navigation property</span></span>

<span data-ttu-id="fe66e-229">Entita Instruktor má `OfficeAssignment` vlastnost navigace s možnou hodnotou null (protože instruktor nemusí mít `Instructor` přiřazení kanceláře) a entita OfficeAssignment má `InstructorID` navigační vlastnost s nenulovou hodnotou (protože přiřazení kanceláře nemůže existovat bez instruktora – je nenulové).</span><span class="sxs-lookup"><span data-stu-id="fe66e-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="fe66e-230">Pokud má entita Instruktor související entitu OfficeAssignment, každá entita bude mít odkaz na druhou ve své navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="fe66e-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="fe66e-231">Můžete umístit `[Required]` atribut na instruktornavigační vlastnost určit, že musí být související instruktor, ale není `InstructorID` nutné provést, protože cizí klíč (který je také klíčem k této tabulce) je nenulovatelný.</span><span class="sxs-lookup"><span data-stu-id="fe66e-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="fe66e-232">Upravit entitu kurzu</span><span class="sxs-lookup"><span data-stu-id="fe66e-232">Modify Course entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="fe66e-234">V *části Models/Course.cs*nahraďte kód, který jste přidali dříve, následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="fe66e-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="fe66e-235">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="fe66e-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="fe66e-236">Entita kurzu má `DepartmentID` vlastnost cizího klíče, která odkazuje `Department` na související entitu oddělení a má navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="fe66e-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="fe66e-237">Entity Framework nevyžaduje přidání vlastnosti cizího klíče do datového modelu, pokud máte vlastnost navigace pro související entitu.</span><span class="sxs-lookup"><span data-stu-id="fe66e-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="fe66e-238">EF automaticky vytvoří cizí klíče v databázi všude tam, kde jsou potřeba, a vytvoří stínové [vlastnosti](/ef/core/modeling/shadow-properties) pro ně.</span><span class="sxs-lookup"><span data-stu-id="fe66e-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="fe66e-239">Ale s cizí klíč v datovém modelu může aktualizace jednodušší a efektivnější.</span><span class="sxs-lookup"><span data-stu-id="fe66e-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="fe66e-240">Například při načtení entity kurzu k úpravám, entity oddělení je null, pokud nechcete načíst, takže při aktualizaci entity kurzu, budete muset nejprve načíst entity oddělení.</span><span class="sxs-lookup"><span data-stu-id="fe66e-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="fe66e-241">Pokud je vlastnost `DepartmentID` cizího klíče zahrnuta v datovém modelu, není nutné před aktualizací načítat entitu Oddělení.</span><span class="sxs-lookup"><span data-stu-id="fe66e-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="fe66e-242">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="fe66e-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="fe66e-243">Atribut `DatabaseGenerated` s `None` parametrem `CourseID` na vlastnost určuje, že hodnoty primárního klíče jsou poskytovány uživatelem, nikoli generovány databází.</span><span class="sxs-lookup"><span data-stu-id="fe66e-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="fe66e-244">Ve výchozím nastavení entity Framework předpokládá, že hodnoty primárního klíče jsou generovány databází.</span><span class="sxs-lookup"><span data-stu-id="fe66e-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="fe66e-245">To je to, co chcete ve většině scénářů.</span><span class="sxs-lookup"><span data-stu-id="fe66e-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="fe66e-246">Pro entity kurzu však použijete uživatelem zadané číslo kurzu, například řadu 1000 pro jedno oddělení, řadu 2000 pro jiné oddělení a tak dále.</span><span class="sxs-lookup"><span data-stu-id="fe66e-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="fe66e-247">Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot, jako v případě sloupců databáze používaných k zaznamenání data vytvoření nebo aktualizace řádku.</span><span class="sxs-lookup"><span data-stu-id="fe66e-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="fe66e-248">Další informace naleznete v tématu [Generated Properties](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="fe66e-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="fe66e-249">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="fe66e-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="fe66e-250">Vlastnosti cizího klíče a navigační vlastnosti v entitě Course odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="fe66e-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="fe66e-251">Kurz je přiřazen k jednomu oddělení, `DepartmentID` takže z `Department` výše uvedených důvodů existuje cizí klíč a navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="fe66e-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="fe66e-252">Kurz může mít libovolný počet studentů zapsaných `Enrollments` v něm, takže navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="fe66e-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="fe66e-253">Kurz může být vyučován `CourseAssignments` více instruktory, takže `CourseAssignment` navigační vlastnost je kolekce (typ je vysvětlen [později](#many-to-many-relationships)):</span><span class="sxs-lookup"><span data-stu-id="fe66e-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="fe66e-254">Entita Vytvořit oddělení</span><span class="sxs-lookup"><span data-stu-id="fe66e-254">Create Department entity</span></span>

![Subjekt oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="fe66e-256">Vytvořit *modely/Department.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="fe66e-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="fe66e-257">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="fe66e-257">The Column attribute</span></span>

<span data-ttu-id="fe66e-258">Dříve jste `Column` atribut použili ke změně mapování názvů sloupců.</span><span class="sxs-lookup"><span data-stu-id="fe66e-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="fe66e-259">V kódu entity Oddělení se `Column` atribut používá ke změně mapování datového typu SQL tak, aby byl sloupec definován pomocí typu peněz serveru SQL Server v databázi:</span><span class="sxs-lookup"><span data-stu-id="fe66e-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="fe66e-260">Mapování sloupců není obecně vyžadováno, protože entity Framework zvolí příslušný datový typ serveru SQL Server na základě typu CLR, který definujete pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="fe66e-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="fe66e-261">Typ CLR `decimal` se mapuje `decimal` na typ serveru SQL Server.</span><span class="sxs-lookup"><span data-stu-id="fe66e-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="fe66e-262">Ale v tomto případě víte, že sloupec bude držet částky měny a datový typ peněz je pro to vhodnější.</span><span class="sxs-lookup"><span data-stu-id="fe66e-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="fe66e-263">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="fe66e-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="fe66e-264">Vlastnosti cizího klíče a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="fe66e-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="fe66e-265">Oddělení může nebo nemusí mít správce a správce je vždy instruktor.</span><span class="sxs-lookup"><span data-stu-id="fe66e-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="fe66e-266">Proto `InstructorID` je vlastnost zahrnuta jako cizí klíč k entitě Instruktor `int` a otazník je přidán za označení typu k označení vlastnosti jako nullable.</span><span class="sxs-lookup"><span data-stu-id="fe66e-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="fe66e-267">Vlastnost navigace je `Administrator` pojmenována, ale obsahuje entitu Instruktor:</span><span class="sxs-lookup"><span data-stu-id="fe66e-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="fe66e-268">Oddělení může mít mnoho kurzů, takže je navigační vlastnost Kurzů:</span><span class="sxs-lookup"><span data-stu-id="fe66e-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="fe66e-269">Podle konvence entity framework umožňuje kaskádové odstranění pro cizí klíče s možnou neplatnou a pro vztahy N:N.</span><span class="sxs-lookup"><span data-stu-id="fe66e-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="fe66e-270">To může mít za následek cyklické kaskády odstranit pravidla, která způsobí výjimku při pokusu o přidání migrace.</span><span class="sxs-lookup"><span data-stu-id="fe66e-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="fe66e-271">Například pokud jste nedefinovali Department.InstructorID vlastnost jako nullable, EF by nakonfigurovat pravidlo kaskádového odstranění odstranit oddělení při odstranění instruktora, což není to, co chcete, aby se stalo.</span><span class="sxs-lookup"><span data-stu-id="fe66e-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="fe66e-272">Pokud vaše obchodní `InstructorID` pravidla vyžadovala, aby vlastnost byla nenulovatelná, budete muset použít následující příkaz fluent API k zakázání kaskádového odstranění ve vztahu:</span><span class="sxs-lookup"><span data-stu-id="fe66e-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="fe66e-273">Změnit entitu registrace</span><span class="sxs-lookup"><span data-stu-id="fe66e-273">Modify Enrollment entity</span></span>

![Entita zápisu](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="fe66e-275">V *části Modely/Enrollment.cs*nahraďte kód, který jste přidali dříve, následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="fe66e-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="fe66e-276">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="fe66e-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="fe66e-277">Vlastnosti cizího klíče a navigační vlastnosti odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="fe66e-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="fe66e-278">Záznam zápisu je pro jeden kurz, `CourseID` takže je cizí `Course` klíč vlastnost a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="fe66e-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="fe66e-279">Záznam zápisu je pro jednoho studenta, `StudentID` takže je `Student` cizí klíč vlastnost a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="fe66e-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="fe66e-280">Vztahy N:N</span><span class="sxs-lookup"><span data-stu-id="fe66e-280">Many-to-Many relationships</span></span>

<span data-ttu-id="fe66e-281">Existuje vztah N:N mezi entitami Student a Kurz a entita Zápis funguje jako tabulka spojení N:N *s datovou částí* v databázi.</span><span class="sxs-lookup"><span data-stu-id="fe66e-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="fe66e-282">"S datovou částí" znamená, že tabulka Registrace obsahuje další data kromě cizích klíčů pro spojené tabulky (v tomto případě primární klíč a vlastnost Grade).</span><span class="sxs-lookup"><span data-stu-id="fe66e-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="fe66e-283">Následující obrázek znázorňuje, jak tyto vztahy vypadají v diagramu entity.</span><span class="sxs-lookup"><span data-stu-id="fe66e-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="fe66e-284">(Tento diagram byl vygenerován pomocí entity framework elektrické nástroje pro EF 6.x; vytvoření diagramu není součástí kurzu, je to jen používá zde jako ilustrace.)</span><span class="sxs-lookup"><span data-stu-id="fe66e-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Student-Kurz mnoho k mnoha vztahům](complex-data-model/_static/student-course.png)

<span data-ttu-id="fe66e-286">Každá čára vztahu má 1 na jednom konci a hvězdičku (\*) na straně druhé, označující vztah 1:N.</span><span class="sxs-lookup"><span data-stu-id="fe66e-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="fe66e-287">Pokud tabulka Zápis neobsahuje informace o hodnocení, musí obsahovat pouze dva cizí klíče CourseID a StudentID.</span><span class="sxs-lookup"><span data-stu-id="fe66e-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="fe66e-288">V takovém případě by se spojit tabulka N:N bez datové části (nebo čisté tabulky spojení) v databázi.</span><span class="sxs-lookup"><span data-stu-id="fe66e-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="fe66e-289">Entity Instruktora a kurzu mají tento druh relace N:N a dalším krokem je vytvoření třídy entity, která bude fungovat jako tabulka spojení bez datové části.</span><span class="sxs-lookup"><span data-stu-id="fe66e-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="fe66e-290">(EF 6.x podporuje implicitní spojit tabulky pro relace N:N, ale EF Core není.</span><span class="sxs-lookup"><span data-stu-id="fe66e-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="fe66e-291">Další informace naleznete [v diskusi v úložišti EF Core GitHub](https://github.com/aspnet/EntityFramework/issues/1368).)</span><span class="sxs-lookup"><span data-stu-id="fe66e-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="fe66e-292">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="fe66e-292">The CourseAssignment entity</span></span>

![Entita CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="fe66e-294">Vytvořit *modely/CourseAssignment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="fe66e-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="fe66e-295">Spojit názvy entit</span><span class="sxs-lookup"><span data-stu-id="fe66e-295">Join entity names</span></span>

<span data-ttu-id="fe66e-296">Tabulka spojení je vyžadována v databázi pro vztah Instruktor-kurzy N:N a musí být reprezentována sadou entit.</span><span class="sxs-lookup"><span data-stu-id="fe66e-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="fe66e-297">Je běžné pojmenovat entitu `EntityName1EntityName2`spojení , která `CourseInstructor`by v tomto případě byla .</span><span class="sxs-lookup"><span data-stu-id="fe66e-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="fe66e-298">Doporučujeme však zvolit název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="fe66e-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="fe66e-299">Datové modely začínají jednoduché a rostou, s no-datové části připojí často získat datové části později.</span><span class="sxs-lookup"><span data-stu-id="fe66e-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="fe66e-300">Pokud začnete s popisným názvem entity, nebudete muset později název měnit.</span><span class="sxs-lookup"><span data-stu-id="fe66e-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="fe66e-301">V ideálním případě by entita spojení měla svůj vlastní přirozený název (případně jedno slovo) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="fe66e-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="fe66e-302">Knihy a zákazníci mohou být například propojeni prostřednictvím hodnocení.</span><span class="sxs-lookup"><span data-stu-id="fe66e-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="fe66e-303">Pro tento `CourseAssignment` vztah, je `CourseInstructor`lepší volbou než .</span><span class="sxs-lookup"><span data-stu-id="fe66e-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="fe66e-304">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="fe66e-304">Composite key</span></span>

<span data-ttu-id="fe66e-305">Vzhledem k tomu, že cizí klíče nelze utajet s platností a společně jednoznačně identifikují každý řádek tabulky, není nutné samostatný primární klíč.</span><span class="sxs-lookup"><span data-stu-id="fe66e-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="fe66e-306">Vlastnosti *InstructorID* a *CourseID* by měly fungovat jako složený primární klíč.</span><span class="sxs-lookup"><span data-stu-id="fe66e-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="fe66e-307">Jediný způsob, jak identifikovat složené primární klíče ef je pomocí *fluent API* (nelze provést pomocí atributů).</span><span class="sxs-lookup"><span data-stu-id="fe66e-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="fe66e-308">V další části uvidíte, jak nakonfigurovat složený primární klíč.</span><span class="sxs-lookup"><span data-stu-id="fe66e-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="fe66e-309">Složený klíč zajišťuje, že zatímco můžete mít více řádků pro jeden kurz a více řádků pro jednoho instruktora, nemůžete mít více řádků pro stejného instruktora a kurz.</span><span class="sxs-lookup"><span data-stu-id="fe66e-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="fe66e-310">Entita `Enrollment` spojení definuje svůj vlastní primární klíč, takže jsou možné duplikáty tohoto druhu.</span><span class="sxs-lookup"><span data-stu-id="fe66e-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="fe66e-311">Chcete-li těmto duplikátům zabránit, můžete do `Enrollment` polí cizího klíče `CourseAssignment`přidat jedinečný index nebo nakonfigurovat pomocí primárního složeného klíče podobného .</span><span class="sxs-lookup"><span data-stu-id="fe66e-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="fe66e-312">Další informace naleznete v tématu [Indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="fe66e-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="fe66e-313">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="fe66e-313">Update the database context</span></span>

<span data-ttu-id="fe66e-314">Do souboru *Data/SchoolContext.cs* přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="fe66e-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="fe66e-315">Tento kód přidá nové entity a nakonfiguruje složený primární klíč entity CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="fe66e-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="fe66e-316">O plynulé alternativě rozhraní API</span><span class="sxs-lookup"><span data-stu-id="fe66e-316">About a fluent API alternative</span></span>

<span data-ttu-id="fe66e-317">Kód v `OnModelCreating` metodě `DbContext` třídy používá *plynulé rozhraní API* ke konfiguraci chování EF.</span><span class="sxs-lookup"><span data-stu-id="fe66e-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="fe66e-318">Rozhraní API se nazývá "plynulý", protože se často používá navlékáním řady volání metod do jednoho příkazu, jako v tomto příkladu z [dokumentace EF Core](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span><span class="sxs-lookup"><span data-stu-id="fe66e-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="fe66e-319">V tomto kurzu používáte plynulé rozhraní API pouze pro mapování databáze, které nelze provést s atributy.</span><span class="sxs-lookup"><span data-stu-id="fe66e-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="fe66e-320">Pomocí rozhraní FLUENT API však můžete také určit většinu pravidel formátování, ověřování a mapování, která můžete provést pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="fe66e-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="fe66e-321">Některé atributy, `MinimumLength` jako je například nelze použít s plynulý rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="fe66e-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="fe66e-322">Jak již bylo `MinimumLength` zmíněno dříve, nezmění schéma, použije pouze ověřovací pravidlo na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="fe66e-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="fe66e-323">Někteří vývojáři dávají přednost použití rozhraní FLUENT API výhradně tak, aby mohli zachovat své třídy entit "čisté".</span><span class="sxs-lookup"><span data-stu-id="fe66e-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="fe66e-324">Můžete kombinovat atributy a plynulé rozhraní API, pokud chcete, a existuje několik úprav, které lze provést pouze pomocí fluent API, ale obecně doporučujeme zvolit jeden z těchto dvou přístupů a používat konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="fe66e-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="fe66e-325">Pokud používáte obojí, všimněte si, že všude tam, kde dojde ke konfliktu, přepíše atributy Fluent API.</span><span class="sxs-lookup"><span data-stu-id="fe66e-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="fe66e-326">Další informace o atributech vs. fluent API naleznete v [tématu Metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="fe66e-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="fe66e-327">Diagram entit znázorňující vztahy</span><span class="sxs-lookup"><span data-stu-id="fe66e-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="fe66e-328">Následující obrázek znázorňuje diagram, který vytvoří nástroje Entity Framework Power Tools pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="fe66e-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="fe66e-330">Kromě linek vztahu 1:N (1 až \*), zde můžete vidět řádek vztahu 1:0 nebo jeden (1 až 0..1) mezi entitami Instruktor a OfficeAssignment a řádek vztahu nula nebo jeden na mnoho (0..1 až \*) mezi entitami Instruktor a Oddělení.</span><span class="sxs-lookup"><span data-stu-id="fe66e-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="fe66e-331">Databáze osiva s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="fe66e-331">Seed database with test data</span></span>

<span data-ttu-id="fe66e-332">Nahraďte kód v souboru *Data/DbInitializer.cs* následujícím kódem, abyste poskytli počáteční data pro nové entity, které jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="fe66e-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="fe66e-333">Jak jste viděli v prvním kurzu, většina tohoto kódu jednoduše vytvoří nové entity objekty a načte ukázková data do vlastností, jak je požadováno pro testování.</span><span class="sxs-lookup"><span data-stu-id="fe66e-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="fe66e-334">Všimněte si, jak jsou zpracovány relace N:N: kód `Enrollments` vytváří `CourseAssignment` vztahy vytvořením entit v sadách entit a spojit je.</span><span class="sxs-lookup"><span data-stu-id="fe66e-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="fe66e-335">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="fe66e-335">Add a migration</span></span>

<span data-ttu-id="fe66e-336">Uložte změny a vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="fe66e-336">Save your changes and build the project.</span></span> <span data-ttu-id="fe66e-337">Potom otevřete příkazové okno ve `migrations add` složce projektu a zadejte příkaz (zatím neprovázte příkaz aktualizovat databázi):</span><span class="sxs-lookup"><span data-stu-id="fe66e-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="fe66e-338">Dostanete upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="fe66e-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="fe66e-339">Pokud jste se `database update` pokusili spustit příkaz v tomto okamžiku (ještě to nedělají), zobrazí se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="fe66e-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="fe66e-340">Příkaz ALTER TABLE je v konfliktu s omezením "FK_dbo cizí klíč. Course_dbo. Department_DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="fe66e-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="fe66e-341">Ke konfliktu došlo v databázi "ContosoUniversity", tabulka "dbo. Department", sloupec "DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="fe66e-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="fe66e-342">Někdy při provádění migrace s existujícími daty je třeba vložit data se zakázaným inzerováním do databáze, aby byla splněna omezení cizího klíče.</span><span class="sxs-lookup"><span data-stu-id="fe66e-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="fe66e-343">Generovaný kód `Up` v metodě přidá cizí klíč DepartmentID, který není možné udát, do tabulky Kurz.</span><span class="sxs-lookup"><span data-stu-id="fe66e-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="fe66e-344">Pokud již existují řádky v tabulce kurz při `AddColumn` spuštění kódu, operace se nezdaří, protože SQL Server neví, jakou hodnotu umístit do sloupce, který nemůže být null.</span><span class="sxs-lookup"><span data-stu-id="fe66e-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="fe66e-345">Pro účely tohoto kurzu spustíte migraci v nové databázi, ale v produkční aplikaci budete muset provést migraci zpracovat existující data, takže následující pokyny ukazují příklad, jak to udělat.</span><span class="sxs-lookup"><span data-stu-id="fe66e-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="fe66e-346">Chcete-li, aby tato migrace fungovala s existujícími daty, musíte změnit kód, aby nový sloupec měl výchozí hodnotu, a vytvořit oddělení se zakázaným inzerováním s názvem "Temp", které bude fungovat jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="fe66e-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="fe66e-347">V důsledku toho existující řádky kurzu budou všechny souviset `Up` s oddělením "Temp" po spuštění metody.</span><span class="sxs-lookup"><span data-stu-id="fe66e-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="fe66e-348">Otevřete soubor *{timestamp}_ComplexDataModel.cs.*</span><span class="sxs-lookup"><span data-stu-id="fe66e-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="fe66e-349">Zakomentujte řádek kódu, který přidá sloupec DepartmentID do tabulky Kurz.</span><span class="sxs-lookup"><span data-stu-id="fe66e-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="fe66e-350">Za kód, který vytvoří tabulku Oddělení, přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="fe66e-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="fe66e-351">V produkční aplikaci byste napsali kód nebo skripty pro přidání řádků oddělení a propojovali řádky kurzu s novými řádky oddělení.</span><span class="sxs-lookup"><span data-stu-id="fe66e-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="fe66e-352">Potom byste již nepotřebovali oddělení "Temp" nebo výchozí hodnotu ve sloupci Course.DepartmentID.</span><span class="sxs-lookup"><span data-stu-id="fe66e-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="fe66e-353">Uložte změny a vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="fe66e-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="fe66e-354">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="fe66e-354">Change the connection string</span></span>

<span data-ttu-id="fe66e-355">Nyní máte nový kód `DbInitializer` ve třídě, která přidá data osiva pro nové entity do prázdné databáze.</span><span class="sxs-lookup"><span data-stu-id="fe66e-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="fe66e-356">Chcete-li ef vytvořit novou prázdnou databázi, změňte název databáze v připojovacím řetězci v *appsettings.json* na ContosoUniversity3 nebo jiný název, který jste nepoužili v počítači, který používáte.</span><span class="sxs-lookup"><span data-stu-id="fe66e-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="fe66e-357">Uložte změnu na *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fe66e-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="fe66e-358">Jako alternativu ke změně názvu databáze můžete databázi odstranit.</span><span class="sxs-lookup"><span data-stu-id="fe66e-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="fe66e-359">Použijte **Průzkumník objektů SERVERU SQL** Server `database drop` (SSOX) nebo příkaz cli:</span><span class="sxs-lookup"><span data-stu-id="fe66e-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="fe66e-360">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="fe66e-360">Update the database</span></span>

<span data-ttu-id="fe66e-361">Po změně názvu databáze nebo odstranění databáze `database update` spusťte příkaz v příkazovém okně a proveďte migrace.</span><span class="sxs-lookup"><span data-stu-id="fe66e-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="fe66e-362">Spusťte aplikaci `DbInitializer.Initialize` způsobit metodu ke spuštění a naplnění nové databáze.</span><span class="sxs-lookup"><span data-stu-id="fe66e-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="fe66e-363">Otevřete databázi ve SSOX stejně jako dříve a rozbalte uzel **Tabulky,** abyste viděli, že byly vytvořeny všechny tabulky.</span><span class="sxs-lookup"><span data-stu-id="fe66e-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="fe66e-364">(Pokud máte stále ssox otevřený z předchozího času, klepněte na tlačítko **Aktualizovat.)**</span><span class="sxs-lookup"><span data-stu-id="fe66e-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="fe66e-366">Spusťte aplikaci a aktivujte kód inicializátoru, který zasílá databázi.</span><span class="sxs-lookup"><span data-stu-id="fe66e-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="fe66e-367">Klikněte pravým tlačítkem myši na tabulku **CourseAssignment** a výběrem **možnosti Zobrazit data** ověřte, zda v ní jsou data.</span><span class="sxs-lookup"><span data-stu-id="fe66e-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![CourseAssignment data v SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="fe66e-369">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="fe66e-369">Get the code</span></span>

[<span data-ttu-id="fe66e-370">Stáhněte nebo zobrazte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe66e-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="fe66e-371">Další kroky</span><span class="sxs-lookup"><span data-stu-id="fe66e-371">Next steps</span></span>

<span data-ttu-id="fe66e-372">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="fe66e-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fe66e-373">Přizpůsobené datového modelu</span><span class="sxs-lookup"><span data-stu-id="fe66e-373">Customized the Data model</span></span>
> * <span data-ttu-id="fe66e-374">Provedené změny v entitě Student</span><span class="sxs-lookup"><span data-stu-id="fe66e-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="fe66e-375">Entita Vytvořeného instruktora</span><span class="sxs-lookup"><span data-stu-id="fe66e-375">Created Instructor entity</span></span>
> * <span data-ttu-id="fe66e-376">Entita Vytvořeno OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="fe66e-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="fe66e-377">Modifikovaná entita kurzu</span><span class="sxs-lookup"><span data-stu-id="fe66e-377">Modified Course entity</span></span>
> * <span data-ttu-id="fe66e-378">Entita Vytvořeného oddělení</span><span class="sxs-lookup"><span data-stu-id="fe66e-378">Created Department entity</span></span>
> * <span data-ttu-id="fe66e-379">Entita Upravené registrace</span><span class="sxs-lookup"><span data-stu-id="fe66e-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="fe66e-380">Byl aktualizován kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="fe66e-380">Updated the database context</span></span>
> * <span data-ttu-id="fe66e-381">Nasevovaná databáze s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="fe66e-381">Seeded database with test data</span></span>
> * <span data-ttu-id="fe66e-382">Přidána migrace</span><span class="sxs-lookup"><span data-stu-id="fe66e-382">Added a migration</span></span>
> * <span data-ttu-id="fe66e-383">Změna připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="fe66e-383">Changed the connection string</span></span>
> * <span data-ttu-id="fe66e-384">Byla aktualizována databáze</span><span class="sxs-lookup"><span data-stu-id="fe66e-384">Updated the database</span></span>

<span data-ttu-id="fe66e-385">Přejdete k dalšímu kurzu, kde se dozvíte více o tom, jak získat přístup k souvisejícím datům.</span><span class="sxs-lookup"><span data-stu-id="fe66e-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="fe66e-386">Další: Přístup k souvisejícím datům</span><span class="sxs-lookup"><span data-stu-id="fe66e-386">Next: Access related data</span></span>](read-related-data.md)
