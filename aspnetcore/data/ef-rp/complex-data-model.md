---
title: Stránky Razor s EF Core v ASP.NET Core – Model dat – 5 z 8
author: rick-anderson
description: V tomto kurzu přidat další entity a relace a přizpůsobte si datový model zadáním formátování, ověřování a pravidel mapování.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 56c2d783e8c75aad7487aa03309bb962f4354567
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346525"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="43cd8-103">Stránky Razor s EF Core v ASP.NET Core – Model dat – 5 z 8</span><span class="sxs-lookup"><span data-stu-id="43cd8-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="43cd8-104">Podle [Petr Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="43cd8-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="43cd8-105">Z předchozích kurzů ve spolupráci s základní datový model, který se skládá z tři entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="43cd8-106">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="43cd8-106">In this tutorial:</span></span>

* <span data-ttu-id="43cd8-107">Jsou přidány další entit a vztahů.</span><span class="sxs-lookup"><span data-stu-id="43cd8-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="43cd8-108">Datový model je upravit tak, že zadáte formátování, ověřování a pravidel mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="43cd8-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="43cd8-109">Tříd entit pro dokončené datový model je vidět na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="43cd8-109">The entity classes for the completed data model is shown in the following illustration:</span></span>

![Entity diagram](complex-data-model/_static/diagram.png)

<span data-ttu-id="43cd8-111">Pokud narazíte na potíže nelze vyřešit, stáhněte si [dokončené aplikace](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="43cd8-111">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="43cd8-112">Přizpůsobte si datový model s atributy</span><span class="sxs-lookup"><span data-stu-id="43cd8-112">Customize the data model with attributes</span></span>

<span data-ttu-id="43cd8-113">V této části je datový model přizpůsobený pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="43cd8-113">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="43cd8-114">Datový typ atributu</span><span class="sxs-lookup"><span data-stu-id="43cd8-114">The DataType attribute</span></span>

<span data-ttu-id="43cd8-115">Na stránkách student aktuálně zobrazuje čas datum registrace.</span><span class="sxs-lookup"><span data-stu-id="43cd8-115">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="43cd8-116">Obvykle pole datum se zobrazí pouze datum a bez času.</span><span class="sxs-lookup"><span data-stu-id="43cd8-116">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="43cd8-117">Aktualizace *Models/Student.cs* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="43cd8-117">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="43cd8-118">[Datový typ](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) atribut určuje datový typ, který je specifičtější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="43cd8-118">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="43cd8-119">V tomto případě by měl zobrazit pouze data, ne datum a čas.</span><span class="sxs-lookup"><span data-stu-id="43cd8-119">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="43cd8-120">[Datový typ výčtu](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje pro mnoho typů dat, jako je například telefonní číslo, datum, čas, Měna, EmailAddress, atd. `DataType` Atribut můžete také povolit aplikaci automaticky poskytovat konkrétní typ funkce.</span><span class="sxs-lookup"><span data-stu-id="43cd8-120">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="43cd8-121">Příklad:</span><span class="sxs-lookup"><span data-stu-id="43cd8-121">For example:</span></span>

* <span data-ttu-id="43cd8-122">`mailto:` Propojení se automaticky vytvoří pro `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-122">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="43cd8-123">Výběr data se poskytuje pro `DataType.Date` u většiny prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="43cd8-123">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="43cd8-124">`DataType` Atribut vysílá HTML 5 `data-` (čteno data dash) atributy, které využívají prohlížeče HTML 5.</span><span class="sxs-lookup"><span data-stu-id="43cd8-124">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="43cd8-125">`DataType` Atributy neposkytují ověření.</span><span class="sxs-lookup"><span data-stu-id="43cd8-125">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="43cd8-126">`DataType.Date` neurčuje formátu, který se zobrazí datum.</span><span class="sxs-lookup"><span data-stu-id="43cd8-126">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="43cd8-127">Ve výchozím nastavení, zobrazí se pole datum podle výchozí formát založený na serveru [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span><span class="sxs-lookup"><span data-stu-id="43cd8-127">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="43cd8-128">`DisplayFormat` Atribut se používá s ohledem na formát data:</span><span class="sxs-lookup"><span data-stu-id="43cd8-128">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="43cd8-129">`ApplyFormatInEditMode` Nastavení určuje, že formátování také bude použito pro úpravy uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="43cd8-129">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="43cd8-130">Některá pole neměli používat `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-130">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="43cd8-131">Symbol měny například obecně nebude se zobrazovat text do textového pole.</span><span class="sxs-lookup"><span data-stu-id="43cd8-131">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="43cd8-132">`DisplayFormat` Atribut lze použít samostatně.</span><span class="sxs-lookup"><span data-stu-id="43cd8-132">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="43cd8-133">Obecně je vhodné použít `DataType` atributem `DisplayFormat` atribut.</span><span class="sxs-lookup"><span data-stu-id="43cd8-133">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="43cd8-134">`DataType` Atribut přenáší sémantiku dat na rozdíl od vykreslování na obrazovce.</span><span class="sxs-lookup"><span data-stu-id="43cd8-134">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="43cd8-135">`DataType` Atribut poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="43cd8-135">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="43cd8-136">Prohlížeči můžete povolit funkce HTML5.</span><span class="sxs-lookup"><span data-stu-id="43cd8-136">The browser can enable HTML5 features.</span></span> <span data-ttu-id="43cd8-137">Například zobrazení ovládacího prvku kalendář, symbol měny odpovídající národní prostředí, odkazy na e-mailu, vstupní ověřování na straně klienta, atd.</span><span class="sxs-lookup"><span data-stu-id="43cd8-137">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="43cd8-138">Ve výchozím prohlížečem data ve správném formátu podle národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="43cd8-138">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="43cd8-139">Další informace najdete v tématu [ \<vstupní > pomocná rutina značek v dokumentaci](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="43cd8-139">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="43cd8-140">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="43cd8-140">Run the app.</span></span> <span data-ttu-id="43cd8-141">Přejděte na stránku studenty indexu.</span><span class="sxs-lookup"><span data-stu-id="43cd8-141">Navigate to the Students Index page.</span></span> <span data-ttu-id="43cd8-142">Časy se už nezobrazují.</span><span class="sxs-lookup"><span data-stu-id="43cd8-142">Times are no longer displayed.</span></span> <span data-ttu-id="43cd8-143">Každé zobrazení, která používá `Student` model zobrazí datum bez času.</span><span class="sxs-lookup"><span data-stu-id="43cd8-143">Every view that uses the `Student` model displays the date without time.</span></span>

![Studenti indexová stránka zobrazující data bez časy](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="43cd8-145">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="43cd8-145">The StringLength attribute</span></span>

<span data-ttu-id="43cd8-146">S atributy lze zadat pravidla ověřování dat a chybové zprávy ověření.</span><span class="sxs-lookup"><span data-stu-id="43cd8-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="43cd8-147">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) atribut určuje minimální a maximální délku znaků, které jsou povoleny v datové pole.</span><span class="sxs-lookup"><span data-stu-id="43cd8-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="43cd8-148">`StringLength` Atribut také poskytuje ověřování na straně klienta i stranu serveru.</span><span class="sxs-lookup"><span data-stu-id="43cd8-148">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="43cd8-149">Minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="43cd8-149">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="43cd8-150">Aktualizace `Student` modelů s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="43cd8-150">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="43cd8-151">Předchozí kód omezení názvů, které se více než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="43cd8-151">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="43cd8-152">`StringLength` Atribut nemá zabránit uživateli v zadávání prázdné znaky pro název.</span><span class="sxs-lookup"><span data-stu-id="43cd8-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="43cd8-153">[Regulární výraz](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) atribut se používá k aplikování omezení na vstup.</span><span class="sxs-lookup"><span data-stu-id="43cd8-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="43cd8-154">Například následující kód vyžaduje prvního znaku na velká písmena a zbývající znaky, které mají být abecední znak:</span><span class="sxs-lookup"><span data-stu-id="43cd8-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="43cd8-155">Spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="43cd8-155">Run the app:</span></span>

* <span data-ttu-id="43cd8-156">Přejděte na stránku pro studenty.</span><span class="sxs-lookup"><span data-stu-id="43cd8-156">Navigate to the Students page.</span></span>
* <span data-ttu-id="43cd8-157">Vyberte **vytvořit nový**a zadejte název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="43cd8-157">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="43cd8-158">Vyberte **vytvořit**, zobrazí se chybová zpráva ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="43cd8-158">Select **Create**, client-side validation shows an error message.</span></span>

![Studenti index stránky zobrazující chyby délky řetězce](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="43cd8-160">V **Průzkumník objektů systému SQL Server** (SSOX), otevřete Návrhář tabulky Student dvojitým kliknutím **Student** tabulky.</span><span class="sxs-lookup"><span data-stu-id="43cd8-160">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulky Studenti v SSOX před migrací](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="43cd8-162">Předchozí obrázek znázorňuje schéma `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="43cd8-162">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="43cd8-163">Název pole mají typ `nvarchar(MAX)` protože migrace nebyla spuštěna v databázi.</span><span class="sxs-lookup"><span data-stu-id="43cd8-163">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="43cd8-164">Spuštění migrace později v tomto kurzu se název pole se stanou `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-164">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="43cd8-165">Atribut sloupce</span><span class="sxs-lookup"><span data-stu-id="43cd8-165">The Column attribute</span></span>

<span data-ttu-id="43cd8-166">Atributy můžete řídit, jak třídy a vlastnosti jsou namapovány na databázi.</span><span class="sxs-lookup"><span data-stu-id="43cd8-166">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="43cd8-167">V této části `Column` atribut se používá k mapování názvu `FirstMidName` vlastnost na "FirstName" v databázi.</span><span class="sxs-lookup"><span data-stu-id="43cd8-167">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="43cd8-168">Když se vytvoří databáze, názvy vlastností na modelu se používají pro názvy sloupců (s výjimkou, kdy `Column` atribut se používá).</span><span class="sxs-lookup"><span data-stu-id="43cd8-168">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="43cd8-169">`Student` Model používá `FirstMidName` pro název prvního pole, protože pole může obsahovat také křestní jméno.</span><span class="sxs-lookup"><span data-stu-id="43cd8-169">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="43cd8-170">Aktualizace *Student.cs* soubor s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="43cd8-170">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="43cd8-171">Předchozí změny `Student.FirstMidName` v aplikaci mapuje `FirstName` sloupec `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="43cd8-171">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="43cd8-172">Přidání `Column` atribut změní základní model `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="43cd8-173">Základní model `SchoolContext` už neodpovídá databázi.</span><span class="sxs-lookup"><span data-stu-id="43cd8-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="43cd8-174">Pokud spuštění aplikace před použitím migrace je vygenerována následující výjimka:</span><span class="sxs-lookup"><span data-stu-id="43cd8-174">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="43cd8-175">Aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="43cd8-175">To update the DB:</span></span>

* <span data-ttu-id="43cd8-176">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="43cd8-176">Build the project.</span></span>
* <span data-ttu-id="43cd8-177">Otevřete okno příkazového řádku ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="43cd8-177">Open a command window in the project folder.</span></span> <span data-ttu-id="43cd8-178">Zadejte následující příkazy k vytvoření nové migrace a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="43cd8-178">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43cd8-179">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43cd8-179">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="43cd8-180">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="43cd8-180">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

------

<span data-ttu-id="43cd8-181">`migrations add ColumnFirstName` Příkaz vygeneruje následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="43cd8-181">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="43cd8-182">Upozornění je generována, protože název pole jsou teď omezená na 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="43cd8-182">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="43cd8-183">Pokud název databáze do více než 50 znaků, by dojít ke ztrátě 51 poslední znak.</span><span class="sxs-lookup"><span data-stu-id="43cd8-183">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="43cd8-184">Testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="43cd8-184">Test the app.</span></span>

<span data-ttu-id="43cd8-185">Otevřete tabulku studentů v SSOX:</span><span class="sxs-lookup"><span data-stu-id="43cd8-185">Open the Student table in SSOX:</span></span>

![Tabulky Studenti v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="43cd8-187">Před migrací se použil, název sloupce byly typu [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="43cd8-187">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="43cd8-188">Název sloupce jsou nyní `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-188">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="43cd8-189">Změnil se název sloupce z `FirstMidName` k `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-189">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="43cd8-190">V následující části sestavení aplikace na několik fází generuje chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="43cd8-190">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="43cd8-191">Podle pokynů určit, kdy k sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="43cd8-191">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="43cd8-192">Aktualizovat entitu studenta</span><span class="sxs-lookup"><span data-stu-id="43cd8-192">Student entity update</span></span>

![Student entity](complex-data-model/_static/student-entity.png)

<span data-ttu-id="43cd8-194">Aktualizace *Models/Student.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="43cd8-194">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="43cd8-195">Požadovaný atribut</span><span class="sxs-lookup"><span data-stu-id="43cd8-195">The Required attribute</span></span>

<span data-ttu-id="43cd8-196">`Required` Atribut je povinná pole název vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="43cd8-196">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="43cd8-197">`Required` Atribut není potřeba pro typy neumožňující hodnotu, jako jsou typy hodnot (`DateTime`, `int`, `double`atd.).</span><span class="sxs-lookup"><span data-stu-id="43cd8-197">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="43cd8-198">Typy, které nemůže mít hodnotu null se automaticky považují za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="43cd8-198">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="43cd8-199">`Required` Atribut může nahrazena minimální délku parametru `StringLength` atribut:</span><span class="sxs-lookup"><span data-stu-id="43cd8-199">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="43cd8-200">Atribut zobrazení</span><span class="sxs-lookup"><span data-stu-id="43cd8-200">The Display attribute</span></span>

<span data-ttu-id="43cd8-201">`Display` Atribut určuje, že popisek pro textová pole by měl být "Jméno", "Last Name", "Jméno" a "Datum registrace."</span><span class="sxs-lookup"><span data-stu-id="43cd8-201">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="43cd8-202">Výchozí popisky neobsahoval mezeru dělení slova, třeba "Prijmeni".</span><span class="sxs-lookup"><span data-stu-id="43cd8-202">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="43cd8-203">Celý název počítané vlastnosti</span><span class="sxs-lookup"><span data-stu-id="43cd8-203">The FullName calculated property</span></span>

<span data-ttu-id="43cd8-204">`FullName` je počítaná vlastnost, která vrací hodnotu, která se vytváří zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="43cd8-204">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="43cd8-205">`FullName` Nelze nastavit, že obsahuje pouze přístupový objekt get.</span><span class="sxs-lookup"><span data-stu-id="43cd8-205">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="43cd8-206">Ne `FullName` sloupce se vytvoří v databázi.</span><span class="sxs-lookup"><span data-stu-id="43cd8-206">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="43cd8-207">Vytvoření Entity instruktorem</span><span class="sxs-lookup"><span data-stu-id="43cd8-207">Create the Instructor Entity</span></span>

![Entita instruktorem](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="43cd8-209">Vytvoření *Models/Instructor.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="43cd8-209">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="43cd8-210">Více atributů může být na jednom řádku.</span><span class="sxs-lookup"><span data-stu-id="43cd8-210">Multiple attributes can be on one line.</span></span> <span data-ttu-id="43cd8-211">`HireDate` Atributů může být napsaná následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="43cd8-211">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="43cd8-212">Navigační vlastnosti CourseAssignments a OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="43cd8-212">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="43cd8-213">`CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="43cd8-213">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="43cd8-214">Instruktorem můžete naučit libovolný počet kurzů, takže `CourseAssignments` je definovaná jako kolekce.</span><span class="sxs-lookup"><span data-stu-id="43cd8-214">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="43cd8-215">Pokud vlastnost navigace obsahuje více entit:</span><span class="sxs-lookup"><span data-stu-id="43cd8-215">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="43cd8-216">Musí být typu seznamu, kde položky lze přidat, odstranit a aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="43cd8-216">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="43cd8-217">Navigační vlastnost typy patří:</span><span class="sxs-lookup"><span data-stu-id="43cd8-217">Navigation property types include:</span></span>

* `ICollection<T>`
*  `List<T>`
*  `HashSet<T>`

<span data-ttu-id="43cd8-218">Pokud `ICollection<T>` není zadán, vytvoří EF Core `HashSet<T>` kolekcí ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="43cd8-218">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="43cd8-219">`CourseAssignment` Entity je vysvětleno v části u relací m: m.</span><span class="sxs-lookup"><span data-stu-id="43cd8-219">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="43cd8-220">Contoso University obchodní pravidla stát, že instruktorem může mít maximálně jeden office.</span><span class="sxs-lookup"><span data-stu-id="43cd8-220">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="43cd8-221">`OfficeAssignment` Vlastnost obsahuje jediný `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-221">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="43cd8-222">`OfficeAssignment` má hodnotu null, pokud není přiřazena žádná office.</span><span class="sxs-lookup"><span data-stu-id="43cd8-222">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="43cd8-223">Vytvoření OfficeAssignment entity</span><span class="sxs-lookup"><span data-stu-id="43cd8-223">Create the OfficeAssignment entity</span></span>

![OfficeAssignment entity](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="43cd8-225">Vytvoření *Models/OfficeAssignment.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="43cd8-225">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="43cd8-226">Atribut Key</span><span class="sxs-lookup"><span data-stu-id="43cd8-226">The Key attribute</span></span>

<span data-ttu-id="43cd8-227">`[Key]` Atribut se používá k identifikaci vlastnost jako primární klíč (PK) název vlastnosti je něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="43cd8-227">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="43cd8-228">Existuje jedna: nula nebo 1 vztah mezi `Instructor` a `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-228">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="43cd8-229">Přiřazení office existuje pouze ve vztahu k instruktorem, které je přiřazen.</span><span class="sxs-lookup"><span data-stu-id="43cd8-229">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="43cd8-230">`OfficeAssignment` PK má také svůj cizí klíč (Cizíklíč) `Instructor` entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-230">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="43cd8-231">EF Core nemůže automaticky rozpoznat `InstructorID` jako PK z `OfficeAssignment` protože:</span><span class="sxs-lookup"><span data-stu-id="43cd8-231">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="43cd8-232">`InstructorID` není podle ID nebo classnameID konvence.</span><span class="sxs-lookup"><span data-stu-id="43cd8-232">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="43cd8-233">Proto `Key` atribut se používá k identifikaci `InstructorID` jako primárnímu Klíči:</span><span class="sxs-lookup"><span data-stu-id="43cd8-233">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="43cd8-234">Ve výchozím nastavení EF Core považuje za klíč bez databáze vygenerovala protože sloupec je pro identifikující relaci.</span><span class="sxs-lookup"><span data-stu-id="43cd8-234">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="43cd8-235">Navigační vlastnost instruktorem</span><span class="sxs-lookup"><span data-stu-id="43cd8-235">The Instructor navigation property</span></span>

<span data-ttu-id="43cd8-236">`OfficeAssignment` Navigační vlastnost pro `Instructor` entita může mít hodnotu Null protože:</span><span class="sxs-lookup"><span data-stu-id="43cd8-236">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="43cd8-237">Referenční typy (jako jsou třídy s možnou hodnotou Null).</span><span class="sxs-lookup"><span data-stu-id="43cd8-237">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="43cd8-238">Instruktorem nemusí mít přiřazení kanceláře.</span><span class="sxs-lookup"><span data-stu-id="43cd8-238">An instructor might not have an office assignment.</span></span>


<span data-ttu-id="43cd8-239">`OfficeAssignment` Entita má zakázanou `Instructor` navigační vlastnost protože:</span><span class="sxs-lookup"><span data-stu-id="43cd8-239">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="43cd8-240">`InstructorID` hodnotu Null.</span><span class="sxs-lookup"><span data-stu-id="43cd8-240">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="43cd8-241">Přiřazení office nemůže existovat bez instruktorem.</span><span class="sxs-lookup"><span data-stu-id="43cd8-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="43cd8-242">Když `Instructor` entita má se souvisejícím `OfficeAssignment` entit, každá entita obsahuje odkaz na druhou v jeho navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="43cd8-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="43cd8-243">`[Required]` Atribut můžete uplatnit `Instructor` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="43cd8-243">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="43cd8-244">Předchozí kód určuje, že musí být související instruktorem.</span><span class="sxs-lookup"><span data-stu-id="43cd8-244">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="43cd8-245">Předchozí kód není nutný, protože `InstructorID` cizí klíč (což je také primárnímu Klíči) je null.</span><span class="sxs-lookup"><span data-stu-id="43cd8-245">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="43cd8-246">Upravit Entity kurzu</span><span class="sxs-lookup"><span data-stu-id="43cd8-246">Modify the Course Entity</span></span>

![Kurz entity](complex-data-model/_static/course-entity.png)

<span data-ttu-id="43cd8-248">Aktualizace *Models/Course.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="43cd8-248">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="43cd8-249">`Course` Entita má vlastnost cizí klíč (Cizíklíč) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-249">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="43cd8-250">`DepartmentID` odkazuje na související `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-250">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="43cd8-251">`Course` Má entita `Department` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="43cd8-251">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="43cd8-252">EF Core nevyžaduje vlastnosti cizího klíče pro daný datový model, pokud model nemá vlastnost navigace u související entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-252">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="43cd8-253">EF Core FKs automaticky vytvoří v databázi bez ohledu na to budete potřebovat.</span><span class="sxs-lookup"><span data-stu-id="43cd8-253">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="43cd8-254">EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automaticky vytvořené FKs.</span><span class="sxs-lookup"><span data-stu-id="43cd8-254">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="43cd8-255">Máte cizího klíče v datovém modelu můžete provést aktualizace, jednodušší a efektivnější.</span><span class="sxs-lookup"><span data-stu-id="43cd8-255">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="43cd8-256">Představte si třeba modelu kde vlastnost FK `DepartmentID` je *není* zahrnuté.</span><span class="sxs-lookup"><span data-stu-id="43cd8-256">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="43cd8-257">Když kurzu entity jsou načtena upravit:</span><span class="sxs-lookup"><span data-stu-id="43cd8-257">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="43cd8-258">`Department` Entity má hodnotu null, pokud nejsou explicitně načtení.</span><span class="sxs-lookup"><span data-stu-id="43cd8-258">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="43cd8-259">K aktualizaci kurzu entity `Department` musíte entitu nejdřív načíst.</span><span class="sxs-lookup"><span data-stu-id="43cd8-259">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="43cd8-260">Při vlastnost FK `DepartmentID` je zahrnuta v datovém modelu, není nutné načíst `Department` entity před aktualizace.</span><span class="sxs-lookup"><span data-stu-id="43cd8-260">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="43cd8-261">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="43cd8-261">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="43cd8-262">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` Atribut určuje, že primárnímu Klíči je poskytovaný aplikací místo generován databází.</span><span class="sxs-lookup"><span data-stu-id="43cd8-262">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="43cd8-263">Ve výchozím nastavení EF Core předpokládá, že PK hodnoty jsou generovány pomocí databáze.</span><span class="sxs-lookup"><span data-stu-id="43cd8-263">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="43cd8-264">DB generované PK hodnoty je obvykle nejlepší přístup.</span><span class="sxs-lookup"><span data-stu-id="43cd8-264">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="43cd8-265">Pro `Course` entity, uživatel Určuje, PK</span><span class="sxs-lookup"><span data-stu-id="43cd8-265">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="43cd8-266">Například kurzu číslo, například řadu 1000 pro oddělení matematické, řadu 2000 pro anglickou oddělení.</span><span class="sxs-lookup"><span data-stu-id="43cd8-266">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="43cd8-267">`DatabaseGenerated` Atribut lze použít také pro generování výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="43cd8-267">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="43cd8-268">Například databáze může automaticky generovat pole s datem a zaznamenávat data řádku byl vytvořen nebo aktualizován.</span><span class="sxs-lookup"><span data-stu-id="43cd8-268">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="43cd8-269">Další informace najdete v tématu [vygenerovaným vlastnostem](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="43cd8-269">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="43cd8-270">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="43cd8-270">Foreign key and navigation properties</span></span>

<span data-ttu-id="43cd8-271">Vlastnosti cizího klíče (Cizíklíč) a navigačních vlastností v `Course` entity zahrnují následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="43cd8-271">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="43cd8-272">Kurz je přiřazena jednoho oddělení, takže `DepartmentID` FK a `Department` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="43cd8-272">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="43cd8-273">Kurz můžete mít libovolný počet studentů zaregistrované, takže `Enrollments` navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="43cd8-273">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="43cd8-274">Kurz může být vedená instruktorů více, proto `CourseAssignments` navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="43cd8-274">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="43cd8-275">`CourseAssignment` je vysvětleno [později](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="43cd8-275">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="43cd8-276">Vytvoření entity oddělení</span><span class="sxs-lookup"><span data-stu-id="43cd8-276">Create the Department entity</span></span>

![Oddělení entity](complex-data-model/_static/department-entity.png)

<span data-ttu-id="43cd8-278">Vytvoření *Models/Department.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="43cd8-278">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="43cd8-279">Atribut sloupce</span><span class="sxs-lookup"><span data-stu-id="43cd8-279">The Column attribute</span></span>

<span data-ttu-id="43cd8-280">Dříve `Column` atributu byl použit Chcete-li změnit název mapování sloupců.</span><span class="sxs-lookup"><span data-stu-id="43cd8-280">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="43cd8-281">V kódu `Department` entity, `Column` atribut se používá ke změně mapování datového typu SQL.</span><span class="sxs-lookup"><span data-stu-id="43cd8-281">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="43cd8-282">`Budget` Sloupec je definovaný pomocí typ money systému SQL Server v databázi:</span><span class="sxs-lookup"><span data-stu-id="43cd8-282">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="43cd8-283">Mapování sloupce není obvykle potřeba.</span><span class="sxs-lookup"><span data-stu-id="43cd8-283">Column mapping is generally not required.</span></span> <span data-ttu-id="43cd8-284">EF Core obecně vybere odpovídající datový typ SQL serveru na základě typu CLR pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="43cd8-284">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="43cd8-285">Modul CLR `decimal` zadejte mapuje se na serveru SQL Server `decimal` typu.</span><span class="sxs-lookup"><span data-stu-id="43cd8-285">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="43cd8-286">`Budget` je pro měnu, a datový typ money je vhodnější pro měny.</span><span class="sxs-lookup"><span data-stu-id="43cd8-286">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="43cd8-287">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="43cd8-287">Foreign key and navigation properties</span></span>

<span data-ttu-id="43cd8-288">Vlastnosti cizího klíče a navigace zahrnují následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="43cd8-288">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="43cd8-289">Oddělení může nebo nemusí být správce.</span><span class="sxs-lookup"><span data-stu-id="43cd8-289">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="43cd8-290">Správce je vždy instruktorem.</span><span class="sxs-lookup"><span data-stu-id="43cd8-290">An administrator is always an instructor.</span></span> <span data-ttu-id="43cd8-291">Proto `InstructorID` vlastnost je zahrnutý jako FK k `Instructor` entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-291">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="43cd8-292">Navigační vlastnost jmenuje `Administrator` obsahuje, ale `Instructor` entity:</span><span class="sxs-lookup"><span data-stu-id="43cd8-292">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="43cd8-293">Otazník (?) v předchozím kódu určuje, že vlastnost může mít hodnotu Null.</span><span class="sxs-lookup"><span data-stu-id="43cd8-293">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="43cd8-294">Oddělení může mít mnoho kurzů, tedy navigační vlastnost kurzy:</span><span class="sxs-lookup"><span data-stu-id="43cd8-294">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="43cd8-295">Poznámka: Podle konvence EF Core umožňuje kaskádové odstranění pro Null FKs a vztahy many-to-many.</span><span class="sxs-lookup"><span data-stu-id="43cd8-295">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="43cd8-296">Kaskádové odstranění může způsobit Cyklické kaskádové odstranění pravidla.</span><span class="sxs-lookup"><span data-stu-id="43cd8-296">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="43cd8-297">Kruhový Kaskádové odstraňování pravidel způsobí, že při migraci se přidá výjimku.</span><span class="sxs-lookup"><span data-stu-id="43cd8-297">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="43cd8-298">Například pokud `Department.InstructorID` vlastnost nebyl definován jako s možnou hodnotou NULL:</span><span class="sxs-lookup"><span data-stu-id="43cd8-298">For example, if the `Department.InstructorID` property wasn't defined as nullable:</span></span>

* <span data-ttu-id="43cd8-299">EF Core nakonfiguruje kaskádové odstranění pravidla můžete odstranit instruktorem, když je odstraněn z oddělení.</span><span class="sxs-lookup"><span data-stu-id="43cd8-299">EF Core configures a cascade delete rule to delete the instructor when the department is deleted.</span></span>
* <span data-ttu-id="43cd8-300">Odstraňuje kurzů vedených při odstranění tohoto oddělení není zamýšlené chování.</span><span class="sxs-lookup"><span data-stu-id="43cd8-300">Deleting the instructor when the department is deleted isn't the intended behavior.</span></span>

<span data-ttu-id="43cd8-301">V případě potřeby obchodních pravidel `InstructorID` vlastnosti být null, použijte následující příkaz rozhraní API fluent:</span><span class="sxs-lookup"><span data-stu-id="43cd8-301">If business rules required the `InstructorID` property be non-nullable, use the following fluent API statement:</span></span>

 ```csharp
 modelBuilder.Entity<Department>()
    .HasOne(d => d.Administrator)
    .WithMany()
    .OnDelete(DeleteBehavior.Restrict)
 ```

<span data-ttu-id="43cd8-302">Předchozí kód zakáže kaskádové odstranění relace oddělení instruktorem.</span><span class="sxs-lookup"><span data-stu-id="43cd8-302">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="43cd8-303">Aktualizace registrace entity</span><span class="sxs-lookup"><span data-stu-id="43cd8-303">Update the Enrollment entity</span></span>

<span data-ttu-id="43cd8-304">Záznam registrace je pro jeden kurz provedenou na základě jedné studentů.</span><span class="sxs-lookup"><span data-stu-id="43cd8-304">An enrollment record is for one course taken by one student.</span></span>

![Registrace entity](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="43cd8-306">Aktualizace *Models/Enrollment.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="43cd8-306">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="43cd8-307">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="43cd8-307">Foreign key and navigation properties</span></span>

<span data-ttu-id="43cd8-308">Vlastnosti cizího klíče a navigačních vlastností zahrnují následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="43cd8-308">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="43cd8-309">Záznam registrace je pro jeden kurz, tedy `CourseID` vlastnosti cizího klíče a `Course` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="43cd8-309">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="43cd8-310">Záznam registrace je pro jeden student, tedy `StudentID` vlastnosti cizího klíče a `Student` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="43cd8-310">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="43cd8-311">Relace m: N</span><span class="sxs-lookup"><span data-stu-id="43cd8-311">Many-to-Many Relationships</span></span>

<span data-ttu-id="43cd8-312">Existuje vztah n: n mezi `Student` a `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-312">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="43cd8-313">`Enrollment` Entity funguje jako tabulka many-to-many spojení *s datovou částí* v databázi.</span><span class="sxs-lookup"><span data-stu-id="43cd8-313">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="43cd8-314">"S datovou částí" znamená, že `Enrollment` tabulka obsahuje další data kromě FKs pro spojené tabulky (v tomto případě primárnímu Klíči a `Grade`).</span><span class="sxs-lookup"><span data-stu-id="43cd8-314">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="43cd8-315">Následující obrázek znázorňuje, jak tyto vztahy vypadat v diagramu entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-315">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="43cd8-316">(Tento diagram se vygeneroval pomocí [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="43cd8-316">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="43cd8-317">Vytvoření diagramu, které nejsou součástí tohoto kurzu.)</span><span class="sxs-lookup"><span data-stu-id="43cd8-317">Creating the diagram isn't part of the tutorial.)</span></span>

![Kurz student mnoho na mnoho vztah](complex-data-model/_static/student-course.png)

<span data-ttu-id="43cd8-319">Každý řádek vztah má 1 na jednom konci a hvězdičku (\*) na druhém, určující vztah jeden mnoho.</span><span class="sxs-lookup"><span data-stu-id="43cd8-319">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="43cd8-320">Pokud `Enrollment` tabulky nezahrnuli informace na podnikové úrovni, třeba jenom tak, aby obsahovala dva FKs (`CourseID` a `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="43cd8-320">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="43cd8-321">Tabulku spojení many-to-many bez datové části se někdy nazývá čistě spojení tabulky (PJT).</span><span class="sxs-lookup"><span data-stu-id="43cd8-321">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="43cd8-322">`Instructor` a `Course` entity mají vztah many-to-many pomocí tabulky čistě spojení.</span><span class="sxs-lookup"><span data-stu-id="43cd8-322">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="43cd8-323">Poznámka: EF 6.x podporuje implicitní spojení tabulek pro vztahy many-to-many, ale EF Core nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="43cd8-323">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="43cd8-324">Další informace najdete v tématu [Many-to-many vztahy v EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="43cd8-324">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="43cd8-325">CourseAssignment entity</span><span class="sxs-lookup"><span data-stu-id="43cd8-325">The CourseAssignment entity</span></span>

![CourseAssignment entity](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="43cd8-327">Vytvoření *Models/CourseAssignment.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="43cd8-327">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="43cd8-328">Kurzů vedených kurzy</span><span class="sxs-lookup"><span data-stu-id="43cd8-328">Instructor-to-Courses</span></span>

![M:M kurzů vedených kurzy](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="43cd8-330">Relace many-to-many kurzů vedených kurzy:</span><span class="sxs-lookup"><span data-stu-id="43cd8-330">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="43cd8-331">Vyžaduje tabulku spojení, která musí být reprezentována sadu entit.</span><span class="sxs-lookup"><span data-stu-id="43cd8-331">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="43cd8-332">Je čistě vazební tabulka (tabulka bez datové části).</span><span class="sxs-lookup"><span data-stu-id="43cd8-332">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="43cd8-333">Je běžné název entity spojení `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-333">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="43cd8-334">Například tabulku spojení kurzů vedených kurzy použití tohoto modelu je `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-334">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="43cd8-335">Doporučujeme však použít název, který popisuje relace.</span><span class="sxs-lookup"><span data-stu-id="43cd8-335">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="43cd8-336">Datové modely začíná jednoduchou a zvýší.</span><span class="sxs-lookup"><span data-stu-id="43cd8-336">Data models start out simple and grow.</span></span> <span data-ttu-id="43cd8-337">Žádné datové spojení (PJTs) často vyvíjet zahrnout datové části.</span><span class="sxs-lookup"><span data-stu-id="43cd8-337">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="43cd8-338">Začněte s entity popisný název, název nemusí změnit při změně tabulku spojení.</span><span class="sxs-lookup"><span data-stu-id="43cd8-338">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="43cd8-339">Spojení entit v ideálním případě by mít svůj vlastní přirozené název (může být jediné slovo) v obchodní domény.</span><span class="sxs-lookup"><span data-stu-id="43cd8-339">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="43cd8-340">Například může knihy a zákazníci propojené s spojení entitu s názvem hodnocení.</span><span class="sxs-lookup"><span data-stu-id="43cd8-340">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="43cd8-341">Pro relaci many-to-many kurzů vedených – kurzy `CourseAssignment` je upřednostňované nad `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-341">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="43cd8-342">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="43cd8-342">Composite key</span></span>

<span data-ttu-id="43cd8-343">FKs nejsou s možnou hodnotou Null.</span><span class="sxs-lookup"><span data-stu-id="43cd8-343">FKs are not nullable.</span></span> <span data-ttu-id="43cd8-344">Dvě FKs v `CourseAssignment` (`InstructorID` a `CourseID`) společně jednoznačné identifikaci jednotlivých řádků `CourseAssignment` tabulky.</span><span class="sxs-lookup"><span data-stu-id="43cd8-344">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="43cd8-345">`CourseAssignment` nevyžaduje vyhrazený PK</span><span class="sxs-lookup"><span data-stu-id="43cd8-345">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="43cd8-346">`InstructorID` a `CourseID` vlastnosti fungovat jako složený PK</span><span class="sxs-lookup"><span data-stu-id="43cd8-346">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="43cd8-347">Jediný způsob, jak určit složené PKs na EF Core je *rozhraní fluent API*.</span><span class="sxs-lookup"><span data-stu-id="43cd8-347">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="43cd8-348">V další části ukazuje, jak nakonfigurovat složené PK</span><span class="sxs-lookup"><span data-stu-id="43cd8-348">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="43cd8-349">Složený klíč zajistí:</span><span class="sxs-lookup"><span data-stu-id="43cd8-349">The composite key ensures:</span></span>

* <span data-ttu-id="43cd8-350">Více řádků jsou povoleny pro jeden kurz.</span><span class="sxs-lookup"><span data-stu-id="43cd8-350">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="43cd8-351">Více řádků jsou povoleny pro jeden instruktorem.</span><span class="sxs-lookup"><span data-stu-id="43cd8-351">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="43cd8-352">Více řádků pro stejné instruktorem a kurzu není povoleno.</span><span class="sxs-lookup"><span data-stu-id="43cd8-352">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="43cd8-353">`Enrollment` Entity spojení definuje vlastní PK tak, aby byly možné duplicity toto řazení.</span><span class="sxs-lookup"><span data-stu-id="43cd8-353">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="43cd8-354">Aby se tyto duplicitní hodnoty:</span><span class="sxs-lookup"><span data-stu-id="43cd8-354">To prevent such duplicates:</span></span>

* <span data-ttu-id="43cd8-355">Přidat jedinečný index pro pole cizího klíče nebo</span><span class="sxs-lookup"><span data-stu-id="43cd8-355">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="43cd8-356">Konfigurace `Enrollment` s primární složený klíč podobný `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-356">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="43cd8-357">Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="43cd8-357">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="43cd8-358">Aktualizovat kontext databáze</span><span class="sxs-lookup"><span data-stu-id="43cd8-358">Update the DB context</span></span>

<span data-ttu-id="43cd8-359">Přidejte následující zvýrazněný kód do *Data/SchoolContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="43cd8-359">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="43cd8-360">Předchozí kód přidá nové entity a nakonfiguruje `CourseAssignment` složené PK entity</span><span class="sxs-lookup"><span data-stu-id="43cd8-360">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="43cd8-361">Fluent API alternativou k atributům</span><span class="sxs-lookup"><span data-stu-id="43cd8-361">Fluent API alternative to attributes</span></span>

<span data-ttu-id="43cd8-362">`OnModelCreating` Metoda v předchozím kódu používá *rozhraní fluent API* konfigurace chování EF Core.</span><span class="sxs-lookup"><span data-stu-id="43cd8-362">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="43cd8-363">Rozhraní API se nazývá "fluent", protože je často používána zavěšování řadu volání metody společně na jediném příkazu.</span><span class="sxs-lookup"><span data-stu-id="43cd8-363">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="43cd8-364">[Následující kód](/ef/core/modeling/#methods-of-configuration) je příkladem rozhraní fluent API:</span><span class="sxs-lookup"><span data-stu-id="43cd8-364">The [following code](/ef/core/modeling/#methods-of-configuration) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="43cd8-365">V tomto kurzu se používá rozhraní fluent API pouze pro mapování databáze, které nelze provést s atributy.</span><span class="sxs-lookup"><span data-stu-id="43cd8-365">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="43cd8-366">Rozhraní fluent API můžete však určit většinu formátování, ověřování a pravidla mapování, které lze provést s atributy.</span><span class="sxs-lookup"><span data-stu-id="43cd8-366">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="43cd8-367">Některé atributy, jako `MinimumLength` nelze použít s rozhraním API fluent.</span><span class="sxs-lookup"><span data-stu-id="43cd8-367">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="43cd8-368">`MinimumLength` nedojde ke změně schématu, vztahuje se pouze minimální délka ověřovacího pravidla.</span><span class="sxs-lookup"><span data-stu-id="43cd8-368">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="43cd8-369">Někteří vývojáři dávají přednost používání rozhraní fluent API výhradně tak, aby se zachovat jejich tříd entit "vyčištění."</span><span class="sxs-lookup"><span data-stu-id="43cd8-369">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="43cd8-370">Atributy a rozhraní fluent API lze kombinovat.</span><span class="sxs-lookup"><span data-stu-id="43cd8-370">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="43cd8-371">Existují některé konfigurace, které lze provést pouze pomocí rozhraní fluent API (výběr kompozitní PK).</span><span class="sxs-lookup"><span data-stu-id="43cd8-371">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="43cd8-372">Existují některé konfigurace, které lze provést pouze s atributy (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="43cd8-372">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="43cd8-373">Doporučené postupy pro využití fluent API nebo atributy:</span><span class="sxs-lookup"><span data-stu-id="43cd8-373">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="43cd8-374">Zvolte jednu z těchto dvou přístupů.</span><span class="sxs-lookup"><span data-stu-id="43cd8-374">Choose one of these two approaches.</span></span>
* <span data-ttu-id="43cd8-375">Použijte konzistentně dosahovat zvolený způsob.</span><span class="sxs-lookup"><span data-stu-id="43cd8-375">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="43cd8-376">Některé atributy použité v tomto kurzu se používají pro:</span><span class="sxs-lookup"><span data-stu-id="43cd8-376">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="43cd8-377">Pouze ověřování (například `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="43cd8-377">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="43cd8-378">EF Core jenom konfiguraci (například `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="43cd8-378">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="43cd8-379">Konfigurace ověření a EF Core (například `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="43cd8-379">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="43cd8-380">Další informace o atributech vs. rozhraní fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/#methods-of-configuration).</span><span class="sxs-lookup"><span data-stu-id="43cd8-380">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/#methods-of-configuration).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="43cd8-381">Diagram znázorňující entitami</span><span class="sxs-lookup"><span data-stu-id="43cd8-381">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="43cd8-382">Následující obrázek znázorňuje diagram, který EF Power Tools vytvořit pro dokončené model školy.</span><span class="sxs-lookup"><span data-stu-id="43cd8-382">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Entity diagram](complex-data-model/_static/diagram.png)

<span data-ttu-id="43cd8-384">Předchozí diagram znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="43cd8-384">The preceding diagram shows:</span></span>

* <span data-ttu-id="43cd8-385">Několik řádků vztah jeden mnoho (1 k \*).</span><span class="sxs-lookup"><span data-stu-id="43cd8-385">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="43cd8-386">Řádek jedna nula nebo 1 v relaci m (1-0..1) mezi `Instructor` a `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-386">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="43cd8-387">Čára relace nula nebo 1 n (0.. 1 na \*) mezi `Instructor` a `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-387">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="43cd8-388">Počáteční hodnota databáze se testovací Data</span><span class="sxs-lookup"><span data-stu-id="43cd8-388">Seed the DB with Test Data</span></span>

<span data-ttu-id="43cd8-389">Aktualizovat kód v *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="43cd8-389">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="43cd8-390">Předchozí kód poskytuje data počáteční hodnotu pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-390">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="43cd8-391">Většina tento kód vytvoří nové objekty entity a načte ukázková data.</span><span class="sxs-lookup"><span data-stu-id="43cd8-391">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="43cd8-392">Ukázková data se používá pro účely testování.</span><span class="sxs-lookup"><span data-stu-id="43cd8-392">The sample data is used for testing.</span></span> <span data-ttu-id="43cd8-393">Zobrazit `Enrollments` a `CourseAssignments` pro příklady jak many-to-many spojení tabulek můžete nasadí.</span><span class="sxs-lookup"><span data-stu-id="43cd8-393">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="43cd8-394">Přidejte migraci</span><span class="sxs-lookup"><span data-stu-id="43cd8-394">Add a migration</span></span>

<span data-ttu-id="43cd8-395">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="43cd8-395">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43cd8-396">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43cd8-396">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="43cd8-397">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="43cd8-397">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ComplexDataModel
```

------

<span data-ttu-id="43cd8-398">Předchozí příkaz zobrazí varování týkající se ke ztrátě.</span><span class="sxs-lookup"><span data-stu-id="43cd8-398">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="43cd8-399">Pokud `database update` příkaz spustit, je vytvořen následující chybu:</span><span class="sxs-lookup"><span data-stu-id="43cd8-399">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="43cd8-400">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="43cd8-400">Apply the migration</span></span>

<span data-ttu-id="43cd8-401">Teď, když máte existující databázi, musíte přemýšlet o tom, jak na ně vztahují budoucí změny.</span><span class="sxs-lookup"><span data-stu-id="43cd8-401">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="43cd8-402">Tento kurz ukazuje dva přístupy:</span><span class="sxs-lookup"><span data-stu-id="43cd8-402">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="43cd8-403">Vyřadit a znovu vytvořit databázi</span><span class="sxs-lookup"><span data-stu-id="43cd8-403">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="43cd8-404">[Použití migrace k existující databázi](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="43cd8-404">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="43cd8-405">Tato metoda je složité a časově náročné, je upřednostňovaný způsob pro každodenní praxe produkční prostředí.</span><span class="sxs-lookup"><span data-stu-id="43cd8-405">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="43cd8-406">**Poznámka:**: Toto je volitelné části tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="43cd8-406">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="43cd8-407">Můžete provést rozevírací a znovu vytvořit kroky a tuto část přeskočit.</span><span class="sxs-lookup"><span data-stu-id="43cd8-407">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="43cd8-408">Pokud chcete postupovat podle kroků v této části, nemáte proveďte rozevírací nabídku a znovu vytvořit kroky.</span><span class="sxs-lookup"><span data-stu-id="43cd8-408">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="43cd8-409">Vyřadit a znovu vytvořit databázi</span><span class="sxs-lookup"><span data-stu-id="43cd8-409">Drop and re-create the database</span></span>

<span data-ttu-id="43cd8-410">Kód v aktualizovaném `DbInitializer` přidá data počáteční hodnotu pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="43cd8-410">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="43cd8-411">Pokud chcete vynutit EF Core k vytvoření nové databáze, vyřaďte a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="43cd8-411">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43cd8-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43cd8-412">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="43cd8-413">V **Konzola správce balíčků** (PMC), spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="43cd8-413">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="43cd8-414">Spustit `Get-Help about_EntityFrameworkCore` z konzole PMC zobrazíte nápovědu.</span><span class="sxs-lookup"><span data-stu-id="43cd8-414">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="43cd8-415">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="43cd8-415">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="43cd8-416">Otevřete okno příkazového řádku a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="43cd8-416">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="43cd8-417">Obsahuje složky projektu *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="43cd8-417">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="43cd8-418">V příkazovém okně zadejte následující:</span><span class="sxs-lookup"><span data-stu-id="43cd8-418">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

------

<span data-ttu-id="43cd8-419">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="43cd8-419">Run the app.</span></span> <span data-ttu-id="43cd8-420">Spuštění aplikace spuštěná `DbInitializer.Initialize` metody.</span><span class="sxs-lookup"><span data-stu-id="43cd8-420">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="43cd8-421">`DbInitializer.Initialize` Naplní nová databáze.</span><span class="sxs-lookup"><span data-stu-id="43cd8-421">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="43cd8-422">Otevřete v SSOX databáze:</span><span class="sxs-lookup"><span data-stu-id="43cd8-422">Open the DB in SSOX:</span></span>

* <span data-ttu-id="43cd8-423">Pokud SSOX byl dříve otevřen, klikněte na tlačítko **aktualizovat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="43cd8-423">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="43cd8-424">Rozbalte **tabulky** uzlu.</span><span class="sxs-lookup"><span data-stu-id="43cd8-424">Expand the **Tables** node.</span></span> <span data-ttu-id="43cd8-425">Vytvořené tabulky se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="43cd8-425">The created tables are displayed.</span></span>

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="43cd8-427">Zkontrolujte **CourseAssignment** tabulky:</span><span class="sxs-lookup"><span data-stu-id="43cd8-427">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="43cd8-428">Klikněte pravým tlačítkem myši **CourseAssignment** tabulce a vybrat **Data zobrazení**.</span><span class="sxs-lookup"><span data-stu-id="43cd8-428">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="43cd8-429">Ověřte, **CourseAssignment** tabulka obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="43cd8-429">Verify the **CourseAssignment** table contains data.</span></span>

![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="43cd8-431">Použití migrace k existující databázi</span><span class="sxs-lookup"><span data-stu-id="43cd8-431">Apply the migration to the existing database</span></span>

<span data-ttu-id="43cd8-432">Tato část je nepovinná.</span><span class="sxs-lookup"><span data-stu-id="43cd8-432">This section is optional.</span></span> <span data-ttu-id="43cd8-433">Tento postup funguje pouze v případě, že jste přeskočili předchozí [vyřaďte a znovu vytvořit databázi](#drop) oddílu.</span><span class="sxs-lookup"><span data-stu-id="43cd8-433">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="43cd8-434">Při spuštění migrace s existujícími daty, může být omezení cizího klíče, které nejsou splněné s existujícími daty.</span><span class="sxs-lookup"><span data-stu-id="43cd8-434">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="43cd8-435">S použitím provozních dat musí být kroky potřebného k migraci existujících dat.</span><span class="sxs-lookup"><span data-stu-id="43cd8-435">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="43cd8-436">Tato část poskytuje příklad opravuje narušení omezení cizího klíče.</span><span class="sxs-lookup"><span data-stu-id="43cd8-436">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="43cd8-437">Neprovádějte změny kódu bez předchozího provedení zálohy.</span><span class="sxs-lookup"><span data-stu-id="43cd8-437">Don't make these code changes without a backup.</span></span> <span data-ttu-id="43cd8-438">Nenastavujte tyto změny kódu, je-li dokončit předchozí části a aktualizována v databázi.</span><span class="sxs-lookup"><span data-stu-id="43cd8-438">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="43cd8-439">*{Timestamp}_ComplexDataModel.cs* soubor obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="43cd8-439">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="43cd8-440">Předchozí kód přidá neumožňující `DepartmentID` FK k `Course` tabulky.</span><span class="sxs-lookup"><span data-stu-id="43cd8-440">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="43cd8-441">Databáze z předchozí kurz o službě obsahuje řádky v `Course`, takže tuto tabulku nelze aktualizovat migrace.</span><span class="sxs-lookup"><span data-stu-id="43cd8-441">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="43cd8-442">Chcete-li `ComplexDataModel` migraci vám nebudeme nic s existujícími daty:</span><span class="sxs-lookup"><span data-stu-id="43cd8-442">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="43cd8-443">Změňte kód a zadejte nový sloupec (`DepartmentID`) výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="43cd8-443">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="43cd8-444">Vytvořte falešnou oddělení s názvem "Temp" tak, aby fungoval jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="43cd8-444">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="43cd8-445">Oprava omezení cizího klíče</span><span class="sxs-lookup"><span data-stu-id="43cd8-445">Fix the foreign key constraints</span></span>

<span data-ttu-id="43cd8-446">Aktualizace `ComplexDataModel` třídy `Up` metody:</span><span class="sxs-lookup"><span data-stu-id="43cd8-446">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="43cd8-447">Otevřít *{timestamp}_ComplexDataModel.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="43cd8-447">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="43cd8-448">Odkomentujte řádek kódu, který se přidá `DepartmentID` sloupec, který se `Course` tabulky.</span><span class="sxs-lookup"><span data-stu-id="43cd8-448">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="43cd8-449">Přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="43cd8-449">Add the following highlighted code.</span></span> <span data-ttu-id="43cd8-450">Nový kód prochází po `.CreateTable( name: "Department"` blok:</span><span class="sxs-lookup"><span data-stu-id="43cd8-450">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="43cd8-451">S předchozím změní, stávající `Course` řádky se související s oddělení "Temp" po `ComplexDataModel` `Up` metoda spuštění.</span><span class="sxs-lookup"><span data-stu-id="43cd8-451">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="43cd8-452">Produkční aplikace bude:</span><span class="sxs-lookup"><span data-stu-id="43cd8-452">A production app would:</span></span>

* <span data-ttu-id="43cd8-453">Zahrnout kódu nebo skriptech, chcete-li přidat `Department` řádky a související `Course` řádky k novému `Department` řádků.</span><span class="sxs-lookup"><span data-stu-id="43cd8-453">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="43cd8-454">Nepoužívat oddělení "Temp" nebo výchozí hodnotu pro `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="43cd8-454">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="43cd8-455">Další kurz se zaměřuje na související data.</span><span class="sxs-lookup"><span data-stu-id="43cd8-455">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43cd8-456">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="43cd8-456">Additional resources</span></span>

* [<span data-ttu-id="43cd8-457">YouTube verzi tohoto kurzu (část 1)</span><span class="sxs-lookup"><span data-stu-id="43cd8-457">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="43cd8-458">YouTube verzi tohoto kurzu (část 2)</span><span class="sxs-lookup"><span data-stu-id="43cd8-458">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

::: moniker-end

> [!div class="step-by-step"]
> <span data-ttu-id="43cd8-459">[Předchozí](xref:data/ef-rp/migrations)
> [další](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="43cd8-459">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>
