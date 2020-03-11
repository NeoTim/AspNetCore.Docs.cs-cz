---
title: Razor Pages s EF Core v ASP.NET Core-datový model 5 z 8
author: rick-anderson
description: V tomto kurzu přidejte další entity a vztahy a upravte datový model zadáním formátování, ověřování a pravidel mapování.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1d81a0444487c6396bb32381ed2cb26d44312c3a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665716"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="4d055-103">Razor Pages s EF Core v ASP.NET Core-datový model 5 z 8</span><span class="sxs-lookup"><span data-stu-id="4d055-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="4d055-104">[Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4d055-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4d055-105">Předchozí kurzy pracovaly se základním datovým modelem, který se skládá ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="4d055-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="4d055-106">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="4d055-106">In this tutorial:</span></span>

* <span data-ttu-id="4d055-107">Přidávají se další entity a vztahy.</span><span class="sxs-lookup"><span data-stu-id="4d055-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="4d055-108">Datový model je přizpůsoben zadáním pravidel formátování, ověřování a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="4d055-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="4d055-109">Dokončený datový model je zobrazený na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="4d055-109">The completed data model is shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="4d055-111">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="4d055-111">The Student entity</span></span>

![Entita studenta](complex-data-model/_static/student-entity.png)

<span data-ttu-id="4d055-113">Nahraďte kód v *modelů/student. cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="4d055-114">Předchozí kód přidá vlastnost `FullName` a přidá následující atributy do existujících vlastností:</span><span class="sxs-lookup"><span data-stu-id="4d055-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="4d055-115">Vypočítaná vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="4d055-115">The FullName calculated property</span></span>

<span data-ttu-id="4d055-116">`FullName` je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="4d055-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="4d055-117">`FullName` nelze nastavit, takže má pouze přistupující objekt get.</span><span class="sxs-lookup"><span data-stu-id="4d055-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="4d055-118">V databázi není vytvořen žádný `FullName` sloupec.</span><span class="sxs-lookup"><span data-stu-id="4d055-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="4d055-119">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="4d055-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="4d055-120">Pro data o registraci studenta aktuálně zobrazují všechny stránky denní dobu a datum, i když se jedná o relevantní datum.</span><span class="sxs-lookup"><span data-stu-id="4d055-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="4d055-121">Pomocí atributů datových poznámek můžete vytvořit jednu změnu kódu, která bude opravovat formát zobrazení na každé stránce, která zobrazuje data.</span><span class="sxs-lookup"><span data-stu-id="4d055-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="4d055-122">Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) Určuje datový typ, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="4d055-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="4d055-123">V tomto případě by se měla zobrazit pouze datum, nikoli datum a čas.</span><span class="sxs-lookup"><span data-stu-id="4d055-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="4d055-124">[Výčet DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress atd. Atribut `DataType` může také aplikaci povolit automatické poskytování funkcí specifických pro typ.</span><span class="sxs-lookup"><span data-stu-id="4d055-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="4d055-125">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4d055-125">For example:</span></span>

* <span data-ttu-id="4d055-126">Odkaz `mailto:` se automaticky vytvoří pro `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="4d055-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="4d055-127">Selektor data je k dispozici pro `DataType.Date` ve většině prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="4d055-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="4d055-128">Atribut `DataType` emituje atributy HTML 5 `data-` (vyslovení data pomlčky).</span><span class="sxs-lookup"><span data-stu-id="4d055-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="4d055-129">Atributy `DataType` neposkytují ověřování.</span><span class="sxs-lookup"><span data-stu-id="4d055-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="4d055-130">Atribut DisplayFormat</span><span class="sxs-lookup"><span data-stu-id="4d055-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="4d055-131">`DataType.Date` neurčuje formát data, které se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="4d055-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="4d055-132">Ve výchozím nastavení se pole Datum zobrazuje v závislosti na výchozích formátech na základě objektu [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serveru.</span><span class="sxs-lookup"><span data-stu-id="4d055-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="4d055-133">Atribut `DisplayFormat` slouží k explicitnímu zadání formátu data.</span><span class="sxs-lookup"><span data-stu-id="4d055-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="4d055-134">Nastavení `ApplyFormatInEditMode` určuje, zda má být formátování použito také pro uživatelské rozhraní úprav.</span><span class="sxs-lookup"><span data-stu-id="4d055-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="4d055-135">Některá pole by neměla používat `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="4d055-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="4d055-136">Například symbol měny by neměl být v textovém poli pro úpravy obvykle zobrazen.</span><span class="sxs-lookup"><span data-stu-id="4d055-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="4d055-137">Atribut `DisplayFormat` může používat sám sebe.</span><span class="sxs-lookup"><span data-stu-id="4d055-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="4d055-138">Obecně je vhodné použít atribut `DataType` s atributem `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="4d055-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="4d055-139">Atribut `DataType` předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce.</span><span class="sxs-lookup"><span data-stu-id="4d055-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="4d055-140">Atribut `DataType` poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="4d055-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="4d055-141">Prohlížeč může povolit funkce HTML5.</span><span class="sxs-lookup"><span data-stu-id="4d055-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="4d055-142">Například můžete zobrazit ovládací prvek kalendáře, symbol měny odpovídající národním prostředí, e-mailové odkazy a ověření vstupu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="4d055-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="4d055-143">Ve výchozím nastavení prohlížeč vykresluje data pomocí správného formátu založeného na národním prostředí.</span><span class="sxs-lookup"><span data-stu-id="4d055-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="4d055-144">Další informace najdete v dokumentaci k [pomocníka značek\<input >](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4d055-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="4d055-145">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="4d055-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="4d055-146">Pravidla ověřování dat a chybové zprávy ověřování lze zadat pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="4d055-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="4d055-147">Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli.</span><span class="sxs-lookup"><span data-stu-id="4d055-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="4d055-148">Zobrazený kód omezuje názvy na více než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="4d055-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="4d055-149">Příklad, který nastaví minimální délku řetězce, se zobrazí [později](#the-required-attribute).</span><span class="sxs-lookup"><span data-stu-id="4d055-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="4d055-150">Atribut `StringLength` také poskytuje ověřování na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="4d055-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="4d055-151">Minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="4d055-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="4d055-152">Atribut `StringLength` nebrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="4d055-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="4d055-153">Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) lze použít k uplatnění omezení na vstup.</span><span class="sxs-lookup"><span data-stu-id="4d055-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="4d055-154">Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:</span><span class="sxs-lookup"><span data-stu-id="4d055-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="4d055-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d055-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4d055-156">V **Průzkumník objektů systému SQL Server** (SSOX) otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .</span><span class="sxs-lookup"><span data-stu-id="4d055-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů v SSOX před migracemi](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="4d055-158">Na předchozím obrázku je znázorněno schéma `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="4d055-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="4d055-159">Pole název mají typ `nvarchar(MAX)`.</span><span class="sxs-lookup"><span data-stu-id="4d055-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="4d055-160">Když se vytvoří migrace a použije se později v tomto kurzu, pole název se stanou `nvarchar(50)` v důsledku atributů délky řetězce.</span><span class="sxs-lookup"><span data-stu-id="4d055-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d055-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d055-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d055-162">V nástroji SQLite si Projděte definice sloupců `Student` tabulce.</span><span class="sxs-lookup"><span data-stu-id="4d055-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="4d055-163">Pole název mají typ `Text`.</span><span class="sxs-lookup"><span data-stu-id="4d055-163">The name fields have type `Text`.</span></span> <span data-ttu-id="4d055-164">Všimněte si, že pole jméno se nazývá `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="4d055-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="4d055-165">V další části změníte název tohoto sloupce na `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="4d055-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="4d055-166">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="4d055-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="4d055-167">Atributy mohou řídit způsob, jakým jsou třídy a vlastnosti mapovány na databázi.</span><span class="sxs-lookup"><span data-stu-id="4d055-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="4d055-168">V modelu `Student` se k namapování názvu vlastnosti `FirstMidName` na FirstName v databázi používá atribut `Column`.</span><span class="sxs-lookup"><span data-stu-id="4d055-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="4d055-169">Při vytvoření databáze se názvy vlastností v modelu používají pro názvy sloupců (kromě případu, kdy se používá atribut `Column`).</span><span class="sxs-lookup"><span data-stu-id="4d055-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="4d055-170">`Student` model používá `FirstMidName` pro pole jméno a příjmení, protože pole může obsahovat také prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="4d055-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="4d055-171">Pomocí atributu `[Column]` `Student.FirstMidName` v datovém modelu mapuje na sloupec `FirstName` tabulky `Student`.</span><span class="sxs-lookup"><span data-stu-id="4d055-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="4d055-172">Přidání atributu `Column` změní model zálohování `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="4d055-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="4d055-173">Model zálohovaný `SchoolContext` se už neshoduje s databází.</span><span class="sxs-lookup"><span data-stu-id="4d055-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="4d055-174">Tato nesrovnalost bude vyřešena přidáním migrace později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="4d055-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="4d055-175">Požadovaný atribut</span><span class="sxs-lookup"><span data-stu-id="4d055-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="4d055-176">Atribut `Required` vytváří pole vlastnosti názvu Required.</span><span class="sxs-lookup"><span data-stu-id="4d055-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="4d055-177">Atribut `Required` není potřebný pro typy, které nejsou null, jako jsou například typy hodnot (například `DateTime`, `int`a `double`).</span><span class="sxs-lookup"><span data-stu-id="4d055-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="4d055-178">Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="4d055-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="4d055-179">Atribut `Required` musí být použit s `MinimumLength` pro vymáhání `MinimumLength`.</span><span class="sxs-lookup"><span data-stu-id="4d055-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="4d055-180">`MinimumLength` a `Required` umožňují, aby bylo prázdné znaky vyhovět ověřování.</span><span class="sxs-lookup"><span data-stu-id="4d055-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="4d055-181">Použijte atribut `RegularExpression` pro úplnou kontrolu nad řetězcem.</span><span class="sxs-lookup"><span data-stu-id="4d055-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="4d055-182">Atribut zobrazení</span><span class="sxs-lookup"><span data-stu-id="4d055-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="4d055-183">Atribut `Display` určuje, že titulek pro textová pole by měl být "jméno", "příjmení", "celé jméno" a "datum zápisu".</span><span class="sxs-lookup"><span data-stu-id="4d055-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="4d055-184">Výchozí titulky neobsahovaly místo dělení slov, například "LastName".</span><span class="sxs-lookup"><span data-stu-id="4d055-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="4d055-185">Vytvoření migrace</span><span class="sxs-lookup"><span data-stu-id="4d055-185">Create a migration</span></span>

<span data-ttu-id="4d055-186">Spusťte aplikaci a pokračujte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="4d055-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="4d055-187">Je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="4d055-187">An exception is thrown.</span></span> <span data-ttu-id="4d055-188">Atribut `[Column]` způsobuje, že EF očekává, že najde sloupec s názvem `FirstName`, ale název sloupce v databázi je stále `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="4d055-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d055-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d055-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4d055-190">Chybová zpráva je podobná následujícímu příkladu:</span><span class="sxs-lookup"><span data-stu-id="4d055-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="4d055-191">Do PMC zadejte následující příkazy, abyste vytvořili novou migraci a aktualizovali databázi:</span><span class="sxs-lookup"><span data-stu-id="4d055-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="4d055-192">První z těchto příkazů generuje následující varovnou zprávu:</span><span class="sxs-lookup"><span data-stu-id="4d055-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="4d055-193">Upozornění je vygenerováno, protože pole s názvem jsou nyní omezena na 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="4d055-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="4d055-194">Pokud má název v databázi více než 50 znaků, bude ztraceno 51 k poslednímu znaku.</span><span class="sxs-lookup"><span data-stu-id="4d055-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="4d055-195">Otevřete tabulku student v SSOX:</span><span class="sxs-lookup"><span data-stu-id="4d055-195">Open the Student table in SSOX:</span></span>

  ![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="4d055-197">Před použitím migrace byly sloupce názvu typu [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="4d055-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="4d055-198">Sloupce s názvem jsou nyní `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="4d055-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="4d055-199">Název sloupce se změnil z `FirstMidName` na `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="4d055-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d055-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d055-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d055-201">Chybová zpráva je podobná následujícímu příkladu:</span><span class="sxs-lookup"><span data-stu-id="4d055-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="4d055-202">Otevřete okno příkazového řádku ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="4d055-202">Open a command window in the project folder.</span></span> <span data-ttu-id="4d055-203">Zadáním následujících příkazů vytvořte novou migraci a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="4d055-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="4d055-204">Příkaz aktualizace databáze zobrazí chybu jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4d055-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="4d055-205">Pro tento kurz se způsobem, jak se tato chyba zobrazí po této chybě, odstranit a znovu vytvořit počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="4d055-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="4d055-206">Další informace najdete v poznámkovém upozornění SQLite v horní části [kurzu migrace](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="4d055-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="4d055-207">Odstraňte složku *migrace* .</span><span class="sxs-lookup"><span data-stu-id="4d055-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="4d055-208">Spusťte následující příkazy k vyřazení databáze, vytvořte novou počáteční migraci a použijte migraci:</span><span class="sxs-lookup"><span data-stu-id="4d055-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="4d055-209">Projděte si tabulku student v nástroji SQLite.</span><span class="sxs-lookup"><span data-stu-id="4d055-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="4d055-210">Sloupec, který byl FirstMidName, je nyní FirstName.</span><span class="sxs-lookup"><span data-stu-id="4d055-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="4d055-211">Spusťte aplikaci a pokračujte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="4d055-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="4d055-212">Všimněte si, že časy nejsou vstupní ani se nezobrazují spolu s kalendářními daty.</span><span class="sxs-lookup"><span data-stu-id="4d055-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="4d055-213">Vyberte **vytvořit novou**a zkuste zadat název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="4d055-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="4d055-214">V následujících oddílech sestavení aplikace v některých fázích generuje chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="4d055-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="4d055-215">Pokyny určují, kdy se má aplikace sestavit.</span><span class="sxs-lookup"><span data-stu-id="4d055-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="4d055-216">Entita instruktor</span><span class="sxs-lookup"><span data-stu-id="4d055-216">The Instructor Entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="4d055-218">Vytvořte *modely/Instructor. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="4d055-219">Více atributů může být na jednom řádku.</span><span class="sxs-lookup"><span data-stu-id="4d055-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="4d055-220">Atributy `HireDate` mohou být zapsány následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="4d055-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="4d055-221">Navigační vlastnosti</span><span class="sxs-lookup"><span data-stu-id="4d055-221">Navigation properties</span></span>

<span data-ttu-id="4d055-222">Vlastnosti `CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="4d055-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="4d055-223">Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.</span><span class="sxs-lookup"><span data-stu-id="4d055-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="4d055-224">Instruktor může mít maximálně jednu kancelář, takže vlastnost `OfficeAssignment` obsahuje jedinou entitu `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d055-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="4d055-225">`OfficeAssignment` má hodnotu null, pokud není přiřazen žádný systém Office.</span><span class="sxs-lookup"><span data-stu-id="4d055-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="4d055-226">Entita OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="4d055-226">The OfficeAssignment entity</span></span>

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="4d055-228">Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="4d055-229">Klíčový atribut</span><span class="sxs-lookup"><span data-stu-id="4d055-229">The Key attribute</span></span>

<span data-ttu-id="4d055-230">Atribut `[Key]` slouží k identifikaci vlastnosti jako primárního klíče (PK), pokud je název vlastnosti něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="4d055-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="4d055-231">Mezi entitami `Instructor` a `OfficeAssignment` existuje vztah 1:1 nebo jedna.</span><span class="sxs-lookup"><span data-stu-id="4d055-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="4d055-232">Přiřazení kanceláře existuje jenom ve vztahu k instruktorovi, ke kterému je přiřazený.</span><span class="sxs-lookup"><span data-stu-id="4d055-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="4d055-233">`OfficeAssignment` PK je také jeho cizí klíč (FK) k entitě `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="4d055-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="4d055-234">EF Core nemůže automaticky rozpoznat `InstructorID` jako PK v rámci `OfficeAssignment`, protože `InstructorID` nedodržuje zásady vytváření názvů (ID) nebo classnameID.</span><span class="sxs-lookup"><span data-stu-id="4d055-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="4d055-235">Proto atribut `Key` slouží k identifikaci `InstructorID` jako PK:</span><span class="sxs-lookup"><span data-stu-id="4d055-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="4d055-236">Ve výchozím nastavení EF Core považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.</span><span class="sxs-lookup"><span data-stu-id="4d055-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="4d055-237">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="4d055-237">The Instructor navigation property</span></span>

<span data-ttu-id="4d055-238">Vlastnost navigace `Instructor.OfficeAssignment` může mít hodnotu null, protože pro daného instruktora pravděpodobně není `OfficeAssignment` řádek.</span><span class="sxs-lookup"><span data-stu-id="4d055-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="4d055-239">Instruktor nemusí mít přiřazení kanceláře.</span><span class="sxs-lookup"><span data-stu-id="4d055-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="4d055-240">Navigační vlastnost `OfficeAssignment.Instructor` bude mít vždycky entitu Instructor, protože typ `InstructorID` cizího klíče je `int`, typ hodnoty, která není null.</span><span class="sxs-lookup"><span data-stu-id="4d055-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="4d055-241">Přiřazení kanceláře nemůže existovat bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="4d055-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="4d055-242">Pokud má entita `Instructor` související entitu `OfficeAssignment`, Každá entita má odkaz na jinou entitu v její navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="4d055-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="4d055-243">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="4d055-243">The Course Entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="4d055-245">Aktualizujte *modely/Course. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="4d055-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="4d055-246">Entita `Course` má `DepartmentID`vlastnost cizího klíče (FK).</span><span class="sxs-lookup"><span data-stu-id="4d055-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="4d055-247">`DepartmentID` odkazuje na související entitu `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d055-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="4d055-248">`Course` entita má vlastnost navigace `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d055-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="4d055-249">EF Core nevyžaduje vlastnost cizího klíče pro datový model, pokud model má vlastnost navigace pro související entitu.</span><span class="sxs-lookup"><span data-stu-id="4d055-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="4d055-250">EF Core v databázi automaticky vytvoří FKs bez ohledu na to, kde jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="4d055-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="4d055-251">EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automatické vytváření FKs.</span><span class="sxs-lookup"><span data-stu-id="4d055-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="4d055-252">Explicitní a efektivnější je však, že explicitně včetně FK v datovém modelu může zjednodušit a efektivněji dělat aktualizace.</span><span class="sxs-lookup"><span data-stu-id="4d055-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="4d055-253">*Představte* si například model, ve kterém není obsažena vlastnost FK `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="4d055-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="4d055-254">Když se načte entita kurzu, která se upraví:</span><span class="sxs-lookup"><span data-stu-id="4d055-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="4d055-255">Vlastnost `Department` má hodnotu null, pokud není explicitně načtena.</span><span class="sxs-lookup"><span data-stu-id="4d055-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="4d055-256">Chcete-li aktualizovat entitu kurzu, musí být nejprve načtena `Department` entita.</span><span class="sxs-lookup"><span data-stu-id="4d055-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="4d055-257">Pokud je vlastnost FK `DepartmentID` obsažena v datovém modelu, není nutné načíst entitu `Department` před aktualizací.</span><span class="sxs-lookup"><span data-stu-id="4d055-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="4d055-258">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="4d055-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="4d055-259">Atribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` určuje, zda je v rámci aplikace poskytnuta PK místo vygenerovaného databází.</span><span class="sxs-lookup"><span data-stu-id="4d055-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="4d055-260">Ve výchozím nastavení EF Core předpokládá, že se hodnoty PK generují v databázi.</span><span class="sxs-lookup"><span data-stu-id="4d055-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="4d055-261">Vygenerovaná databáze obvykle představuje nejlepší přístup.</span><span class="sxs-lookup"><span data-stu-id="4d055-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="4d055-262">Pro `Course` entit určuje uživatel PK.</span><span class="sxs-lookup"><span data-stu-id="4d055-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="4d055-263">Například číslo kurzu, jako je například série 1000 pro matematické oddělení, série 2000 pro národní oddělení.</span><span class="sxs-lookup"><span data-stu-id="4d055-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="4d055-264">Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="4d055-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="4d055-265">Databáze může například automaticky vygenerovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován.</span><span class="sxs-lookup"><span data-stu-id="4d055-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="4d055-266">Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="4d055-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d055-267">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="4d055-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d055-268">Vlastnosti cizího klíče (FK) a navigační vlastnosti v entitě `Course` odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="4d055-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="4d055-269">Kurz se přiřadí jednomu oddělení, takže existuje `DepartmentID` FK a vlastnost `Department` navigace.</span><span class="sxs-lookup"><span data-stu-id="4d055-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="4d055-270">Kurz může mít zaregistrovaný libovolný počet studentů, takže navigační vlastnost `Enrollments` je kolekce:</span><span class="sxs-lookup"><span data-stu-id="4d055-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="4d055-271">Kurz může být výukou více instruktorů, takže navigační vlastnost `CourseAssignments` je kolekce:</span><span class="sxs-lookup"><span data-stu-id="4d055-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="4d055-272">`CourseAssignment` je vysvětleno [později](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="4d055-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="4d055-273">Entita oddělení</span><span class="sxs-lookup"><span data-stu-id="4d055-273">The Department entity</span></span>

![Entita oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="4d055-275">Vytvořte *modely/oddělení. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="4d055-276">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="4d055-276">The Column attribute</span></span>

<span data-ttu-id="4d055-277">Dřív se použil atribut `Column` pro změnu mapování názvu sloupce.</span><span class="sxs-lookup"><span data-stu-id="4d055-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="4d055-278">V kódu pro entitu `Department` se atribut `Column` používá ke změně mapování datových typů SQL.</span><span class="sxs-lookup"><span data-stu-id="4d055-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="4d055-279">`Budget` sloupec je definován pomocí SQL Server peněžního typu v databázi:</span><span class="sxs-lookup"><span data-stu-id="4d055-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="4d055-280">Mapování sloupce není obecně vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="4d055-280">Column mapping is generally not required.</span></span> <span data-ttu-id="4d055-281">EF Core zvolí příslušný datový typ SQL Server na základě typu CLR pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4d055-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="4d055-282">Typ CLR `decimal` namapuje na typ `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4d055-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="4d055-283">`Budget` je pro měnu a datový typ Money je pro měnu vhodný.</span><span class="sxs-lookup"><span data-stu-id="4d055-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d055-284">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="4d055-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d055-285">Vlastnosti FK a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="4d055-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="4d055-286">Oddělení může nebo nemusí mít správce.</span><span class="sxs-lookup"><span data-stu-id="4d055-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="4d055-287">Správce je vždy instruktor.</span><span class="sxs-lookup"><span data-stu-id="4d055-287">An administrator is always an instructor.</span></span> <span data-ttu-id="4d055-288">Proto je vlastnost `InstructorID` zahrnutá jako FK do `Instructor` entity.</span><span class="sxs-lookup"><span data-stu-id="4d055-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="4d055-289">Navigační vlastnost má název `Administrator`, ale obsahuje `Instructor` entitu:</span><span class="sxs-lookup"><span data-stu-id="4d055-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="4d055-290">Otazník (?) v předchozím kódu určuje vlastnost s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="4d055-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="4d055-291">Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:</span><span class="sxs-lookup"><span data-stu-id="4d055-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="4d055-292">Podle konvence EF Core povoluje kaskádové odstranění pro FKs, která nejsou null a pro relace m:n.</span><span class="sxs-lookup"><span data-stu-id="4d055-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="4d055-293">Toto výchozí chování může způsobit cyklické kaskády odstraňování pravidel.</span><span class="sxs-lookup"><span data-stu-id="4d055-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="4d055-294">Cyklická kaskádová odstranění pravidel způsobují při přidání migrace výjimku.</span><span class="sxs-lookup"><span data-stu-id="4d055-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="4d055-295">Pokud je například vlastnost `Department.InstructorID` definovaná jako hodnota, která není null, EF Core by nakonfigurovala pravidlo kaskádového odstranění.</span><span class="sxs-lookup"><span data-stu-id="4d055-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="4d055-296">V takovém případě by se oddělení odstranilo, když se odstraní instruktor jako jeho správce.</span><span class="sxs-lookup"><span data-stu-id="4d055-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="4d055-297">V tomto scénáři by pravidlo omezení mělo smysl.</span><span class="sxs-lookup"><span data-stu-id="4d055-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="4d055-298">Následující [rozhraní Fluent API](#fluent-api-alternative-to-attributes) by nastavilo pravidlo omezení a zakáže kaskádové odstranění.</span><span class="sxs-lookup"><span data-stu-id="4d055-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="4d055-299">Registrace entity</span><span class="sxs-lookup"><span data-stu-id="4d055-299">The Enrollment entity</span></span>

<span data-ttu-id="4d055-300">Záznam zápisu je pro jeden kurz, který přijímá jeden student.</span><span class="sxs-lookup"><span data-stu-id="4d055-300">An enrollment record is for one course taken by one student.</span></span>

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="4d055-302">Aktualizujte *modely/zápis. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d055-303">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="4d055-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d055-304">Vlastnosti CK a vlastnosti navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="4d055-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="4d055-305">Záznam zápisu je pro jeden kurz, takže existuje `CourseID` vlastnost FK a `Course` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="4d055-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="4d055-306">Záznam zápisu je určen pro jednoho studenta, takže existuje `StudentID` vlastnost FK a `Student` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="4d055-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="4d055-307">Relace m:n</span><span class="sxs-lookup"><span data-stu-id="4d055-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="4d055-308">Mezi entitami `Student` a `Course` existuje vztah m:n.</span><span class="sxs-lookup"><span data-stu-id="4d055-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="4d055-309">Entita `Enrollment` funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi.</span><span class="sxs-lookup"><span data-stu-id="4d055-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="4d055-310">"S datovou částí" znamená, že `Enrollment` tabulka obsahuje další data kromě FKs pro Spojené tabulky (v tomto případě je to PK a `Grade`).</span><span class="sxs-lookup"><span data-stu-id="4d055-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="4d055-311">Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit.</span><span class="sxs-lookup"><span data-stu-id="4d055-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="4d055-312">(Tento diagram byl vygenerován pomocí [nástrojů EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6. x.</span><span class="sxs-lookup"><span data-stu-id="4d055-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="4d055-313">Vytvoření diagramu není součástí kurzu.)</span><span class="sxs-lookup"><span data-stu-id="4d055-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

<span data-ttu-id="4d055-315">Každá čára relace má 1 na jednom konci a hvězdičku (\*) na druhé straně, která indikuje relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="4d055-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="4d055-316">Pokud tabulka `Enrollment` neobsahovala informace o třídě, musí obsahovat pouze dvě FKs (`CourseID` a `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="4d055-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="4d055-317">Tabulka JOIN typu m:n bez datové části se někdy označuje jako čistá spojovací tabulka (PJT).</span><span class="sxs-lookup"><span data-stu-id="4d055-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="4d055-318">Entity `Instructor` a `Course` mají relaci n:n pomocí tabulky Pure JOIN.</span><span class="sxs-lookup"><span data-stu-id="4d055-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="4d055-319">Poznámka: EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne.</span><span class="sxs-lookup"><span data-stu-id="4d055-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="4d055-320">Další informace najdete v tématu [relace m:n v EF Core 2,0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="4d055-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="4d055-321">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="4d055-321">The CourseAssignment entity</span></span>

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="4d055-323">Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="4d055-324">Relace m:n pro každého-více kurzů vyžaduje tabulku JOIN a entita pro tuto tabulku JOIN je CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="4d055-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="4d055-326">Je běžné pojmenovat entitu JOIN `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="4d055-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="4d055-327">Například tabulka pro spojování instruktorů do kurzů, pomocí které tento model bude `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="4d055-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="4d055-328">Doporučujeme však použít název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="4d055-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="4d055-329">Modely dat začínají jednoduchým a roste.</span><span class="sxs-lookup"><span data-stu-id="4d055-329">Data models start out simple and grow.</span></span> <span data-ttu-id="4d055-330">Spojování tabulek bez datové části (PJTs) se často vyvíjí, aby zahrnovalo datovou část.</span><span class="sxs-lookup"><span data-stu-id="4d055-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="4d055-331">Když začnete s popisným názvem entity, nemusíte při změně tabulky JOIN měnit název.</span><span class="sxs-lookup"><span data-stu-id="4d055-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="4d055-332">V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="4d055-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="4d055-333">Například knihy a zákazníci mohou být propojeny s entitou JOIN nazvanou hodnocení.</span><span class="sxs-lookup"><span data-stu-id="4d055-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="4d055-334">Pro relaci n:n od instruktora na více kurzů `CourseAssignment` přednost před `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="4d055-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="4d055-335">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="4d055-335">Composite key</span></span>

<span data-ttu-id="4d055-336">Dva FKs v `CourseAssignment` (`InstructorID` a `CourseID`) společně jednoznačně identifikují každý řádek tabulky `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d055-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="4d055-337">`CourseAssignment` nevyžaduje vyhrazený PK.</span><span class="sxs-lookup"><span data-stu-id="4d055-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="4d055-338">Vlastnosti `InstructorID` a `CourseID` fungují jako složený PK.</span><span class="sxs-lookup"><span data-stu-id="4d055-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="4d055-339">Jediným způsobem, jak zadat složené PKs EF Core je s *rozhraním API Fluent*.</span><span class="sxs-lookup"><span data-stu-id="4d055-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="4d055-340">V další části se dozvíte, jak nakonfigurovat složený PK.</span><span class="sxs-lookup"><span data-stu-id="4d055-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="4d055-341">Složený klíč zajišťuje:</span><span class="sxs-lookup"><span data-stu-id="4d055-341">The composite key ensures that:</span></span>

* <span data-ttu-id="4d055-342">Pro jeden kurz je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="4d055-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="4d055-343">Pro jednoho instruktora je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="4d055-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="4d055-344">Pro stejný instruktor a kurz nejsou povoleny vícenásobné řádky.</span><span class="sxs-lookup"><span data-stu-id="4d055-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="4d055-345">Entita `Enrollment` JOIN definuje vlastní PK, takže je možné duplikovat toto řazení.</span><span class="sxs-lookup"><span data-stu-id="4d055-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="4d055-346">Chcete-li zabránit těmto duplicitám:</span><span class="sxs-lookup"><span data-stu-id="4d055-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="4d055-347">Přidejte do polí FK jedinečný index nebo</span><span class="sxs-lookup"><span data-stu-id="4d055-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="4d055-348">Nakonfigurujte `Enrollment` s primárním kombinovaným klíčem podobným `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d055-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="4d055-349">Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="4d055-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="4d055-350">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="4d055-350">Update the database context</span></span>

<span data-ttu-id="4d055-351">Aktualizujte *data/SchoolContext. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="4d055-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="4d055-352">Předchozí kód přidá nové entity a nakonfiguruje neseparovaný PK entity `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d055-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="4d055-353">Alternativa k atributům rozhraní Fluent API</span><span class="sxs-lookup"><span data-stu-id="4d055-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="4d055-354">Metoda `OnModelCreating` v předchozím kódu používá ke konfiguraci chování EF Core *rozhraní Fluent API* .</span><span class="sxs-lookup"><span data-stu-id="4d055-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="4d055-355">Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu.</span><span class="sxs-lookup"><span data-stu-id="4d055-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="4d055-356">[Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem rozhraní Fluent API:</span><span class="sxs-lookup"><span data-stu-id="4d055-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="4d055-357">V tomto kurzu se rozhraní API Fluent používá jenom pro mapování databáze, které nejde s atributy dělat.</span><span class="sxs-lookup"><span data-stu-id="4d055-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="4d055-358">Rozhraní API Fluent ale může určovat většinu pravidel formátování, ověřování a mapování, která se dají provádět s atributy.</span><span class="sxs-lookup"><span data-stu-id="4d055-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="4d055-359">Některé atributy, jako je `MinimumLength`, se nedají použít s rozhraním API Fluent.</span><span class="sxs-lookup"><span data-stu-id="4d055-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="4d055-360">`MinimumLength` nemění schéma, používá pouze ověřovací pravidlo minimální délky.</span><span class="sxs-lookup"><span data-stu-id="4d055-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="4d055-361">Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit".</span><span class="sxs-lookup"><span data-stu-id="4d055-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="4d055-362">Atributy a rozhraní API Fluent lze kombinovat.</span><span class="sxs-lookup"><span data-stu-id="4d055-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="4d055-363">Existují některé konfigurace, které lze provést pouze s rozhraním API Fluent (určením složeného PK).</span><span class="sxs-lookup"><span data-stu-id="4d055-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="4d055-364">Existují některé konfigurace, které lze provádět pouze s atributy (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="4d055-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="4d055-365">Doporučený postup pro použití rozhraní Fluent API nebo atributů:</span><span class="sxs-lookup"><span data-stu-id="4d055-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="4d055-366">Vyberte jednu z těchto dvou přístupů.</span><span class="sxs-lookup"><span data-stu-id="4d055-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="4d055-367">Používejte vybraný postup konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="4d055-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="4d055-368">Některé atributy používané v tomto kurzu se používají pro:</span><span class="sxs-lookup"><span data-stu-id="4d055-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="4d055-369">Pouze ověření (například `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="4d055-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="4d055-370">Pouze konfigurace EF Core (například `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="4d055-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="4d055-371">Konfigurace ověřování a EF Core (například `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="4d055-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="4d055-372">Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="4d055-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="4d055-373">Diagram entit</span><span class="sxs-lookup"><span data-stu-id="4d055-373">Entity diagram</span></span>

<span data-ttu-id="4d055-374">Následující ilustrace znázorňuje diagram, který nástroje EF Power Tools vytvoří pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="4d055-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="4d055-376">Předchozí diagram znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="4d055-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="4d055-377">Několik čar relací 1:1 (1 až \*).</span><span class="sxs-lookup"><span data-stu-id="4d055-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="4d055-378">Čára relace 1:1 (1 – 0.. 1) mezi entitami `Instructor` a `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d055-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="4d055-379">Čára vztahu nula až n-many (0.. 1 až ×) mezi entitami `Instructor` a `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d055-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="4d055-380">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="4d055-380">Seed the database</span></span>

<span data-ttu-id="4d055-381">Aktualizujte kód v *data/DbInitializer. cs*:</span><span class="sxs-lookup"><span data-stu-id="4d055-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="4d055-382">Předchozí kód poskytuje počáteční data pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="4d055-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="4d055-383">Většina tohoto kódu vytváří nové objekty entit a načítá vzorová data.</span><span class="sxs-lookup"><span data-stu-id="4d055-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="4d055-384">Ukázková data se používají k testování.</span><span class="sxs-lookup"><span data-stu-id="4d055-384">The sample data is used for testing.</span></span> <span data-ttu-id="4d055-385">Příklady toho, kolik tabulek Join lze dosazením, najdete v tématu `Enrollments` a `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="4d055-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="4d055-386">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="4d055-386">Add a migration</span></span>

<span data-ttu-id="4d055-387">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="4d055-387">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d055-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d055-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4d055-389">V PMC spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="4d055-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="4d055-390">Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="4d055-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="4d055-391">Pokud se spustí příkaz `database update`, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="4d055-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="4d055-392">V další části se dozvíte, co dělat s touto chybou.</span><span class="sxs-lookup"><span data-stu-id="4d055-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d055-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d055-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d055-394">Pokud přidáte migraci a spustíte příkaz `database update`, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="4d055-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="4d055-395">V další části se zobrazí informace o tom, jak se vyhnout této chybě.</span><span class="sxs-lookup"><span data-stu-id="4d055-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="4d055-396">Použití migrace nebo vyřazení a opětovné vytvoření</span><span class="sxs-lookup"><span data-stu-id="4d055-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="4d055-397">Teď, když máte existující databázi, musíte si představit, jak se na ni aplikují změny.</span><span class="sxs-lookup"><span data-stu-id="4d055-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="4d055-398">V tomto kurzu se zobrazí dvě alternativy:</span><span class="sxs-lookup"><span data-stu-id="4d055-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="4d055-399">[Vyřaďte a znovu vytvořte databázi](#drop).</span><span class="sxs-lookup"><span data-stu-id="4d055-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="4d055-400">Tuto část vyberte, pokud používáte SQLite.</span><span class="sxs-lookup"><span data-stu-id="4d055-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="4d055-401">[Použijte migraci na stávající databázi](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="4d055-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="4d055-402">Pokyny v této části fungují pouze pro SQL Server, **nikoli pro SQLite**.</span><span class="sxs-lookup"><span data-stu-id="4d055-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="4d055-403">Jedna volba funguje pro SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4d055-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="4d055-404">I když je metoda Apply složitější a časově náročná, jedná se o preferovaný přístup pro produkční prostředí z reálného světa.</span><span class="sxs-lookup"><span data-stu-id="4d055-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4d055-405">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="4d055-405">Drop and re-create the database</span></span>

<span data-ttu-id="4d055-406">[Tuto část přeskočte](#apply-the-migration) , pokud používáte SQL Server a chcete provést postup použití migrace v následující části.</span><span class="sxs-lookup"><span data-stu-id="4d055-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="4d055-407">Chcete-li vynutit EF Core vytvoření nové databáze, vyřaďte a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="4d055-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d055-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d055-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4d055-409">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4d055-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="4d055-410">Odstraňte složku *migrace* a pak spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4d055-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d055-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d055-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4d055-412">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="4d055-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="4d055-413">Složka projektu obsahuje soubor *ContosoUniversity. csproj* .</span><span class="sxs-lookup"><span data-stu-id="4d055-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="4d055-414">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4d055-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="4d055-415">Odstraňte složku *migrace* a pak spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4d055-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="4d055-416">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4d055-416">Run the app.</span></span> <span data-ttu-id="4d055-417">Spuštění aplikace spustí metodu `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="4d055-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="4d055-418">`DbInitializer.Initialize` naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="4d055-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d055-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d055-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4d055-420">Otevřete databázi v SSOX:</span><span class="sxs-lookup"><span data-stu-id="4d055-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="4d055-421">Pokud jste dříve otevřeli SSOX, klikněte na tlačítko **aktualizovat** .</span><span class="sxs-lookup"><span data-stu-id="4d055-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="4d055-422">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="4d055-422">Expand the **Tables** node.</span></span> <span data-ttu-id="4d055-423">Zobrazí se vytvořené tabulky.</span><span class="sxs-lookup"><span data-stu-id="4d055-423">The created tables are displayed.</span></span>

  ![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="4d055-425">Projděte si tabulku **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="4d055-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="4d055-426">Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data**.</span><span class="sxs-lookup"><span data-stu-id="4d055-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="4d055-427">Ověřte, že tabulka **CourseAssignment** obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="4d055-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d055-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d055-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d055-430">Použijte nástroj SQLite k prohlédnutí databáze:</span><span class="sxs-lookup"><span data-stu-id="4d055-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="4d055-431">Nové tabulky a sloupce.</span><span class="sxs-lookup"><span data-stu-id="4d055-431">New tables and columns.</span></span>
* <span data-ttu-id="4d055-432">Osazená data v tabulkách, například tabulka **CourseAssignment** .</span><span class="sxs-lookup"><span data-stu-id="4d055-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="4d055-433">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="4d055-433">Apply the migration</span></span>

<span data-ttu-id="4d055-434">Tato část je volitelná.</span><span class="sxs-lookup"><span data-stu-id="4d055-434">This section is optional.</span></span> <span data-ttu-id="4d055-435">Tyto kroky fungují jenom pro SQL Server LocalDB a jenom v případě, že jste přeskočili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="4d055-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="4d055-436">Pokud jsou migrace spouštěny s existujícími daty, může dojít k omezením FK, která nesplňují stávající data.</span><span class="sxs-lookup"><span data-stu-id="4d055-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="4d055-437">S provozními daty je potřeba provést kroky pro migraci stávajících dat.</span><span class="sxs-lookup"><span data-stu-id="4d055-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="4d055-438">V této části najdete příklad opravy porušení omezení CK.</span><span class="sxs-lookup"><span data-stu-id="4d055-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="4d055-439">Neprovádějte změny kódu bez zálohy.</span><span class="sxs-lookup"><span data-stu-id="4d055-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="4d055-440">Neprovádějte změny kódu, pokud jste dokončili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="4d055-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="4d055-441">Soubor *{timestamp} _ComplexDataModel. cs* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="4d055-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="4d055-442">Předchozí kód přidá do tabulky `Course` `DepartmentID` FK, který nemůže mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="4d055-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="4d055-443">Databáze z předchozího kurzu obsahuje řádky v `Course`, takže se tabulka nedá aktualizovat pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="4d055-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="4d055-444">Postup při práci `ComplexDataModel` migrace s existujícími daty:</span><span class="sxs-lookup"><span data-stu-id="4d055-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="4d055-445">Změňte kód tak, aby měl nový sloupec (`DepartmentID`) výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4d055-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="4d055-446">Vytvořte falešné oddělení s názvem "Temp", které bude sloužit jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="4d055-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="4d055-447">Oprava omezení cizího klíče</span><span class="sxs-lookup"><span data-stu-id="4d055-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="4d055-448">V `ComplexDataModel` třídy migrace aktualizujte `Up` metodu:</span><span class="sxs-lookup"><span data-stu-id="4d055-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="4d055-449">Otevřete soubor *{timestamp} _ComplexDataModel. cs* .</span><span class="sxs-lookup"><span data-stu-id="4d055-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="4d055-450">Odkomentujte řádek kódu, který přidá sloupec `DepartmentID` do tabulky `Course`.</span><span class="sxs-lookup"><span data-stu-id="4d055-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="4d055-451">Přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="4d055-451">Add the following highlighted code.</span></span> <span data-ttu-id="4d055-452">Nový kód přejde za `.CreateTable( name: "Department"` blok:</span><span class="sxs-lookup"><span data-stu-id="4d055-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="4d055-453">V předchozích změnách budou existující `Course` řádky při spuštění metody `ComplexDataModel.Up` v souvislosti s oddělením "dočasné".</span><span class="sxs-lookup"><span data-stu-id="4d055-453">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="4d055-454">Pro účely tohoto kurzu je zjednodušený způsob zpracování situace, kterou tady vidíte.</span><span class="sxs-lookup"><span data-stu-id="4d055-454">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="4d055-455">Produkční aplikace by:</span><span class="sxs-lookup"><span data-stu-id="4d055-455">A production app would:</span></span>

* <span data-ttu-id="4d055-456">Zahrnout kód nebo skripty pro přidání `Department` řádků a souvisejících `Course` řádků do nových `Department` řádků.</span><span class="sxs-lookup"><span data-stu-id="4d055-456">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="4d055-457">Nepoužívejte oddělení "dočasné" nebo výchozí hodnotu pro `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="4d055-457">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d055-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d055-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4d055-459">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4d055-459">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="4d055-460">Vzhledem k tomu, že metoda `DbInitializer.Initialize` je navržena tak, aby fungovala pouze s prázdnou databází, použijte SSOX k odstranění všech řádků v tabulkách student a Course.</span><span class="sxs-lookup"><span data-stu-id="4d055-460">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="4d055-461">(Kaskádová odstranění se postará o tabulku zápisu.)</span><span class="sxs-lookup"><span data-stu-id="4d055-461">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d055-462">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d055-462">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4d055-463">Pokud používáte SQL Server LocalDB s Visual Studio Code, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4d055-463">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="4d055-464">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4d055-464">Run the app.</span></span> <span data-ttu-id="4d055-465">Spuštění aplikace spustí metodu `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="4d055-465">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="4d055-466">`DbInitializer.Initialize` naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="4d055-466">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4d055-467">Další kroky</span><span class="sxs-lookup"><span data-stu-id="4d055-467">Next steps</span></span>

<span data-ttu-id="4d055-468">Následující dva kurzy ukazují, jak číst a aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="4d055-468">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4d055-469">[Předchozí kurz](xref:data/ef-rp/migrations)
> [Další kurz](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="4d055-469">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4d055-470">Předchozí kurzy pracovaly se základním datovým modelem, který se skládá ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="4d055-470">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="4d055-471">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="4d055-471">In this tutorial:</span></span>

* <span data-ttu-id="4d055-472">Přidávají se další entity a vztahy.</span><span class="sxs-lookup"><span data-stu-id="4d055-472">More entities and relationships are added.</span></span>
* <span data-ttu-id="4d055-473">Datový model je přizpůsoben zadáním pravidel formátování, ověřování a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="4d055-473">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="4d055-474">Třídy entit pro dokončený datový model jsou znázorněny na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="4d055-474">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="4d055-476">Pokud narazíte na problémy, které nemůžete vyřešit, stáhněte [dokončenou aplikaci](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="4d055-476">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="4d055-477">Přizpůsobení datového modelu pomocí atributů</span><span class="sxs-lookup"><span data-stu-id="4d055-477">Customize the data model with attributes</span></span>

<span data-ttu-id="4d055-478">V této části je datový model přizpůsoben pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="4d055-478">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="4d055-479">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="4d055-479">The DataType attribute</span></span>

<span data-ttu-id="4d055-480">Na stránkách studenta se aktuálně zobrazuje čas pro datum registrace.</span><span class="sxs-lookup"><span data-stu-id="4d055-480">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="4d055-481">Pole data obvykle zobrazují pouze datum a čas.</span><span class="sxs-lookup"><span data-stu-id="4d055-481">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="4d055-482">Aktualizujte *modely/student. cs* následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-482">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="4d055-483">Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) Určuje datový typ, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="4d055-483">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="4d055-484">V tomto případě by se měla zobrazit pouze datum, nikoli datum a čas.</span><span class="sxs-lookup"><span data-stu-id="4d055-484">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="4d055-485">[Výčet DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress atd. Atribut `DataType` může také aplikaci povolit automatické poskytování funkcí specifických pro typ.</span><span class="sxs-lookup"><span data-stu-id="4d055-485">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="4d055-486">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4d055-486">For example:</span></span>

* <span data-ttu-id="4d055-487">Odkaz `mailto:` se automaticky vytvoří pro `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="4d055-487">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="4d055-488">Selektor data je k dispozici pro `DataType.Date` ve většině prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="4d055-488">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="4d055-489">Atribut `DataType` emituje atributy HTML 5 `data-` (vyslovované datové přerušované), které používají prohlížeče formátu HTML 5.</span><span class="sxs-lookup"><span data-stu-id="4d055-489">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="4d055-490">Atributy `DataType` neposkytují ověřování.</span><span class="sxs-lookup"><span data-stu-id="4d055-490">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="4d055-491">`DataType.Date` neurčuje formát data, které se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="4d055-491">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="4d055-492">Ve výchozím nastavení se pole Datum zobrazuje v závislosti na výchozích formátech na základě objektu [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serveru.</span><span class="sxs-lookup"><span data-stu-id="4d055-492">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="4d055-493">Atribut `DisplayFormat` slouží k explicitnímu zadání formátu data:</span><span class="sxs-lookup"><span data-stu-id="4d055-493">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="4d055-494">Nastavení `ApplyFormatInEditMode` určuje, zda má být formátování použito také pro uživatelské rozhraní úprav.</span><span class="sxs-lookup"><span data-stu-id="4d055-494">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="4d055-495">Některá pole by neměla používat `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="4d055-495">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="4d055-496">Například symbol měny by neměl být v textovém poli pro úpravy obvykle zobrazen.</span><span class="sxs-lookup"><span data-stu-id="4d055-496">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="4d055-497">Atribut `DisplayFormat` může používat sám sebe.</span><span class="sxs-lookup"><span data-stu-id="4d055-497">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="4d055-498">Obecně je vhodné použít atribut `DataType` s atributem `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="4d055-498">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="4d055-499">Atribut `DataType` předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce.</span><span class="sxs-lookup"><span data-stu-id="4d055-499">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="4d055-500">Atribut `DataType` poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="4d055-500">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="4d055-501">Prohlížeč může povolit funkce HTML5.</span><span class="sxs-lookup"><span data-stu-id="4d055-501">The browser can enable HTML5 features.</span></span> <span data-ttu-id="4d055-502">Například můžete zobrazit ovládací prvek kalendáře, symbol měny odpovídající národním prostředí, e-mailové odkazy, ověřování vstupu na straně klienta atd.</span><span class="sxs-lookup"><span data-stu-id="4d055-502">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="4d055-503">Ve výchozím nastavení prohlížeč vykresluje data pomocí správného formátu založeného na národním prostředí.</span><span class="sxs-lookup"><span data-stu-id="4d055-503">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="4d055-504">Další informace najdete v dokumentaci k [pomocníka značek\<input >](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4d055-504">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="4d055-505">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4d055-505">Run the app.</span></span> <span data-ttu-id="4d055-506">Přejděte na stránku indexu studentů.</span><span class="sxs-lookup"><span data-stu-id="4d055-506">Navigate to the Students Index page.</span></span> <span data-ttu-id="4d055-507">Časy se už nezobrazují.</span><span class="sxs-lookup"><span data-stu-id="4d055-507">Times are no longer displayed.</span></span> <span data-ttu-id="4d055-508">Každé zobrazení, které používá model `Student`, zobrazí datum bez času.</span><span class="sxs-lookup"><span data-stu-id="4d055-508">Every view that uses the `Student` model displays the date without time.</span></span>

![Stránka indexu studentů zobrazující data bez časů](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="4d055-510">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="4d055-510">The StringLength attribute</span></span>

<span data-ttu-id="4d055-511">Pravidla ověřování dat a chybové zprávy ověřování lze zadat pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="4d055-511">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="4d055-512">Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli.</span><span class="sxs-lookup"><span data-stu-id="4d055-512">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="4d055-513">Atribut `StringLength` také poskytuje ověřování na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="4d055-513">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="4d055-514">Minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="4d055-514">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="4d055-515">Aktualizujte `Student` model pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="4d055-515">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="4d055-516">Předchozí kód omezuje názvy na více než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="4d055-516">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="4d055-517">Atribut `StringLength` nebrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="4d055-517">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="4d055-518">Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) se používá k aplikování omezení na vstup.</span><span class="sxs-lookup"><span data-stu-id="4d055-518">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="4d055-519">Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:</span><span class="sxs-lookup"><span data-stu-id="4d055-519">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="4d055-520">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="4d055-520">Run the app:</span></span>

* <span data-ttu-id="4d055-521">Přejděte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="4d055-521">Navigate to the Students page.</span></span>
* <span data-ttu-id="4d055-522">Vyberte **vytvořit novou**a zadejte název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="4d055-522">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="4d055-523">Vyberte **vytvořit**, ověřování na straně klienta zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="4d055-523">Select **Create**, client-side validation shows an error message.</span></span>

![Stránka indexu studentů zobrazující chyby délky řetězce](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="4d055-525">V **Průzkumník objektů systému SQL Server** (SSOX) otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .</span><span class="sxs-lookup"><span data-stu-id="4d055-525">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů v SSOX před migracemi](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="4d055-527">Na předchozím obrázku je znázorněno schéma `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="4d055-527">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="4d055-528">Pole název mají typ `nvarchar(MAX)`, protože migrace nejsou v databázi spuštěny.</span><span class="sxs-lookup"><span data-stu-id="4d055-528">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="4d055-529">Po spuštění migrace později v tomto kurzu se zobrazí pole název `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="4d055-529">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="4d055-530">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="4d055-530">The Column attribute</span></span>

<span data-ttu-id="4d055-531">Atributy mohou řídit způsob, jakým jsou třídy a vlastnosti mapovány na databázi.</span><span class="sxs-lookup"><span data-stu-id="4d055-531">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="4d055-532">V této části se pro mapování názvu vlastnosti `FirstMidName` na FirstName v databázi používá atribut `Column`.</span><span class="sxs-lookup"><span data-stu-id="4d055-532">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="4d055-533">Při vytvoření databáze se názvy vlastností v modelu používají pro názvy sloupců (s výjimkou případů, kdy se používá atribut `Column`).</span><span class="sxs-lookup"><span data-stu-id="4d055-533">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="4d055-534">`Student` model používá `FirstMidName` pro pole jméno a příjmení, protože pole může obsahovat také prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="4d055-534">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="4d055-535">Aktualizujte soubor *student.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="4d055-535">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="4d055-536">V předchozí změně `Student.FirstMidName` v aplikaci namapovány do sloupce `FirstName` tabulky `Student`.</span><span class="sxs-lookup"><span data-stu-id="4d055-536">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="4d055-537">Přidání atributu `Column` změní model zálohování `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="4d055-537">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="4d055-538">Model zálohovaný `SchoolContext` se už neshoduje s databází.</span><span class="sxs-lookup"><span data-stu-id="4d055-538">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="4d055-539">Pokud je aplikace spuštěná před použitím migrace, vygeneruje se tato výjimka:</span><span class="sxs-lookup"><span data-stu-id="4d055-539">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="4d055-540">Aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="4d055-540">To update the DB:</span></span>

* <span data-ttu-id="4d055-541">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="4d055-541">Build the project.</span></span>
* <span data-ttu-id="4d055-542">Otevřete okno příkazového řádku ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="4d055-542">Open a command window in the project folder.</span></span> <span data-ttu-id="4d055-543">Zadáním následujících příkazů vytvořte novou migraci a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="4d055-543">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d055-544">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d055-544">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d055-545">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d055-545">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="4d055-546">Příkaz `migrations add ColumnFirstName` generuje následující varovnou zprávu:</span><span class="sxs-lookup"><span data-stu-id="4d055-546">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="4d055-547">Upozornění je vygenerováno, protože pole s názvem jsou nyní omezena na 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="4d055-547">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="4d055-548">Pokud má název v databázi více než 50 znaků, ztratí se 51 na poslední znak.</span><span class="sxs-lookup"><span data-stu-id="4d055-548">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="4d055-549">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4d055-549">Test the app.</span></span>

<span data-ttu-id="4d055-550">Otevřete tabulku student v SSOX:</span><span class="sxs-lookup"><span data-stu-id="4d055-550">Open the Student table in SSOX:</span></span>

![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="4d055-552">Před použitím migrace byly sloupce názvu typu [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="4d055-552">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="4d055-553">Sloupce s názvem jsou nyní `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="4d055-553">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="4d055-554">Název sloupce se změnil z `FirstMidName` na `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="4d055-554">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="4d055-555">V následující části sestavení aplikace v některých fázích generuje chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="4d055-555">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="4d055-556">Pokyny určují, kdy se má aplikace sestavit.</span><span class="sxs-lookup"><span data-stu-id="4d055-556">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="4d055-557">Aktualizace entity studenta</span><span class="sxs-lookup"><span data-stu-id="4d055-557">Student entity update</span></span>

![Entita studenta](complex-data-model/_static/student-entity.png)

<span data-ttu-id="4d055-559">Aktualizujte *modely/student. cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-559">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="4d055-560">Požadovaný atribut</span><span class="sxs-lookup"><span data-stu-id="4d055-560">The Required attribute</span></span>

<span data-ttu-id="4d055-561">Atribut `Required` vytváří pole vlastnosti názvu Required.</span><span class="sxs-lookup"><span data-stu-id="4d055-561">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="4d055-562">Atribut `Required` není potřebný pro typy, které nejsou null, jako jsou například typy hodnot (`DateTime`, `int`, `double`atd.).</span><span class="sxs-lookup"><span data-stu-id="4d055-562">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="4d055-563">Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="4d055-563">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="4d055-564">Atribut `Required` mohl být nahrazen parametrem minimální délky v atributu `StringLength`:</span><span class="sxs-lookup"><span data-stu-id="4d055-564">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="4d055-565">Atribut zobrazení</span><span class="sxs-lookup"><span data-stu-id="4d055-565">The Display attribute</span></span>

<span data-ttu-id="4d055-566">Atribut `Display` určuje, že titulek pro textová pole by měl být "jméno", "příjmení", "celé jméno" a "datum zápisu".</span><span class="sxs-lookup"><span data-stu-id="4d055-566">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="4d055-567">Výchozí titulky neobsahovaly místo dělení slov, například "LastName".</span><span class="sxs-lookup"><span data-stu-id="4d055-567">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="4d055-568">Vypočítaná vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="4d055-568">The FullName calculated property</span></span>

<span data-ttu-id="4d055-569">`FullName` je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="4d055-569">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="4d055-570">`FullName` nelze nastavit, má pouze přistupující objekt get.</span><span class="sxs-lookup"><span data-stu-id="4d055-570">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="4d055-571">V databázi není vytvořen žádný `FullName` sloupec.</span><span class="sxs-lookup"><span data-stu-id="4d055-571">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="4d055-572">Vytvořit entitu instruktora</span><span class="sxs-lookup"><span data-stu-id="4d055-572">Create the Instructor Entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="4d055-574">Vytvořte *modely/Instructor. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-574">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="4d055-575">Více atributů může být na jednom řádku.</span><span class="sxs-lookup"><span data-stu-id="4d055-575">Multiple attributes can be on one line.</span></span> <span data-ttu-id="4d055-576">Atributy `HireDate` mohou být zapsány následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="4d055-576">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="4d055-577">Navigační vlastnosti CourseAssignments a OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="4d055-577">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="4d055-578">Vlastnosti `CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="4d055-578">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="4d055-579">Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.</span><span class="sxs-lookup"><span data-stu-id="4d055-579">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="4d055-580">Pokud navigační vlastnost obsahuje více entit:</span><span class="sxs-lookup"><span data-stu-id="4d055-580">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="4d055-581">Musí se jednat o typ seznamu, kde je možné přidávat, odstraňovat a aktualizovat položky.</span><span class="sxs-lookup"><span data-stu-id="4d055-581">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="4d055-582">Mezi typy vlastností navigace patří:</span><span class="sxs-lookup"><span data-stu-id="4d055-582">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="4d055-583">Je-li zadán `ICollection<T>`, EF Core ve výchozím nastavení vytvoří kolekci `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="4d055-583">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="4d055-584">`CourseAssignment` entita je vysvětlena v části u vztahů m:n.</span><span class="sxs-lookup"><span data-stu-id="4d055-584">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="4d055-585">Firemní pravidla společnosti Contoso vysokých škol, že vyučující může mít maximálně jednu kancelář.</span><span class="sxs-lookup"><span data-stu-id="4d055-585">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="4d055-586">Vlastnost `OfficeAssignment` obsahuje jednu entitu `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d055-586">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="4d055-587">`OfficeAssignment` má hodnotu null, pokud není přiřazen žádný systém Office.</span><span class="sxs-lookup"><span data-stu-id="4d055-587">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="4d055-588">Vytvoření entity OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="4d055-588">Create the OfficeAssignment entity</span></span>

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="4d055-590">Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-590">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="4d055-591">Klíčový atribut</span><span class="sxs-lookup"><span data-stu-id="4d055-591">The Key attribute</span></span>

<span data-ttu-id="4d055-592">Atribut `[Key]` slouží k identifikaci vlastnosti jako primárního klíče (PK), pokud je název vlastnosti něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="4d055-592">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="4d055-593">Mezi entitami `Instructor` a `OfficeAssignment` existuje vztah 1:1 nebo jedna.</span><span class="sxs-lookup"><span data-stu-id="4d055-593">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="4d055-594">Přiřazení kanceláře existuje jenom ve vztahu k instruktorovi, ke kterému je přiřazený.</span><span class="sxs-lookup"><span data-stu-id="4d055-594">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="4d055-595">`OfficeAssignment` PK je také jeho cizí klíč (FK) k entitě `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="4d055-595">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="4d055-596">EF Core nemůže automaticky rozpoznat `InstructorID` jako PK `OfficeAssignment` z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="4d055-596">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="4d055-597">`InstructorID` nedodržuje konvence pojmenování ID nebo classnameID.</span><span class="sxs-lookup"><span data-stu-id="4d055-597">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="4d055-598">Proto atribut `Key` slouží k identifikaci `InstructorID` jako PK:</span><span class="sxs-lookup"><span data-stu-id="4d055-598">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="4d055-599">Ve výchozím nastavení EF Core považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.</span><span class="sxs-lookup"><span data-stu-id="4d055-599">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="4d055-600">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="4d055-600">The Instructor navigation property</span></span>

<span data-ttu-id="4d055-601">Navigační vlastnost `OfficeAssignment` pro `Instructor` entitu může mít hodnotu null, protože:</span><span class="sxs-lookup"><span data-stu-id="4d055-601">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="4d055-602">Typy odkazů (například třídy mohou mít hodnotu null).</span><span class="sxs-lookup"><span data-stu-id="4d055-602">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="4d055-603">Instruktor nemusí mít přiřazení kanceláře.</span><span class="sxs-lookup"><span data-stu-id="4d055-603">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="4d055-604">Entita `OfficeAssignment` má vlastnost navigace `Instructor`, která není null, protože:</span><span class="sxs-lookup"><span data-stu-id="4d055-604">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="4d055-605">`InstructorID` nemůže mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="4d055-605">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="4d055-606">Přiřazení kanceláře nemůže existovat bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="4d055-606">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="4d055-607">Pokud má entita `Instructor` související entitu `OfficeAssignment`, Každá entita má odkaz na jinou entitu v její navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="4d055-607">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="4d055-608">Atribut `[Required]` lze použít pro `Instructor` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="4d055-608">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="4d055-609">Předchozí kód určuje, že musí existovat související instruktor.</span><span class="sxs-lookup"><span data-stu-id="4d055-609">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="4d055-610">Předchozí kód není nezbytný, protože `InstructorID` cizí klíč (což je také PK), nemůže mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="4d055-610">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="4d055-611">Úprava entity kurzu</span><span class="sxs-lookup"><span data-stu-id="4d055-611">Modify the Course Entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="4d055-613">Aktualizujte *modely/Course. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="4d055-613">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="4d055-614">Entita `Course` má `DepartmentID`vlastnost cizího klíče (FK).</span><span class="sxs-lookup"><span data-stu-id="4d055-614">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="4d055-615">`DepartmentID` odkazuje na související entitu `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d055-615">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="4d055-616">`Course` entita má vlastnost navigace `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d055-616">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="4d055-617">EF Core nevyžaduje vlastnost FK pro datový model, pokud model má vlastnost navigace pro související entitu.</span><span class="sxs-lookup"><span data-stu-id="4d055-617">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="4d055-618">EF Core v databázi automaticky vytvoří FKs bez ohledu na to, kde jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="4d055-618">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="4d055-619">EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automatické vytváření FKs.</span><span class="sxs-lookup"><span data-stu-id="4d055-619">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="4d055-620">Pokud se v datovém modelu nachází FK, může být aktualizace jednodušší a efektivnější.</span><span class="sxs-lookup"><span data-stu-id="4d055-620">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="4d055-621">*Představte* si například model, ve kterém není obsažena vlastnost FK `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="4d055-621">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="4d055-622">Když se načte entita kurzu, která se upraví:</span><span class="sxs-lookup"><span data-stu-id="4d055-622">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="4d055-623">Entita `Department` má hodnotu null, pokud není explicitně načtena.</span><span class="sxs-lookup"><span data-stu-id="4d055-623">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="4d055-624">Chcete-li aktualizovat entitu kurzu, musí být nejprve načtena `Department` entita.</span><span class="sxs-lookup"><span data-stu-id="4d055-624">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="4d055-625">Pokud je vlastnost FK `DepartmentID` obsažena v datovém modelu, není nutné načíst entitu `Department` před aktualizací.</span><span class="sxs-lookup"><span data-stu-id="4d055-625">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="4d055-626">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="4d055-626">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="4d055-627">Atribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` určuje, zda je v rámci aplikace poskytnuta PK místo vygenerovaného databází.</span><span class="sxs-lookup"><span data-stu-id="4d055-627">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="4d055-628">Ve výchozím nastavení EF Core předpokládá, že jsou hodnoty PK generovány databází.</span><span class="sxs-lookup"><span data-stu-id="4d055-628">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="4d055-629">DATABÁZE vygenerovala hodnoty PK většinou nejlepšího přístupu.</span><span class="sxs-lookup"><span data-stu-id="4d055-629">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="4d055-630">Pro `Course` entit určuje uživatel PK.</span><span class="sxs-lookup"><span data-stu-id="4d055-630">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="4d055-631">Například číslo kurzu, jako je například série 1000 pro matematické oddělení, série 2000 pro národní oddělení.</span><span class="sxs-lookup"><span data-stu-id="4d055-631">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="4d055-632">Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="4d055-632">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="4d055-633">DATABÁZE může například automaticky vygenerovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován.</span><span class="sxs-lookup"><span data-stu-id="4d055-633">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="4d055-634">Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="4d055-634">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d055-635">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="4d055-635">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d055-636">Vlastnosti cizího klíče (FK) a navigační vlastnosti v entitě `Course` odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="4d055-636">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="4d055-637">Kurz se přiřadí jednomu oddělení, takže existuje `DepartmentID` FK a vlastnost `Department` navigace.</span><span class="sxs-lookup"><span data-stu-id="4d055-637">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="4d055-638">Kurz může mít zaregistrovaný libovolný počet studentů, takže navigační vlastnost `Enrollments` je kolekce:</span><span class="sxs-lookup"><span data-stu-id="4d055-638">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="4d055-639">Kurz může být výukou více instruktorů, takže navigační vlastnost `CourseAssignments` je kolekce:</span><span class="sxs-lookup"><span data-stu-id="4d055-639">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="4d055-640">`CourseAssignment` je vysvětleno [později](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="4d055-640">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="4d055-641">Vytvořit entitu oddělení</span><span class="sxs-lookup"><span data-stu-id="4d055-641">Create the Department entity</span></span>

![Entita oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="4d055-643">Vytvořte *modely/oddělení. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-643">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="4d055-644">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="4d055-644">The Column attribute</span></span>

<span data-ttu-id="4d055-645">Dřív se použil atribut `Column` pro změnu mapování názvu sloupce.</span><span class="sxs-lookup"><span data-stu-id="4d055-645">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="4d055-646">V kódu pro entitu `Department` se atribut `Column` používá ke změně mapování datových typů SQL.</span><span class="sxs-lookup"><span data-stu-id="4d055-646">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="4d055-647">`Budget` sloupec je definován pomocí SQL Server peněžního typu v databázi:</span><span class="sxs-lookup"><span data-stu-id="4d055-647">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="4d055-648">Mapování sloupce není obecně vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="4d055-648">Column mapping is generally not required.</span></span> <span data-ttu-id="4d055-649">EF Core všeobecně vybere vhodný SQL Server datový typ založený na typu CLR pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4d055-649">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="4d055-650">Typ CLR `decimal` namapuje na typ `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4d055-650">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="4d055-651">`Budget` je pro měnu a datový typ Money je pro měnu vhodný.</span><span class="sxs-lookup"><span data-stu-id="4d055-651">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d055-652">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="4d055-652">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d055-653">Vlastnosti FK a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="4d055-653">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="4d055-654">Oddělení může nebo nemusí mít správce.</span><span class="sxs-lookup"><span data-stu-id="4d055-654">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="4d055-655">Správce je vždy instruktor.</span><span class="sxs-lookup"><span data-stu-id="4d055-655">An administrator is always an instructor.</span></span> <span data-ttu-id="4d055-656">Proto je vlastnost `InstructorID` zahrnutá jako FK do `Instructor` entity.</span><span class="sxs-lookup"><span data-stu-id="4d055-656">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="4d055-657">Navigační vlastnost má název `Administrator`, ale obsahuje `Instructor` entitu:</span><span class="sxs-lookup"><span data-stu-id="4d055-657">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="4d055-658">Otazník (?) v předchozím kódu určuje vlastnost s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="4d055-658">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="4d055-659">Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:</span><span class="sxs-lookup"><span data-stu-id="4d055-659">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="4d055-660">Poznámka: podle konvence EF Core povoluje kaskádové odstranění pro FKs, která nejsou null a pro relace m:n.</span><span class="sxs-lookup"><span data-stu-id="4d055-660">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="4d055-661">Kaskádové odstranění může mít za následek cyklické Kaskádové odstraňování pravidel.</span><span class="sxs-lookup"><span data-stu-id="4d055-661">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="4d055-662">Cyklická kaskádová odstranění pravidel způsobí výjimku při přidání migrace.</span><span class="sxs-lookup"><span data-stu-id="4d055-662">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="4d055-663">Pokud je například vlastnost `Department.InstructorID` definovaná jako hodnota, která není null:</span><span class="sxs-lookup"><span data-stu-id="4d055-663">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="4d055-664">EF Core konfiguruje pravidlo kaskádového odstranění, které odstraní oddělení při odstranění instruktora.</span><span class="sxs-lookup"><span data-stu-id="4d055-664">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="4d055-665">Odstranění oddělení, když se odstraní instruktor, není zamýšleným chováním.</span><span class="sxs-lookup"><span data-stu-id="4d055-665">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="4d055-666">Následující [rozhraní Fluent API](#fluent-api-alternative-to-attributes) by místo kaskádového nastavilo pravidlo omezení.</span><span class="sxs-lookup"><span data-stu-id="4d055-666">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="4d055-667">Předchozí kód zakáže kaskádové odstranění na základě vztahu oddělení a instruktor.</span><span class="sxs-lookup"><span data-stu-id="4d055-667">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="4d055-668">Aktualizace entity registrace</span><span class="sxs-lookup"><span data-stu-id="4d055-668">Update the Enrollment entity</span></span>

<span data-ttu-id="4d055-669">Záznam zápisu je pro jeden kurz, který přijímá jeden student.</span><span class="sxs-lookup"><span data-stu-id="4d055-669">An enrollment record is for one course taken by one student.</span></span>

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="4d055-671">Aktualizujte *modely/zápis. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-671">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="4d055-672">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="4d055-672">Foreign key and navigation properties</span></span>

<span data-ttu-id="4d055-673">Vlastnosti CK a vlastnosti navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="4d055-673">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="4d055-674">Záznam zápisu je pro jeden kurz, takže existuje `CourseID` vlastnost FK a `Course` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="4d055-674">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="4d055-675">Záznam zápisu je určen pro jednoho studenta, takže existuje `StudentID` vlastnost FK a `Student` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="4d055-675">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="4d055-676">Relace m:n</span><span class="sxs-lookup"><span data-stu-id="4d055-676">Many-to-Many Relationships</span></span>

<span data-ttu-id="4d055-677">Mezi entitami `Student` a `Course` existuje vztah m:n.</span><span class="sxs-lookup"><span data-stu-id="4d055-677">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="4d055-678">Entita `Enrollment` funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi.</span><span class="sxs-lookup"><span data-stu-id="4d055-678">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="4d055-679">"S datovou částí" znamená, že `Enrollment` tabulka obsahuje další data kromě FKs pro Spojené tabulky (v tomto případě je to PK a `Grade`).</span><span class="sxs-lookup"><span data-stu-id="4d055-679">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="4d055-680">Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit.</span><span class="sxs-lookup"><span data-stu-id="4d055-680">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="4d055-681">(Tento diagram byl vygenerován pomocí [nástrojů EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6. x.</span><span class="sxs-lookup"><span data-stu-id="4d055-681">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="4d055-682">Vytvoření diagramu není součástí kurzu.)</span><span class="sxs-lookup"><span data-stu-id="4d055-682">Creating the diagram isn't part of the tutorial.)</span></span>

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

<span data-ttu-id="4d055-684">Každá čára relace má 1 na jednom konci a hvězdičku (\*) na druhé straně, která indikuje relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="4d055-684">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="4d055-685">Pokud tabulka `Enrollment` neobsahovala informace o třídě, musí obsahovat pouze dvě FKs (`CourseID` a `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="4d055-685">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="4d055-686">Tabulka JOIN typu m:n bez datové části se někdy označuje jako čistá spojovací tabulka (PJT).</span><span class="sxs-lookup"><span data-stu-id="4d055-686">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="4d055-687">Entity `Instructor` a `Course` mají relaci n:n pomocí tabulky Pure JOIN.</span><span class="sxs-lookup"><span data-stu-id="4d055-687">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="4d055-688">Poznámka: EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne.</span><span class="sxs-lookup"><span data-stu-id="4d055-688">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="4d055-689">Další informace najdete v tématu [relace m:n v EF Core 2,0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="4d055-689">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="4d055-690">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="4d055-690">The CourseAssignment entity</span></span>

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="4d055-692">Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4d055-692">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="4d055-693">Instruktory do kurzů</span><span class="sxs-lookup"><span data-stu-id="4d055-693">Instructor-to-Courses</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="4d055-695">Vztah n:n od instruktora do více kurzů:</span><span class="sxs-lookup"><span data-stu-id="4d055-695">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="4d055-696">Vyžaduje tabulku JOIN, která musí být reprezentována sadou entit.</span><span class="sxs-lookup"><span data-stu-id="4d055-696">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="4d055-697">Je čistá spojovací tabulka (tabulka bez datové části).</span><span class="sxs-lookup"><span data-stu-id="4d055-697">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="4d055-698">Je běžné pojmenovat entitu JOIN `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="4d055-698">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="4d055-699">Například tabulka pro spojení instruktora do kurzů, která používá tento model, je `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="4d055-699">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="4d055-700">Doporučujeme však použít název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="4d055-700">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="4d055-701">Modely dat začínají jednoduchým a roste.</span><span class="sxs-lookup"><span data-stu-id="4d055-701">Data models start out simple and grow.</span></span> <span data-ttu-id="4d055-702">Spojení bez datové části (PJTs) se často rozvíjejí, aby zahrnovalo datovou část.</span><span class="sxs-lookup"><span data-stu-id="4d055-702">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="4d055-703">Když začnete s popisným názvem entity, nemusíte při změně tabulky JOIN měnit název.</span><span class="sxs-lookup"><span data-stu-id="4d055-703">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="4d055-704">V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="4d055-704">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="4d055-705">Například knihy a zákazníci mohou být propojeny s entitou JOIN nazvanou hodnocení.</span><span class="sxs-lookup"><span data-stu-id="4d055-705">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="4d055-706">Pro relaci n:n od instruktora na více kurzů `CourseAssignment` přednost před `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="4d055-706">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="4d055-707">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="4d055-707">Composite key</span></span>

<span data-ttu-id="4d055-708">FKs nemohou mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="4d055-708">FKs are not nullable.</span></span> <span data-ttu-id="4d055-709">Dva FKs v `CourseAssignment` (`InstructorID` a `CourseID`) společně jednoznačně identifikují každý řádek tabulky `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d055-709">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="4d055-710">`CourseAssignment` nevyžaduje vyhrazený PK.</span><span class="sxs-lookup"><span data-stu-id="4d055-710">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="4d055-711">Vlastnosti `InstructorID` a `CourseID` fungují jako složený PK.</span><span class="sxs-lookup"><span data-stu-id="4d055-711">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="4d055-712">Jediným způsobem, jak zadat složené PKs EF Core je s *rozhraním API Fluent*.</span><span class="sxs-lookup"><span data-stu-id="4d055-712">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="4d055-713">V další části se dozvíte, jak nakonfigurovat složený PK.</span><span class="sxs-lookup"><span data-stu-id="4d055-713">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="4d055-714">Složený klíč zajišťuje:</span><span class="sxs-lookup"><span data-stu-id="4d055-714">The composite key ensures:</span></span>

* <span data-ttu-id="4d055-715">Pro jeden kurz je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="4d055-715">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="4d055-716">Pro jednoho instruktora je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="4d055-716">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="4d055-717">Více řádků pro stejný instruktor a kurz není povoleno.</span><span class="sxs-lookup"><span data-stu-id="4d055-717">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="4d055-718">Entita `Enrollment` JOIN definuje vlastní PK, takže je možné duplikovat toto řazení.</span><span class="sxs-lookup"><span data-stu-id="4d055-718">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="4d055-719">Chcete-li zabránit těmto duplicitám:</span><span class="sxs-lookup"><span data-stu-id="4d055-719">To prevent such duplicates:</span></span>

* <span data-ttu-id="4d055-720">Přidejte do polí FK jedinečný index nebo</span><span class="sxs-lookup"><span data-stu-id="4d055-720">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="4d055-721">Nakonfigurujte `Enrollment` s primárním kombinovaným klíčem podobným `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d055-721">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="4d055-722">Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="4d055-722">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="4d055-723">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="4d055-723">Update the DB context</span></span>

<span data-ttu-id="4d055-724">Do *data/SchoolContext. cs*přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="4d055-724">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="4d055-725">Předchozí kód přidá nové entity a nakonfiguruje neseparovaný PK entity `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d055-725">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="4d055-726">Alternativa k atributům rozhraní Fluent API</span><span class="sxs-lookup"><span data-stu-id="4d055-726">Fluent API alternative to attributes</span></span>

<span data-ttu-id="4d055-727">Metoda `OnModelCreating` v předchozím kódu používá ke konfiguraci chování EF Core *rozhraní Fluent API* .</span><span class="sxs-lookup"><span data-stu-id="4d055-727">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="4d055-728">Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu.</span><span class="sxs-lookup"><span data-stu-id="4d055-728">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="4d055-729">[Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem rozhraní Fluent API:</span><span class="sxs-lookup"><span data-stu-id="4d055-729">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="4d055-730">V tomto kurzu se rozhraní API Fluent používá jenom pro mapování DB, které nejde s atributy dělat.</span><span class="sxs-lookup"><span data-stu-id="4d055-730">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="4d055-731">Rozhraní API Fluent ale může určovat většinu pravidel formátování, ověřování a mapování, která se dají provádět s atributy.</span><span class="sxs-lookup"><span data-stu-id="4d055-731">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="4d055-732">Některé atributy, jako je `MinimumLength`, se nedají použít s rozhraním API Fluent.</span><span class="sxs-lookup"><span data-stu-id="4d055-732">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="4d055-733">`MinimumLength` nemění schéma, používá pouze ověřovací pravidlo minimální délky.</span><span class="sxs-lookup"><span data-stu-id="4d055-733">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="4d055-734">Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit".</span><span class="sxs-lookup"><span data-stu-id="4d055-734">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="4d055-735">Atributy a rozhraní API Fluent lze kombinovat.</span><span class="sxs-lookup"><span data-stu-id="4d055-735">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="4d055-736">Existují některé konfigurace, které lze provést pouze s rozhraním API Fluent (určením složeného PK).</span><span class="sxs-lookup"><span data-stu-id="4d055-736">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="4d055-737">Existují některé konfigurace, které lze provádět pouze s atributy (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="4d055-737">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="4d055-738">Doporučený postup pro použití rozhraní Fluent API nebo atributů:</span><span class="sxs-lookup"><span data-stu-id="4d055-738">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="4d055-739">Vyberte jednu z těchto dvou přístupů.</span><span class="sxs-lookup"><span data-stu-id="4d055-739">Choose one of these two approaches.</span></span>
* <span data-ttu-id="4d055-740">Používejte vybraný postup konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="4d055-740">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="4d055-741">Některé atributy použité v tomto kurzu se používají pro:</span><span class="sxs-lookup"><span data-stu-id="4d055-741">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="4d055-742">Pouze ověření (například `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="4d055-742">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="4d055-743">Pouze konfigurace EF Core (například `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="4d055-743">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="4d055-744">Konfigurace ověřování a EF Core (například `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="4d055-744">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="4d055-745">Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="4d055-745">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="4d055-746">Diagram entit znázorňující vztahy</span><span class="sxs-lookup"><span data-stu-id="4d055-746">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="4d055-747">Následující ilustrace znázorňuje diagram, který nástroje EF Power Tools vytvoří pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="4d055-747">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="4d055-749">Předchozí diagram znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="4d055-749">The preceding diagram shows:</span></span>

* <span data-ttu-id="4d055-750">Několik čar relací 1:1 (1 až \*).</span><span class="sxs-lookup"><span data-stu-id="4d055-750">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="4d055-751">Čára relace 1:1 (1 – 0.. 1) mezi entitami `Instructor` a `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="4d055-751">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="4d055-752">Čára vztahu nula až n-many (0.. 1 až ×) mezi entitami `Instructor` a `Department`.</span><span class="sxs-lookup"><span data-stu-id="4d055-752">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="4d055-753">Osazení databáze pomocí testovacích dat</span><span class="sxs-lookup"><span data-stu-id="4d055-753">Seed the DB with Test Data</span></span>

<span data-ttu-id="4d055-754">Aktualizujte kód v *data/DbInitializer. cs*:</span><span class="sxs-lookup"><span data-stu-id="4d055-754">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="4d055-755">Předchozí kód poskytuje počáteční data pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="4d055-755">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="4d055-756">Většina tohoto kódu vytváří nové objekty entit a načítá vzorová data.</span><span class="sxs-lookup"><span data-stu-id="4d055-756">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="4d055-757">Ukázková data se používají k testování.</span><span class="sxs-lookup"><span data-stu-id="4d055-757">The sample data is used for testing.</span></span> <span data-ttu-id="4d055-758">Příklady toho, kolik tabulek Join lze dosazením, najdete v tématu `Enrollments` a `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="4d055-758">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="4d055-759">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="4d055-759">Add a migration</span></span>

<span data-ttu-id="4d055-760">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="4d055-760">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d055-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d055-761">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d055-762">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d055-762">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="4d055-763">Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="4d055-763">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="4d055-764">Pokud se spustí příkaz `database update`, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="4d055-764">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="4d055-765">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="4d055-765">Apply the migration</span></span>

<span data-ttu-id="4d055-766">Teď, když máte existující databázi, musíte si představit, jak v nich použít budoucí změny.</span><span class="sxs-lookup"><span data-stu-id="4d055-766">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="4d055-767">V tomto kurzu se dozvíte dva přístupy:</span><span class="sxs-lookup"><span data-stu-id="4d055-767">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="4d055-768">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="4d055-768">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="4d055-769">[Použijte migraci na stávající databázi](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="4d055-769">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="4d055-770">I když je tato metoda složitější a časově náročná, jedná se o preferovaný přístup pro produkční prostředí z reálného světa.</span><span class="sxs-lookup"><span data-stu-id="4d055-770">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="4d055-771">**Poznámka**: Toto je volitelný oddíl tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="4d055-771">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="4d055-772">Můžete provést kroky odkládací a znovu vytvořit a tuto část přeskočit.</span><span class="sxs-lookup"><span data-stu-id="4d055-772">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="4d055-773">Pokud chcete postupovat podle kroků v této části, neprovádějte kroky odkládací a znovu vytvořit.</span><span class="sxs-lookup"><span data-stu-id="4d055-773">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4d055-774">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="4d055-774">Drop and re-create the database</span></span>

<span data-ttu-id="4d055-775">Kód v aktualizované `DbInitializer` přidá počáteční data pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="4d055-775">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="4d055-776">Pokud chcete vynutit EF Core vytvoření nové databáze, vyřaďte a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="4d055-776">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d055-777">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d055-777">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4d055-778">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4d055-778">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="4d055-779">Pokud chcete získat informace o nápovědě, spusťte `Get-Help about_EntityFrameworkCore` z PMC.</span><span class="sxs-lookup"><span data-stu-id="4d055-779">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d055-780">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d055-780">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d055-781">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="4d055-781">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="4d055-782">Složka projektu obsahuje soubor *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="4d055-782">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="4d055-783">V příkazovém okně zadejte následující:</span><span class="sxs-lookup"><span data-stu-id="4d055-783">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="4d055-784">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4d055-784">Run the app.</span></span> <span data-ttu-id="4d055-785">Spuštění aplikace spustí metodu `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="4d055-785">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="4d055-786">`DbInitializer.Initialize` naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="4d055-786">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="4d055-787">Otevřete databázi v SSOX:</span><span class="sxs-lookup"><span data-stu-id="4d055-787">Open the DB in SSOX:</span></span>

* <span data-ttu-id="4d055-788">Pokud jste dříve otevřeli SSOX, klikněte na tlačítko **aktualizovat** .</span><span class="sxs-lookup"><span data-stu-id="4d055-788">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="4d055-789">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="4d055-789">Expand the **Tables** node.</span></span> <span data-ttu-id="4d055-790">Zobrazí se vytvořené tabulky.</span><span class="sxs-lookup"><span data-stu-id="4d055-790">The created tables are displayed.</span></span>

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="4d055-792">Projděte si tabulku **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="4d055-792">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="4d055-793">Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data**.</span><span class="sxs-lookup"><span data-stu-id="4d055-793">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="4d055-794">Ověřte, že tabulka **CourseAssignment** obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="4d055-794">Verify the **CourseAssignment** table contains data.</span></span>

![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="4d055-796">Použít migraci na existující databázi</span><span class="sxs-lookup"><span data-stu-id="4d055-796">Apply the migration to the existing database</span></span>

<span data-ttu-id="4d055-797">Tato část je volitelná.</span><span class="sxs-lookup"><span data-stu-id="4d055-797">This section is optional.</span></span> <span data-ttu-id="4d055-798">Tyto kroky fungují pouze v případě, že jste přeskočili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="4d055-798">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="4d055-799">Pokud jsou migrace spouštěny s existujícími daty, může dojít k omezením FK, která nesplňují stávající data.</span><span class="sxs-lookup"><span data-stu-id="4d055-799">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="4d055-800">S provozními daty je potřeba provést kroky pro migraci stávajících dat.</span><span class="sxs-lookup"><span data-stu-id="4d055-800">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="4d055-801">V této části najdete příklad opravy porušení omezení CK.</span><span class="sxs-lookup"><span data-stu-id="4d055-801">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="4d055-802">Neprovádějte změny kódu bez zálohy.</span><span class="sxs-lookup"><span data-stu-id="4d055-802">Don't make these code changes without a backup.</span></span> <span data-ttu-id="4d055-803">Neprovádějte změny kódu, pokud jste dokončili předchozí oddíl a aktualizovali databázi.</span><span class="sxs-lookup"><span data-stu-id="4d055-803">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="4d055-804">Soubor *{timestamp} _ComplexDataModel. cs* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="4d055-804">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="4d055-805">Předchozí kód přidá do tabulky `Course` `DepartmentID` FK, který nemůže mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="4d055-805">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="4d055-806">DATABÁZE z předchozího kurzu obsahuje řádky v `Course`, takže se tabulka nedá aktualizovat pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="4d055-806">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="4d055-807">Postup při práci `ComplexDataModel` migrace s existujícími daty:</span><span class="sxs-lookup"><span data-stu-id="4d055-807">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="4d055-808">Změňte kód tak, aby měl nový sloupec (`DepartmentID`) výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4d055-808">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="4d055-809">Vytvořte falešné oddělení s názvem "Temp", které bude sloužit jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="4d055-809">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="4d055-810">Oprava omezení cizího klíče</span><span class="sxs-lookup"><span data-stu-id="4d055-810">Fix the foreign key constraints</span></span>

<span data-ttu-id="4d055-811">Aktualizujte `ComplexDataModel` třídy `Up` metodu:</span><span class="sxs-lookup"><span data-stu-id="4d055-811">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="4d055-812">Otevřete soubor *{timestamp} _ComplexDataModel. cs* .</span><span class="sxs-lookup"><span data-stu-id="4d055-812">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="4d055-813">Odkomentujte řádek kódu, který přidá sloupec `DepartmentID` do tabulky `Course`.</span><span class="sxs-lookup"><span data-stu-id="4d055-813">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="4d055-814">Přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="4d055-814">Add the following highlighted code.</span></span> <span data-ttu-id="4d055-815">Nový kód přejde za `.CreateTable( name: "Department"` blok:</span><span class="sxs-lookup"><span data-stu-id="4d055-815">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="4d055-816">V předchozích změnách budou existující `Course` řádky při spuštění metody `ComplexDataModel` `Up` v souvislosti s oddělením "dočasného".</span><span class="sxs-lookup"><span data-stu-id="4d055-816">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="4d055-817">Produkční aplikace by:</span><span class="sxs-lookup"><span data-stu-id="4d055-817">A production app would:</span></span>

* <span data-ttu-id="4d055-818">Zahrnout kód nebo skripty pro přidání `Department` řádků a souvisejících `Course` řádků do nových `Department` řádků.</span><span class="sxs-lookup"><span data-stu-id="4d055-818">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="4d055-819">Nepoužívejte oddělení "dočasné" nebo výchozí hodnotu pro `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="4d055-819">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="4d055-820">Další kurz se zabývá souvisejícími daty.</span><span class="sxs-lookup"><span data-stu-id="4d055-820">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d055-821">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4d055-821">Additional resources</span></span>

* [<span data-ttu-id="4d055-822">Verze tohoto kurzu pro YouTube (část 1)</span><span class="sxs-lookup"><span data-stu-id="4d055-822">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="4d055-823">Verze tohoto kurzu pro YouTube (část 2)</span><span class="sxs-lookup"><span data-stu-id="4d055-823">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="4d055-824">[Předchozí](xref:data/ef-rp/migrations)
> [Další](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="4d055-824">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
