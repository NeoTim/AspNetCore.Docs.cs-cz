---
title: 'Kurz: Vytvoření složitého datového modelu – ASP.NET MVC s EF Core'
description: V tomto kurzu přidat další entity a relace a přizpůsobte si datový model zadáním formátování, ověřování a pravidel mapování.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/05/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 5ab893dd77ff2cc9a735702eb3a547ed8bcb2197
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264864"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="1b06b-103">Kurz: Vytvoření složitého datového modelu – ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="1b06b-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="1b06b-104">V předchozích kurzech jste pracovali s jednoduchý datový model, který se skládá z tři entity.</span><span class="sxs-lookup"><span data-stu-id="1b06b-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="1b06b-105">V tomto kurzu přidáte další entity a relace a tak, že zadáte formátování, ověřování a pravidel mapování database budete Přizpůsobte si datový model.</span><span class="sxs-lookup"><span data-stu-id="1b06b-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="1b06b-106">Jakmile budete hotovi, tříd entit budou použity k vytvoření dokončeného datový model, který je znázorněno na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="1b06b-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Entity diagram](complex-data-model/_static/diagram.png)

<span data-ttu-id="1b06b-108">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="1b06b-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1b06b-109">Přizpůsobte si datový model</span><span class="sxs-lookup"><span data-stu-id="1b06b-109">Customize the Data model</span></span>
> * <span data-ttu-id="1b06b-110">Provádět změny entity studenta</span><span class="sxs-lookup"><span data-stu-id="1b06b-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="1b06b-111">Vytvoření entity instruktorem</span><span class="sxs-lookup"><span data-stu-id="1b06b-111">Create Instructor entity</span></span>
> * <span data-ttu-id="1b06b-112">Vytvoření OfficeAssignment entity</span><span class="sxs-lookup"><span data-stu-id="1b06b-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="1b06b-113">Upravit entity kurzu</span><span class="sxs-lookup"><span data-stu-id="1b06b-113">Modify Course entity</span></span>
> * <span data-ttu-id="1b06b-114">Vytvoření entity oddělení</span><span class="sxs-lookup"><span data-stu-id="1b06b-114">Create Department entity</span></span>
> * <span data-ttu-id="1b06b-115">Upravit entity registrace</span><span class="sxs-lookup"><span data-stu-id="1b06b-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="1b06b-116">Aktualizace kontext databáze</span><span class="sxs-lookup"><span data-stu-id="1b06b-116">Update the database context</span></span>
> * <span data-ttu-id="1b06b-117">Počáteční hodnota databáze s testovací data</span><span class="sxs-lookup"><span data-stu-id="1b06b-117">Seed database with test data</span></span>
> * <span data-ttu-id="1b06b-118">Přidejte migraci</span><span class="sxs-lookup"><span data-stu-id="1b06b-118">Add a migration</span></span>
> * <span data-ttu-id="1b06b-119">Změňte připojovací řetězec</span><span class="sxs-lookup"><span data-stu-id="1b06b-119">Change the connection string</span></span>
> * <span data-ttu-id="1b06b-120">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="1b06b-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1b06b-121">Požadavky</span><span class="sxs-lookup"><span data-stu-id="1b06b-121">Prerequisites</span></span>

* [<span data-ttu-id="1b06b-122">Použití funkce migrace EF Core pro ASP.NET Core ve webové aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="1b06b-122">Using the EF Core migrations feature for ASP.NET Core in an MVC web app</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="1b06b-123">Přizpůsobte si datový model</span><span class="sxs-lookup"><span data-stu-id="1b06b-123">Customize the Data model</span></span>

<span data-ttu-id="1b06b-124">V této části uvidíte, jak přizpůsobit datového modelu s použitím atributů, které určují databáze mapování pravidel ověřování a formátování.</span><span class="sxs-lookup"><span data-stu-id="1b06b-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="1b06b-125">Potom v některé z následujících částí, které si vytvoříte kompletní datový model školní přidáním atributů třídy jste již vytvořili a vytvářet nové třídy pro zbývající typy entit v modelu.</span><span class="sxs-lookup"><span data-stu-id="1b06b-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="1b06b-126">Datový typ atributu</span><span class="sxs-lookup"><span data-stu-id="1b06b-126">The DataType attribute</span></span>

<span data-ttu-id="1b06b-127">Student zápisu dat všechny webové stránky aktuálně zobrazit čas spolu s datem, i když je všechno, co vás zajímá pro toto pole datum.</span><span class="sxs-lookup"><span data-stu-id="1b06b-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="1b06b-128">S použitím anotace atributů dat, můžete si ho kódu změnu, která budou v každé zobrazení, která zobrazuje data opravit formát zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1b06b-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="1b06b-129">Chcete-li zobrazit příklad tohoto postupu, že přidáte atribut, který má `EnrollmentDate` vlastnost `Student` třídy.</span><span class="sxs-lookup"><span data-stu-id="1b06b-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="1b06b-130">V *Models/Student.cs*, přidejte `using` příkaz pro `System.ComponentModel.DataAnnotations` obor názvů a přidejte `DataType` a `DisplayFormat` atributů `EnrollmentDate` vlastnost, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1b06b-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="1b06b-131">`DataType` Atribut se používá k určení datový typ, který je specifičtější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="1b06b-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="1b06b-132">V tomto případě chceme jenom udržovat přehled o datum, nejsou data a času.</span><span class="sxs-lookup"><span data-stu-id="1b06b-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="1b06b-133">`DataType` Výčet poskytuje pro mnoho typů dat, jako je například datum, čas, telefonní číslo, Měna, EmailAddress a další.</span><span class="sxs-lookup"><span data-stu-id="1b06b-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="1b06b-134">`DataType` Atribut můžete také povolit aplikace a zajistit tak automaticky specifické pro typ funkce.</span><span class="sxs-lookup"><span data-stu-id="1b06b-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="1b06b-135">Například `mailto:` propojení lze vytvořit pro `DataType.EmailAddress`, a selektor date lze zadat pro `DataType.Date` v prohlížečích podporujících HTML5.</span><span class="sxs-lookup"><span data-stu-id="1b06b-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="1b06b-136">`DataType` Atribut vysílá HTML 5 `data-` (čteno data dash) atributy, které umožní pochopit prohlížeče HTML 5.</span><span class="sxs-lookup"><span data-stu-id="1b06b-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="1b06b-137">`DataType` Atributy neposkytují žádné ověřování.</span><span class="sxs-lookup"><span data-stu-id="1b06b-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="1b06b-138">`DataType.Date` neurčuje formátu, který se zobrazí datum.</span><span class="sxs-lookup"><span data-stu-id="1b06b-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="1b06b-139">Ve výchozím nastavení zobrazí se pole data podle výchozí formát založený na serveru CultureInfo.</span><span class="sxs-lookup"><span data-stu-id="1b06b-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="1b06b-140">`DisplayFormat` Atribut se používá s ohledem na formát data:</span><span class="sxs-lookup"><span data-stu-id="1b06b-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="1b06b-141">`ApplyFormatInEditMode` Nastavení určuje, že formátování také bude použito při hodnota se zobrazí v textovém poli pro úpravu.</span><span class="sxs-lookup"><span data-stu-id="1b06b-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="1b06b-142">(Není vhodné pro některá pole – například pro hodnoty měny, nemusí požadované symbol měny v textovém poli pro úpravu.)</span><span class="sxs-lookup"><span data-stu-id="1b06b-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="1b06b-143">Můžete použít `DisplayFormat` atribut samotný, ale to je obecně vhodné použít `DataType` také atribut.</span><span class="sxs-lookup"><span data-stu-id="1b06b-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="1b06b-144">`DataType` Atribut přenáší sémantiku dat na rozdíl od vykreslování na obrazovce a nabízí následující výhody, které vám s `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="1b06b-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="1b06b-145">HTML5 funkce můžete povolit v prohlížeči (například zobrazení ovládacího prvku kalendář, symbol měny odpovídající národní prostředí, odkazy na e-mailu, některé klientské vstupní ověřování atd.).</span><span class="sxs-lookup"><span data-stu-id="1b06b-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="1b06b-146">Ve výchozím prohlížeči bude vykreslovat data ve správném formátu podle vašeho národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="1b06b-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="1b06b-147">Další informace najdete v tématu [ \<vstupní > dokumentace pomocné rutiny značky](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="1b06b-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="1b06b-148">Spusťte aplikaci, přejděte na stránku Index studenty a Všimněte si, že časy se už nezobrazují pro data registrace.</span><span class="sxs-lookup"><span data-stu-id="1b06b-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="1b06b-149">Stejné bude platit pro všechna zobrazení, která používá model studentů.</span><span class="sxs-lookup"><span data-stu-id="1b06b-149">The same will be true for any view that uses the Student model.</span></span>

![Studenti indexová stránka zobrazující data bez časy](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="1b06b-151">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="1b06b-151">The StringLength attribute</span></span>

<span data-ttu-id="1b06b-152">Můžete také zadat data ověřovací pravidla a chybových zpráv ověření pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="1b06b-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="1b06b-153">`StringLength` Atribut Nastaví maximální počet znaků v databázi a na straně klienta a na straně serveru ověřování pro ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="1b06b-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="1b06b-154">Minimální délka řetězce. můžete také zadat v tomto atributu, ale minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="1b06b-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="1b06b-155">Předpokládejme, že chcete mít jistotu, že uživatelé nezadávejte více než 50 znaků pro název.</span><span class="sxs-lookup"><span data-stu-id="1b06b-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="1b06b-156">Chcete-li přidat toto omezení, přidejte `StringLength` atributů `LastName` a `FirstMidName` vlastnosti, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1b06b-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="1b06b-157">`StringLength` Atribut nebude zabránit uživateli v zadávání prázdné znaky pro název.</span><span class="sxs-lookup"><span data-stu-id="1b06b-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="1b06b-158">Můžete použít `RegularExpression` atributu použít omezení na vstup.</span><span class="sxs-lookup"><span data-stu-id="1b06b-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="1b06b-159">Například následující kód vyžaduje prvního znaku na velká písmena a zbývající znaky, které mají být abecední znak:</span><span class="sxs-lookup"><span data-stu-id="1b06b-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="1b06b-160">`MaxLength` Atribut poskytuje podobné funkce `StringLength` atribut, ale neposkytuje na straně klienta ověření.</span><span class="sxs-lookup"><span data-stu-id="1b06b-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="1b06b-161">Model databáze se změnilo tak, aby vyžaduje změnu schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="1b06b-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="1b06b-162">Migrace budete používat k aktualizaci schématu bez ztráty dat, která jste mohli přidat do databáze s použitím uživatelského rozhraní aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b06b-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="1b06b-163">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="1b06b-163">Save your changes and build the project.</span></span> <span data-ttu-id="1b06b-164">Potom otevřete okno příkazového řádku ve složce projektu a zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="1b06b-164">Then open the command window in the project folder and enter the following commands:</span></span>

```console
dotnet ef migrations add MaxLengthOnNames
```

```console
dotnet ef database update
```

<span data-ttu-id="1b06b-165">`migrations add` Příkaz vás upozorní, že může dojít ke ztrátě dat, protože díky této změně maximální délku kratší pro dva sloupce.</span><span class="sxs-lookup"><span data-stu-id="1b06b-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="1b06b-166">Migrace vytvoří soubor s názvem  *\<časové razítko > _MaxLengthOnNames.cs*.</span><span class="sxs-lookup"><span data-stu-id="1b06b-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="1b06b-167">Tento soubor obsahuje kód v `Up` metodu, která aktualizuje databázi tak, aby odpovídaly aktuálním datovým modelem.</span><span class="sxs-lookup"><span data-stu-id="1b06b-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="1b06b-168">`database update` Tento kód byl spuštěn příkaz.</span><span class="sxs-lookup"><span data-stu-id="1b06b-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="1b06b-169">Časové razítko, před kterou je připojený k názvu souboru migrace používá Entity Framework pro řazení migrace.</span><span class="sxs-lookup"><span data-stu-id="1b06b-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="1b06b-170">Můžete vytvořit více migrace před spuštěním příkazu update databáze a pak všechny migrace se použijí v pořadí, ve kterém byly vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="1b06b-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="1b06b-171">Spusťte aplikaci, vyberte **studenty** klikněte na tlačítko **vytvořit nový**a pokuste se zadat buď název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="1b06b-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="1b06b-172">Aplikace by měla by vám bránily v to.</span><span class="sxs-lookup"><span data-stu-id="1b06b-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="1b06b-173">Atribut sloupce</span><span class="sxs-lookup"><span data-stu-id="1b06b-173">The Column attribute</span></span>

<span data-ttu-id="1b06b-174">Atributy můžete také řídit, jak třídy a vlastnosti jsou namapovány na databázi.</span><span class="sxs-lookup"><span data-stu-id="1b06b-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="1b06b-175">Předpokládejme, že byste použili název `FirstMidName` pro název prvního pole, protože pole může obsahovat také křestní jméno.</span><span class="sxs-lookup"><span data-stu-id="1b06b-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="1b06b-176">Chcete sloupec databáze s názvem, ale `FirstName`, protože uživatelé budou psát ad-hoc dotazy na databázi jsou zvyklí na tento název.</span><span class="sxs-lookup"><span data-stu-id="1b06b-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="1b06b-177">Chcete-li toto mapování, můžete použít `Column` atribut.</span><span class="sxs-lookup"><span data-stu-id="1b06b-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="1b06b-178">`Column` Atribut určuje, že když se vytvoří databáze, sloupci `Student` tabulku, která se mapuje `FirstMidName` bude mít název vlastnosti `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="1b06b-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="1b06b-179">Jinými slovy, pokud váš kód odkazuje na `Student.FirstMidName`, data budou přicházet z nebo v aktualizovat `FirstName` sloupec `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="1b06b-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="1b06b-180">Pokud nechcete zadat názvy sloupců, že mu udělená stejný název jako název vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="1b06b-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="1b06b-181">V *Student.cs* přidejte `using` příkaz pro `System.ComponentModel.DataAnnotations.Schema` a přidat sloupec název atributu `FirstMidName` vlastnost, jak je znázorněno v následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="1b06b-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="1b06b-182">Přidání `Column` atribut změní základní model `SchoolContext`, takže nebude odpovídat databázi.</span><span class="sxs-lookup"><span data-stu-id="1b06b-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="1b06b-183">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="1b06b-183">Save your changes and build the project.</span></span> <span data-ttu-id="1b06b-184">Potom otevřete okno příkazového řádku ve složce projektu a zadejte následující příkazy vytvoří další migraci:</span><span class="sxs-lookup"><span data-stu-id="1b06b-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```console
dotnet ef migrations add ColumnFirstName
```

```console
dotnet ef database update
```

<span data-ttu-id="1b06b-185">V **Průzkumník objektů systému SQL Server**, otevřete Návrhář tabulky Student dvojitým kliknutím **Student** tabulky.</span><span class="sxs-lookup"><span data-stu-id="1b06b-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulky Studenti v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="1b06b-187">Před použitím první dvě migrace název sloupce se mají z typu nvarchar(MAX).</span><span class="sxs-lookup"><span data-stu-id="1b06b-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="1b06b-188">Jsou to teď nvarchar(50) a název sloupce byl změněn z FirstMidName na jméno.</span><span class="sxs-lookup"><span data-stu-id="1b06b-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="1b06b-189">Pokud se pokusíte zkompilovat před dokončení vytváření všech tříd entit v následujících částech, může docházet k chybám kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="1b06b-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="1b06b-190">Změny entity studenta</span><span class="sxs-lookup"><span data-stu-id="1b06b-190">Changes to Student entity</span></span>

![Student entity](complex-data-model/_static/student-entity.png)

<span data-ttu-id="1b06b-192">V *Models/Student.cs*, nahraďte kód, který jste přidali dříve následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="1b06b-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="1b06b-193">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="1b06b-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="1b06b-194">Požadovaný atribut</span><span class="sxs-lookup"><span data-stu-id="1b06b-194">The Required attribute</span></span>

<span data-ttu-id="1b06b-195">`Required` Atribut je povinná pole název vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="1b06b-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="1b06b-196">`Required` Atribut není potřeba pro typy neumožňující hodnotu, jako jsou typy hodnot (datum a čas, int, dvakrát, float, atd.).</span><span class="sxs-lookup"><span data-stu-id="1b06b-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="1b06b-197">Typy, které nemůže mít hodnotu null se automaticky považují za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="1b06b-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="1b06b-198">Můžete odebrat `Required` atribut a nahraďte ji metodou minimální délku parametru `StringLength` atribut:</span><span class="sxs-lookup"><span data-stu-id="1b06b-198">You could remove the `Required` attribute and replace it with a minimum length parameter for the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="1b06b-199">Atribut zobrazení</span><span class="sxs-lookup"><span data-stu-id="1b06b-199">The Display attribute</span></span>

<span data-ttu-id="1b06b-200">`Display` Atribut určuje, že titulek pro textová pole by měl být "Jméno", "Last Name", "Jméno" a "Datum registrace" namísto názvu vlastnosti v každé instanci (která nemá místo dělení slov).</span><span class="sxs-lookup"><span data-stu-id="1b06b-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="1b06b-201">Celý název počítané vlastnosti</span><span class="sxs-lookup"><span data-stu-id="1b06b-201">The FullName calculated property</span></span>

<span data-ttu-id="1b06b-202">`FullName` je počítaná vlastnost, která vrací hodnotu, která se vytváří zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="1b06b-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="1b06b-203">Proto má pouze přístupový objekt get a ne `FullName` vygeneruje sloupec v databázi.</span><span class="sxs-lookup"><span data-stu-id="1b06b-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="1b06b-204">Vytvoření entity instruktorem</span><span class="sxs-lookup"><span data-stu-id="1b06b-204">Create Instructor entity</span></span>

![Entita instruktorem](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="1b06b-206">Vytvoření *Models/Instructor.cs*, nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1b06b-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="1b06b-207">Všimněte si, že několik vlastnosti jsou stejné v entitách studenty a instruktorem.</span><span class="sxs-lookup"><span data-stu-id="1b06b-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="1b06b-208">V [implementace dědičnosti](inheritance.md) kurz později v této sérii, budete Refaktorovat tento kód eliminovat redundance.</span><span class="sxs-lookup"><span data-stu-id="1b06b-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="1b06b-209">Na jednom řádku, můžete umístit více atributů, tak by mohl taky zapisovat `HireDate` atributy následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="1b06b-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="1b06b-210">Navigační vlastnosti CourseAssignments a OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="1b06b-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="1b06b-211">`CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="1b06b-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="1b06b-212">Instruktorem můžete naučit libovolný počet kurzů, takže `CourseAssignments` je definovaná jako kolekce.</span><span class="sxs-lookup"><span data-stu-id="1b06b-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="1b06b-213">Pokud vlastnost navigace může obsahovat více entit, jeho typ musí být seznam, ve kterém položky lze přidávat, odstranit a aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="1b06b-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="1b06b-214">Můžete zadat `ICollection<T>` nebo typu jako `List<T>` nebo `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="1b06b-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="1b06b-215">Pokud zadáte `ICollection<T>`, vytvoří EF `HashSet<T>` kolekcí ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="1b06b-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="1b06b-216">Důvod, proč jsou `CourseAssignment` entity je popsán níže v části o relacích many-to-many.</span><span class="sxs-lookup"><span data-stu-id="1b06b-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="1b06b-217">Obchodní pravidla contoso University stavu instruktorem může mít pouze nejvýše jeden office, takže `OfficeAssignment` vlastnost obsahuje jednu entitu OfficeAssignment (který může mít hodnotu null, pokud není přiřazena žádná office).</span><span class="sxs-lookup"><span data-stu-id="1b06b-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="1b06b-218">Vytvoření OfficeAssignment entity</span><span class="sxs-lookup"><span data-stu-id="1b06b-218">Create OfficeAssignment entity</span></span>

![OfficeAssignment entity](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="1b06b-220">Vytvoření *Models/OfficeAssignment.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1b06b-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="1b06b-221">Atribut Key</span><span class="sxs-lookup"><span data-stu-id="1b06b-221">The Key attribute</span></span>

<span data-ttu-id="1b06b-222">Existuje vztah jeden: nula nebo 1 až kurzů vedených OfficeAssignment entity.</span><span class="sxs-lookup"><span data-stu-id="1b06b-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="1b06b-223">Přiřazení office existuje pouze ve vztahu k instruktorem, který je přiřazen k a proto jeho primární klíč se také jeho cizí klíč do kurzů vedených entity.</span><span class="sxs-lookup"><span data-stu-id="1b06b-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="1b06b-224">Ale Entity Framework nemůže rozpoznat automaticky InstructorID jako primární klíč tuto entitu protože jeho název není podle ID nebo classnameID konvence.</span><span class="sxs-lookup"><span data-stu-id="1b06b-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="1b06b-225">Proto `Key` atribut se používá k jeho identifikaci jako klíč:</span><span class="sxs-lookup"><span data-stu-id="1b06b-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="1b06b-226">Můžete také použít `Key` atribut Pokud entita nemá primární klíč, ale budete chtít název vlastnosti něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="1b06b-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="1b06b-227">Ve výchozím nastavení EF považuje za klíč bez databáze vygenerovala protože sloupec je pro identifikující relaci.</span><span class="sxs-lookup"><span data-stu-id="1b06b-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="1b06b-228">Navigační vlastnost instruktorem</span><span class="sxs-lookup"><span data-stu-id="1b06b-228">The Instructor navigation property</span></span>

<span data-ttu-id="1b06b-229">Kurzů vedených entita má s povolenou hodnotou Null `OfficeAssignment` navigační vlastnosti (protože instruktorem nemusí mít přiřazení office), a OfficeAssignment entita má zakázanou `Instructor` navigační vlastnosti (protože nelze přiřazení office existovat bez instruktorem – `InstructorID` je null).</span><span class="sxs-lookup"><span data-stu-id="1b06b-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="1b06b-230">Pokud má entita kurzů vedených související entita OfficeAssignment, každá entita bude mít odkaz na druhou v jeho navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="1b06b-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="1b06b-231">Můžete umístit `[Required]` atribut u vlastnosti navigace kurzů vedených a určit tak, že musí být související instruktorem, ale nemáte to provést, protože `InstructorID` cizí klíč (což je také klíč do této tabulky) je null.</span><span class="sxs-lookup"><span data-stu-id="1b06b-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="1b06b-232">Upravit entity kurzu</span><span class="sxs-lookup"><span data-stu-id="1b06b-232">Modify Course entity</span></span>

![Kurz entity](complex-data-model/_static/course-entity.png)

<span data-ttu-id="1b06b-234">V *Models/Course.cs*, nahraďte kód, který jste přidali dříve následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="1b06b-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="1b06b-235">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="1b06b-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="1b06b-236">Kurz entita má vlastnost cizího klíče `DepartmentID` který odkazuje na související entity oddělení a má `Department` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="1b06b-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="1b06b-237">Entity Framework nevyžaduje, můžete přidat vlastnost cizího klíče do datového modelu, když máte navigační vlastnost pro související entity.</span><span class="sxs-lookup"><span data-stu-id="1b06b-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="1b06b-238">EF automaticky vytvoří cizí klíče v databázi bez ohledu na to budete potřebovat a vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro ně.</span><span class="sxs-lookup"><span data-stu-id="1b06b-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="1b06b-239">Ale s cizího klíče v datovém modelu může být aktualizace jednodušší a efektivnější.</span><span class="sxs-lookup"><span data-stu-id="1b06b-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="1b06b-240">Například při načtení entity kurzu upravit entity oddělení má hodnotu null Pokud načtete nemusíte ho, proto při aktualizaci entity kurzu budete mít se nejdřív načíst entity oddělení.</span><span class="sxs-lookup"><span data-stu-id="1b06b-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="1b06b-241">Pokud vlastnost cizího klíče `DepartmentID` je zahrnuta v datovém modelu, není nutné k načtení entity oddělení před aktualizací.</span><span class="sxs-lookup"><span data-stu-id="1b06b-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="1b06b-242">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="1b06b-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="1b06b-243">`DatabaseGenerated` Atributem `None` parametru u `CourseID` vlastnost určuje, že hodnoty primárního klíče jsou uživatelem zadané místo generován databází.</span><span class="sxs-lookup"><span data-stu-id="1b06b-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="1b06b-244">Ve výchozím nastavení Entity Framework předpokládá, že hodnoty primárního klíče je generován databází.</span><span class="sxs-lookup"><span data-stu-id="1b06b-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="1b06b-245">Který se má ve většině scénářů.</span><span class="sxs-lookup"><span data-stu-id="1b06b-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="1b06b-246">Pro entity kurzu, budete však použít číslo uživatel zadal kurzu například řadu 1000 pro jedno oddělení, řadu 2000 pro jiného oddělení a tak dále.</span><span class="sxs-lookup"><span data-stu-id="1b06b-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="1b06b-247">`DatabaseGenerated` Atribut lze také generovat výchozí hodnoty, jako v případě sloupců databáze slouží k záznamu datum řádek byl vytvořen nebo aktualizován.</span><span class="sxs-lookup"><span data-stu-id="1b06b-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="1b06b-248">Další informace najdete v tématu [vygenerovaným vlastnostem](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="1b06b-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="1b06b-249">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="1b06b-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="1b06b-250">Vlastnosti cizího klíče a navigačních vlastností v entitě kurzu zahrnují následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="1b06b-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="1b06b-251">Kurz je přiřazena jednoho oddělení, takže `DepartmentID` cizího klíče a `Department` navigační vlastnost z důvodů uvedených výše.</span><span class="sxs-lookup"><span data-stu-id="1b06b-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="1b06b-252">Kurz můžete mít libovolný počet studentů zaregistrované, takže `Enrollments` navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="1b06b-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="1b06b-253">Kurz může být vedená instruktorů více, proto `CourseAssignments` navigační vlastnost je kolekce (typ `CourseAssignment` je vysvětleno [později](#many-to-many-relationships)):</span><span class="sxs-lookup"><span data-stu-id="1b06b-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="1b06b-254">Vytvoření entity oddělení</span><span class="sxs-lookup"><span data-stu-id="1b06b-254">Create Department entity</span></span>

![Oddělení entity](complex-data-model/_static/department-entity.png)

<span data-ttu-id="1b06b-256">Vytvoření *Models/Department.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1b06b-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="1b06b-257">Atribut sloupce</span><span class="sxs-lookup"><span data-stu-id="1b06b-257">The Column attribute</span></span>

<span data-ttu-id="1b06b-258">Dříve jste použili `Column` atribut, chcete-li změnit název mapování sloupců.</span><span class="sxs-lookup"><span data-stu-id="1b06b-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="1b06b-259">V kódu pro entitu oddělení `Column` atribut se používá, chcete-li změnit SQL mapování datového typu tak, aby sloupec bude definován pomocí typ money systému SQL Server v databázi:</span><span class="sxs-lookup"><span data-stu-id="1b06b-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="1b06b-260">Mapování sloupců se obecně nevyžaduje, protože rozhraní Entity Framework vybere odpovídající datový typ SQL serveru na základě typu CLR, které definujete pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="1b06b-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="1b06b-261">Modul CLR `decimal` zadejte mapuje se na serveru SQL Server `decimal` typu.</span><span class="sxs-lookup"><span data-stu-id="1b06b-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="1b06b-262">Ale v tomto případě víte, že sloupec se drží peněžních hodnot, a je vhodnější pro tento datový typ money.</span><span class="sxs-lookup"><span data-stu-id="1b06b-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="1b06b-263">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="1b06b-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="1b06b-264">Vlastnosti cizího klíče a navigace zahrnují následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="1b06b-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="1b06b-265">Oddělení může nebo nemusí mít správce a správce je vždy instruktorem.</span><span class="sxs-lookup"><span data-stu-id="1b06b-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="1b06b-266">Proto `InstructorID` vlastnost je součástí jako cizí klíč k entitě instruktorem a přidá se otazník za `int` označení označit vlastnosti jako datový typ s možnou hodnotou Null typu.</span><span class="sxs-lookup"><span data-stu-id="1b06b-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="1b06b-267">Navigační vlastnost jmenuje `Administrator` ale obsahuje entitu kurzů vedených:</span><span class="sxs-lookup"><span data-stu-id="1b06b-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="1b06b-268">Oddělení může mít mnoho kurzů, tedy navigační vlastnost kurzy:</span><span class="sxs-lookup"><span data-stu-id="1b06b-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="1b06b-269">Podle konvence rozhraní Entity Framework umožňuje kaskádové odstranění pro Null cizí klíče a vztahy many-to-many.</span><span class="sxs-lookup"><span data-stu-id="1b06b-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="1b06b-270">To může způsobit Cyklické kaskádové odstranění pravidla, která způsobí výjimku při pokusu o přidání migrace.</span><span class="sxs-lookup"><span data-stu-id="1b06b-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="1b06b-271">Například pokud definujete neměli vlastnost Department.InstructorID jako s možnou hodnotou Null, EF by nakonfigurovat pravidlo cascade delete můžete odstranit instruktorem, když odstraníte oddělení, který není co byste chtěli se stane.</span><span class="sxs-lookup"><span data-stu-id="1b06b-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the instructor when you delete the department, which isn't what you want to have happen.</span></span> <span data-ttu-id="1b06b-272">V případě potřeby obchodních pravidel `InstructorID` vlastnosti být null, je třeba použít následující příkaz rozhraní API fluent zakázat kaskádové odstranění v relaci:</span><span class="sxs-lookup"><span data-stu-id="1b06b-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="1b06b-273">Upravit entity registrace</span><span class="sxs-lookup"><span data-stu-id="1b06b-273">Modify Enrollment entity</span></span>

![Registrace entity](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="1b06b-275">V *Models/Enrollment.cs*, nahraďte kód, který jste přidali dříve následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1b06b-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="1b06b-276">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="1b06b-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="1b06b-277">Vlastnosti cizího klíče a navigačních vlastností zahrnují následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="1b06b-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="1b06b-278">Záznam registrace je jeden kurzům, tedy `CourseID` vlastnost cizího klíče a `Course` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="1b06b-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="1b06b-279">Záznam registrace je pro jeden student, tedy `StudentID` vlastnost cizího klíče a `Student` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="1b06b-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="1b06b-280">Relace m: N</span><span class="sxs-lookup"><span data-stu-id="1b06b-280">Many-to-Many relationships</span></span>

<span data-ttu-id="1b06b-281">Existuje many-to-many vztah mezi entitami studenty a kurzu a entitou registrace funguje jako tabulka many-to-many spojení *s datovou částí* v databázi.</span><span class="sxs-lookup"><span data-stu-id="1b06b-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="1b06b-282">"S datovou částí" znamená, že registrace tabulka obsahuje další údaje kromě cizí klíče pro spojené tabulky (v tomto případě primární klíč a vlastnost na podnikové úrovni).</span><span class="sxs-lookup"><span data-stu-id="1b06b-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="1b06b-283">Následující obrázek znázorňuje, jak tyto vztahy vypadat v diagramu entity.</span><span class="sxs-lookup"><span data-stu-id="1b06b-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="1b06b-284">(Tento diagram se vygeneroval pomocí Entity Framework Power Tools pro EF 6.x; vytvoření diagramu, které nejsou součástí tohoto kurzu, je právě používán jako ilustraci zde.)</span><span class="sxs-lookup"><span data-stu-id="1b06b-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Kurz student mnoho na mnoho vztah](complex-data-model/_static/student-course.png)

<span data-ttu-id="1b06b-286">Každý řádek vztah má 1 na jednom konci a hvězdičku (\*) na druhém, určující vztah jeden mnoho.</span><span class="sxs-lookup"><span data-stu-id="1b06b-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="1b06b-287">Pokud v tabulce registrace nezahrnuli informace na podnikové úrovni, bude pouze nutné tak, aby obsahovala dva cizího klíče CourseID a StudentID.</span><span class="sxs-lookup"><span data-stu-id="1b06b-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="1b06b-288">V takovém případě by bylo tabulku spojení many-to-many bez datové části (nebo čistě spojení tabulku) v databázi.</span><span class="sxs-lookup"><span data-stu-id="1b06b-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="1b06b-289">Entity instruktorem a kurzu mají tento druh vztahu many-to-many a dalším krokem je vytvoření třídu entity jako tabulku spojení bez datové části.</span><span class="sxs-lookup"><span data-stu-id="1b06b-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="1b06b-290">(EF 6.x podporuje implicitní spojení tabulek pro vztahy many-to-many, ale EF Core nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="1b06b-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="1b06b-291">Další informace najdete v tématu [diskuze v úložišti EF Core GitHub](https://github.com/aspnet/EntityFramework/issues/1368).)</span><span class="sxs-lookup"><span data-stu-id="1b06b-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="1b06b-292">CourseAssignment entity</span><span class="sxs-lookup"><span data-stu-id="1b06b-292">The CourseAssignment entity</span></span>

![CourseAssignment entity](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="1b06b-294">Vytvoření *Models/CourseAssignment.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1b06b-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="1b06b-295">Připojte se k názvy entit</span><span class="sxs-lookup"><span data-stu-id="1b06b-295">Join entity names</span></span>

<span data-ttu-id="1b06b-296">V databázi pro vztah many-to-many kurzů vedených kurzy se vyžaduje tabulku spojení a musí být reprezentována sadu entit.</span><span class="sxs-lookup"><span data-stu-id="1b06b-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="1b06b-297">Je běžné název entity spojení `EntityName1EntityName2`, který v tomto případě bude `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="1b06b-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="1b06b-298">Doporučujeme však, že vyberete název, který popisuje relace.</span><span class="sxs-lookup"><span data-stu-id="1b06b-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="1b06b-299">Datové modely začíná jednoduchou i při pozdějším růstu s LINQ pomocí spojení ne datové části často získání datových částí později.</span><span class="sxs-lookup"><span data-stu-id="1b06b-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="1b06b-300">Pokud byste začali s entity popisný název, nebudete muset později změnit název.</span><span class="sxs-lookup"><span data-stu-id="1b06b-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="1b06b-301">Spojení entit v ideálním případě by mít svůj vlastní přirozené název (může být jediné slovo) v obchodní domény.</span><span class="sxs-lookup"><span data-stu-id="1b06b-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="1b06b-302">Například může knihy a zákazníci spojený prostřednictvím hodnocení.</span><span class="sxs-lookup"><span data-stu-id="1b06b-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="1b06b-303">Pro tento vztah `CourseAssignment` je vhodnější než `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="1b06b-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="1b06b-304">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="1b06b-304">Composite key</span></span>

<span data-ttu-id="1b06b-305">Protože cizí klíče nejsou s možnou hodnotou Null a dohromady jedinečně identifikují každý řádek v tabulce, není nutné pro samostatný primární klíč.</span><span class="sxs-lookup"><span data-stu-id="1b06b-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="1b06b-306">*InstructorID* a *CourseID* vlastnosti by měla fungovat jako složený primární klíč.</span><span class="sxs-lookup"><span data-stu-id="1b06b-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="1b06b-307">Jediný způsob, jak identifikovat složené primárního klíče na EF je použít *rozhraní fluent API* (ho nelze provést s použitím atributů).</span><span class="sxs-lookup"><span data-stu-id="1b06b-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="1b06b-308">Uvidíte jak nakonfigurovat složený primární klíč v další části.</span><span class="sxs-lookup"><span data-stu-id="1b06b-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="1b06b-309">Složený klíč zajistí, že i když můžete mít více řádků pro jeden kurz a více řádků pro jeden instruktorem, nemůže mít více řádků pro stejnou instruktorem a kurzu.</span><span class="sxs-lookup"><span data-stu-id="1b06b-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="1b06b-310">`Enrollment` Spojení entita definuje vlastní primární klíč tak, aby byly možné duplicity toto řazení.</span><span class="sxs-lookup"><span data-stu-id="1b06b-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="1b06b-311">Aby se tyto duplikáty, mohou přidat jedinečný index na pole cizích klíčů nebo nakonfigurovat `Enrollment` s primární složený klíč podobný `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="1b06b-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="1b06b-312">Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="1b06b-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="1b06b-313">Aktualizace kontext databáze</span><span class="sxs-lookup"><span data-stu-id="1b06b-313">Update the database context</span></span>

<span data-ttu-id="1b06b-314">Přidejte následující zvýrazněný kód do *Data/SchoolContext.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="1b06b-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="1b06b-315">Tento kód přidá nové entity a nakonfiguruje CourseAssignment entita složený primární klíč.</span><span class="sxs-lookup"><span data-stu-id="1b06b-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="1b06b-316">O fluent alternativu rozhraní API</span><span class="sxs-lookup"><span data-stu-id="1b06b-316">About a fluent API alternative</span></span>

<span data-ttu-id="1b06b-317">Kód v `OnModelCreating` metodu `DbContext` třídy používá *rozhraní fluent API* konfigurace EF chování.</span><span class="sxs-lookup"><span data-stu-id="1b06b-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="1b06b-318">Rozhraní API se nazývá "fluent", protože je často používána zavěšování řadu volání metody společně na jediném příkazu, jako v následujícím příkladu od [EF Core dokumentaci](/ef/core/modeling/#methods-of-configuration):</span><span class="sxs-lookup"><span data-stu-id="1b06b-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#methods-of-configuration):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="1b06b-319">V tomto kurzu se při použití rozhraní fluent API pouze pro mapování databáze, které nelze použít s atributy.</span><span class="sxs-lookup"><span data-stu-id="1b06b-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="1b06b-320">Rozhraní fluent API však můžete také použít k určení většinu formátování, ověřování a pravidla mapování, která vám pomůžou s použitím atributů.</span><span class="sxs-lookup"><span data-stu-id="1b06b-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="1b06b-321">Některé atributy, jako `MinimumLength` nelze použít s rozhraním API fluent.</span><span class="sxs-lookup"><span data-stu-id="1b06b-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="1b06b-322">Jak už bylo zmíněno dříve, `MinimumLength` nedojde ke změně schématu, vztahuje se pouze ověřovacího pravidla na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="1b06b-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="1b06b-323">Někteří vývojáři dávají přednost používání rozhraní fluent API výhradně tak, aby se zachovat jejich tříd entit "vyčištění."</span><span class="sxs-lookup"><span data-stu-id="1b06b-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="1b06b-324">Pokud chcete, a existuje několik přizpůsobení, které lze provést pouze s použitím rozhraní fluent API je možné kombinovat atributy a dynamického rozhraní API, ale obecně doporučeným postupem je zvolte jednu z těchto dvou přístupů a použití, který konzistentně dosahovat.</span><span class="sxs-lookup"><span data-stu-id="1b06b-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="1b06b-325">Pokud používáte obě, mějte na paměti, že bez ohledu na to dojde ke konfliktu, rozhraní Fluent API přepisuje atributy.</span><span class="sxs-lookup"><span data-stu-id="1b06b-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="1b06b-326">Další informace o atributech vs. rozhraní fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/#methods-of-configuration).</span><span class="sxs-lookup"><span data-stu-id="1b06b-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/#methods-of-configuration).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="1b06b-327">Diagram znázorňující entitami</span><span class="sxs-lookup"><span data-stu-id="1b06b-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="1b06b-328">Následující obrázek znázorňuje diagram, který Entity Framework Power Tools vytvořit pro dokončené model školy.</span><span class="sxs-lookup"><span data-stu-id="1b06b-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Entity diagram](complex-data-model/_static/diagram.png)

<span data-ttu-id="1b06b-330">Kromě vztah jednoho k několika řádky (1 k \*), kterou tady vidíte řádek jedna nula nebo 1 v relaci m (1-0..1) mezi instruktorem a OfficeAssignment entity a relace nula nebo 1 n řádek (0.. 1 na \*) mezi Entity instruktorem a oddělení.</span><span class="sxs-lookup"><span data-stu-id="1b06b-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="1b06b-331">Počáteční hodnota databáze s testovací data</span><span class="sxs-lookup"><span data-stu-id="1b06b-331">Seed database with test data</span></span>

<span data-ttu-id="1b06b-332">Nahraďte kód v *Data/DbInitializer.cs* souboru následujícím kódem, aby bylo možné poskytnout data počáteční hodnotu pro nové entity, které jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="1b06b-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="1b06b-333">Jak už jste viděli v první kurz, většina tento kód jednoduše vytvoří nové objekty entity a načte ukázková data do vlastnosti podle potřeby pro testování.</span><span class="sxs-lookup"><span data-stu-id="1b06b-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="1b06b-334">Všimněte si, jak se zpracovává relace many-to-many: kód vytvoří relace tak, že vytvoříte entity v `Enrollments` a `CourseAssignment` připojení sady entit.</span><span class="sxs-lookup"><span data-stu-id="1b06b-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="1b06b-335">Přidejte migraci</span><span class="sxs-lookup"><span data-stu-id="1b06b-335">Add a migration</span></span>

<span data-ttu-id="1b06b-336">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="1b06b-336">Save your changes and build the project.</span></span> <span data-ttu-id="1b06b-337">Pak otevřete okno příkazového řádku ve složce projektu a zadejte `migrations add` příkazu (neprovádějte příkaz aktualizace databáze ještě):</span><span class="sxs-lookup"><span data-stu-id="1b06b-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```console
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="1b06b-338">Zobrazí se upozornění možnou ztrátu.</span><span class="sxs-lookup"><span data-stu-id="1b06b-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="1b06b-339">Pokud jste se pokusili spustit `database update` příkaz v tomto okamžiku (neprovádějte to zatím), by se zobrazí následující chyba:</span><span class="sxs-lookup"><span data-stu-id="1b06b-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="1b06b-340">Příkaz ALTER TABLE způsobil konflikt s omezení FOREIGN KEY "FK_dbo. Course_dbo. Department_DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="1b06b-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="1b06b-341">Ke konfliktu došlo v databázi "ContosoUniversity" table "dbo. Oddělení", sloupec"DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="1b06b-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="1b06b-342">Někdy při spuštění migrace s existujícími daty, je třeba vložit data zástupných procedur do databáze splňovat omezení cizího klíče.</span><span class="sxs-lookup"><span data-stu-id="1b06b-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="1b06b-343">Generovaný kód `Up` metoda přidá do tabulky kurzu cizího klíče nepřipouštějící DepartmentID.</span><span class="sxs-lookup"><span data-stu-id="1b06b-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="1b06b-344">Pokud už existují řádky v tabulce kurzu při spuštění kódu `AddColumn` operace selhala, protože systém SQL Server nebude vědět, jakou hodnotu put ve sloupci, který nemůže mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="1b06b-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="1b06b-345">Pro účely tohoto kurzu budete spouštět na migraci na novou databázi, ale v produkční aplikace bude muset migraci existujících dat, zpracování tak následující pokyny slouží jako ukázka toho, jak to udělat.</span><span class="sxs-lookup"><span data-stu-id="1b06b-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="1b06b-346">Práce s existujícími daty, budete muset změnit kód poskytnout výchozí hodnoty nového sloupce a vytvořit se zakázaným inzerováním oddělení tuto migraci s názvem "Temp" tak, aby fungoval jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="1b06b-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="1b06b-347">V důsledku toho existující řádky kurzu budou všechny souviset s oddělení "Temp" po `Up` metoda spuštění.</span><span class="sxs-lookup"><span data-stu-id="1b06b-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="1b06b-348">Otevřít *{timestamp}_ComplexDataModel.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="1b06b-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="1b06b-349">Zakomentovat řádek kódu, který přidá DepartmentID sloupec v tabulce kurzu.</span><span class="sxs-lookup"><span data-stu-id="1b06b-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="1b06b-350">Přidejte následující zvýrazněný kód za kód, který vytvoří tabulku oddělení:</span><span class="sxs-lookup"><span data-stu-id="1b06b-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="1b06b-351">V produkční aplikace měli byste napsat kód nebo skripty pro přidání řádků oddělení a jejich souvislostí kurzu řádků pro nové řádky oddělení.</span><span class="sxs-lookup"><span data-stu-id="1b06b-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="1b06b-352">By pak už nepotřebujete oddělení "Temp" nebo výchozí hodnotu pro sloupec Course.DepartmentID.</span><span class="sxs-lookup"><span data-stu-id="1b06b-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="1b06b-353">Uložte změny a sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="1b06b-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="1b06b-354">Změňte připojovací řetězec</span><span class="sxs-lookup"><span data-stu-id="1b06b-354">Change the connection string</span></span>

<span data-ttu-id="1b06b-355">Teď máte nový kód `DbInitializer` třídu, která přidá data počáteční hodnotu pro nové entity pro prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="1b06b-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="1b06b-356">Chcete-li vytvořit nové prázdné databáze EF, změňte název databáze v připojovacím řetězci v *appsettings.json* ContosoUniversity3 nebo jiný název, který jste ještě nepoužívali v počítači, který používáte.</span><span class="sxs-lookup"><span data-stu-id="1b06b-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="1b06b-357">Uložit změny do *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1b06b-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="1b06b-358">Jako alternativu k změně názvu databáze je odstranit databázi.</span><span class="sxs-lookup"><span data-stu-id="1b06b-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="1b06b-359">Použití **Průzkumník objektů systému SQL Server** (SSOX) nebo `database drop` příkazu rozhraní příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="1b06b-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```console
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="1b06b-360">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="1b06b-360">Update the database</span></span>

<span data-ttu-id="1b06b-361">Poté, co jste změnili název databáze nebo odstraní databáze, spusťte `database update` příkazu v příkazovém okně k provedení migrace.</span><span class="sxs-lookup"><span data-stu-id="1b06b-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="1b06b-362">Spuštění aplikace způsobí `DbInitializer.Initialize` metoda spuštění a naplňte jimi novou databázi.</span><span class="sxs-lookup"><span data-stu-id="1b06b-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="1b06b-363">Otevřete databázi v SSOX, jako jste to udělali dříve a rozbalte **tabulky** uzel zobrazíte, že všechny tabulky byly vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="1b06b-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="1b06b-364">(Pokud stále máte SSOX otevřít z dřívější čas, klikněte na tlačítko **aktualizovat** tlačítko.)</span><span class="sxs-lookup"><span data-stu-id="1b06b-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="1b06b-366">Spusťte aplikaci aktivovat inicializační kód, který nasazení nasazuje do databáze.</span><span class="sxs-lookup"><span data-stu-id="1b06b-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="1b06b-367">Klikněte pravým tlačítkem myši **CourseAssignment** tabulce a vybrat **Data zobrazení** ověřte, že má data v něm.</span><span class="sxs-lookup"><span data-stu-id="1b06b-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="1b06b-369">Získat kód</span><span class="sxs-lookup"><span data-stu-id="1b06b-369">Get the code</span></span>

[<span data-ttu-id="1b06b-370">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b06b-370">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="1b06b-371">Další kroky</span><span class="sxs-lookup"><span data-stu-id="1b06b-371">Next steps</span></span>

<span data-ttu-id="1b06b-372">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="1b06b-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1b06b-373">Vlastní datový model</span><span class="sxs-lookup"><span data-stu-id="1b06b-373">Customized the Data model</span></span>
> * <span data-ttu-id="1b06b-374">Provedl změny entity studenta</span><span class="sxs-lookup"><span data-stu-id="1b06b-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="1b06b-375">Vytvořené entity instruktorem</span><span class="sxs-lookup"><span data-stu-id="1b06b-375">Created Instructor entity</span></span>
> * <span data-ttu-id="1b06b-376">Vytvořené entity OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="1b06b-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="1b06b-377">Upravená entita kurzu</span><span class="sxs-lookup"><span data-stu-id="1b06b-377">Modified Course entity</span></span>
> * <span data-ttu-id="1b06b-378">Vytvořené entity oddělení</span><span class="sxs-lookup"><span data-stu-id="1b06b-378">Created Department entity</span></span>
> * <span data-ttu-id="1b06b-379">Upravená entita registrace</span><span class="sxs-lookup"><span data-stu-id="1b06b-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="1b06b-380">Aktualizovat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="1b06b-380">Updated the database context</span></span>
> * <span data-ttu-id="1b06b-381">Dosazené databáze s testovací data</span><span class="sxs-lookup"><span data-stu-id="1b06b-381">Seeded database with test data</span></span>
> * <span data-ttu-id="1b06b-382">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="1b06b-382">Added a migration</span></span>
> * <span data-ttu-id="1b06b-383">Změnit připojovací řetězec</span><span class="sxs-lookup"><span data-stu-id="1b06b-383">Changed the connection string</span></span>
> * <span data-ttu-id="1b06b-384">Aktualizovat databázi</span><span class="sxs-lookup"><span data-stu-id="1b06b-384">Updated the database</span></span>

<span data-ttu-id="1b06b-385">Přejděte k dalším článku se dozvíte více o tom, jak přistupovat k související data.</span><span class="sxs-lookup"><span data-stu-id="1b06b-385">Advance to the next article to learn more about how to access related data.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="1b06b-386">Přístup k související data</span><span class="sxs-lookup"><span data-stu-id="1b06b-386">Access related data</span></span>](read-related-data.md)
