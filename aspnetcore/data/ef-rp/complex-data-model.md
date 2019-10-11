---
title: Razor Pages s EF Core v ASP.NET Core-datový model 5 z 8
author: rick-anderson
description: V tomto kurzu přidejte další entity a vztahy a upravte datový model zadáním formátování, ověřování a pravidel mapování.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 2461bc398cd237dac04f4eb8832c70290663ff56
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259487"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="5c54e-103">Razor Pages s EF Core v ASP.NET Core-datový model 5 z 8</span><span class="sxs-lookup"><span data-stu-id="5c54e-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="5c54e-104">[Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5c54e-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c54e-105">Předchozí kurzy pracovaly se základním datovým modelem, který se skládá ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="5c54e-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="5c54e-106">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-106">In this tutorial:</span></span>

* <span data-ttu-id="5c54e-107">Přidávají se další entity a vztahy.</span><span class="sxs-lookup"><span data-stu-id="5c54e-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="5c54e-108">Datový model je přizpůsoben zadáním pravidel formátování, ověřování a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="5c54e-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="5c54e-109">Dokončený datový model je zobrazený na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="5c54e-109">The completed data model is shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="5c54e-111">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="5c54e-111">The Student entity</span></span>

![Entita studenta](complex-data-model/_static/student-entity.png)

<span data-ttu-id="5c54e-113">Nahraďte kód v *modelů/student. cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="5c54e-114">Předchozí kód přidá vlastnost `FullName` a přidá následující atributy do existujících vlastností:</span><span class="sxs-lookup"><span data-stu-id="5c54e-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="5c54e-115">Vypočítaná vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="5c54e-115">The FullName calculated property</span></span>

<span data-ttu-id="5c54e-116">`FullName` je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="5c54e-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="5c54e-117">`FullName` nelze nastavit, takže má pouze přistupující objekt get.</span><span class="sxs-lookup"><span data-stu-id="5c54e-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="5c54e-118">V databázi se nevytvoří žádný sloupec `FullName`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="5c54e-119">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="5c54e-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="5c54e-120">Pro data o registraci studenta aktuálně zobrazují všechny stránky denní dobu a datum, i když se jedná o relevantní datum.</span><span class="sxs-lookup"><span data-stu-id="5c54e-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="5c54e-121">Pomocí atributů datových poznámek můžete vytvořit jednu změnu kódu, která bude opravovat formát zobrazení na každé stránce, která zobrazuje data.</span><span class="sxs-lookup"><span data-stu-id="5c54e-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="5c54e-122">Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) Určuje datový typ, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="5c54e-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="5c54e-123">V tomto případě by se měla zobrazit pouze datum, nikoli datum a čas.</span><span class="sxs-lookup"><span data-stu-id="5c54e-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="5c54e-124">[Výčet DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress atd. Atribut `DataType` může také aplikaci povolit automatické poskytování funkcí specifických pro typ.</span><span class="sxs-lookup"><span data-stu-id="5c54e-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="5c54e-125">Například:</span><span class="sxs-lookup"><span data-stu-id="5c54e-125">For example:</span></span>

* <span data-ttu-id="5c54e-126">Propojení `mailto:` se automaticky vytvoří pro `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="5c54e-127">Selektor data je k dispozici pro `DataType.Date` ve většině prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="5c54e-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="5c54e-128">Atribut `DataType` emituje atributy HTML 5 `data-` (vyslovení data Přerušované čárky).</span><span class="sxs-lookup"><span data-stu-id="5c54e-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="5c54e-129">Atributy `DataType` neposkytují ověřování.</span><span class="sxs-lookup"><span data-stu-id="5c54e-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="5c54e-130">Atribut DisplayFormat</span><span class="sxs-lookup"><span data-stu-id="5c54e-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="5c54e-131">`DataType.Date` neurčuje formát data, které se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="5c54e-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="5c54e-132">Ve výchozím nastavení se pole Datum zobrazuje v závislosti na výchozích formátech na základě objektu [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serveru.</span><span class="sxs-lookup"><span data-stu-id="5c54e-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="5c54e-133">Atribut `DisplayFormat` slouží k explicitnímu zadání formátu data.</span><span class="sxs-lookup"><span data-stu-id="5c54e-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="5c54e-134">Nastavení `ApplyFormatInEditMode` určuje, že by mělo být formátování použito i pro uživatelské rozhraní úprav.</span><span class="sxs-lookup"><span data-stu-id="5c54e-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="5c54e-135">Některá pole by neměla používat `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="5c54e-136">Například symbol měny by neměl být v textovém poli pro úpravy obvykle zobrazen.</span><span class="sxs-lookup"><span data-stu-id="5c54e-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="5c54e-137">Atribut `DisplayFormat` může používat sám sebe.</span><span class="sxs-lookup"><span data-stu-id="5c54e-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="5c54e-138">Obecně je vhodné použít atribut `DataType` s atributem `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="5c54e-139">Atribut `DataType` vyjadřuje sémantiku dat na rozdíl od toho, jak ji vykreslit na obrazovce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="5c54e-140">Atribut `DataType` poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="5c54e-141">Prohlížeč může povolit funkce HTML5.</span><span class="sxs-lookup"><span data-stu-id="5c54e-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="5c54e-142">Například můžete zobrazit ovládací prvek kalendáře, symbol měny odpovídající národním prostředí, e-mailové odkazy a ověření vstupu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="5c54e-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="5c54e-143">Ve výchozím nastavení prohlížeč vykresluje data pomocí správného formátu založeného na národním prostředí.</span><span class="sxs-lookup"><span data-stu-id="5c54e-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="5c54e-144">Další informace najdete v dokumentaci k [pomocníka značek \<input >](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5c54e-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="5c54e-145">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="5c54e-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="5c54e-146">Pravidla ověřování dat a chybové zprávy ověřování lze zadat pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="5c54e-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="5c54e-147">Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli.</span><span class="sxs-lookup"><span data-stu-id="5c54e-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="5c54e-148">Zobrazený kód omezuje názvy na více než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="5c54e-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="5c54e-149">Příklad, který nastaví minimální délku řetězce, se zobrazí [později](#the-required-attribute).</span><span class="sxs-lookup"><span data-stu-id="5c54e-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="5c54e-150">Atribut `StringLength` také poskytuje ověřování na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="5c54e-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="5c54e-151">Minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="5c54e-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="5c54e-152">Atribut `StringLength` nebrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="5c54e-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="5c54e-153">Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) lze použít k uplatnění omezení na vstup.</span><span class="sxs-lookup"><span data-stu-id="5c54e-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="5c54e-154">Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:</span><span class="sxs-lookup"><span data-stu-id="5c54e-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c54e-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c54e-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c54e-156">V **Průzkumník objektů systému SQL Server** (SSOX) otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .</span><span class="sxs-lookup"><span data-stu-id="5c54e-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů v SSOX před migracemi](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="5c54e-158">Na předchozím obrázku je znázorněno schéma pro tabulku `Student`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="5c54e-159">Pole název mají typ `nvarchar(MAX)`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="5c54e-160">Když se vytvoří migrace a použije se později v tomto kurzu, pole s názvem se stanou `nvarchar(50)` v důsledku atributů délky řetězce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5c54e-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c54e-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5c54e-162">V nástroji SQLite si Projděte definice sloupců pro tabulku `Student`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="5c54e-163">Pole název mají typ `Text`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-163">The name fields have type `Text`.</span></span> <span data-ttu-id="5c54e-164">Všimněte si, že pole jméno se nazývá `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="5c54e-165">V další části změníte název tohoto sloupce na `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="5c54e-166">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="5c54e-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="5c54e-167">Atributy mohou řídit způsob, jakým jsou třídy a vlastnosti mapovány na databázi.</span><span class="sxs-lookup"><span data-stu-id="5c54e-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="5c54e-168">V modelu `Student` se k mapování názvu vlastnosti `FirstMidName` na FirstName v databázi používá atribut `Column`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="5c54e-169">Při vytvoření databáze se názvy vlastností v modelu používají pro názvy sloupců (s výjimkou případů, kdy se používá atribut `Column`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="5c54e-170">Model `Student` používá pro pole první název `FirstMidName`, protože pole může obsahovat také prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="5c54e-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="5c54e-171">Pomocí atributu `[Column]` se v datovém modelu `Student.FirstMidName` mapuje do sloupce `FirstName` tabulky `Student`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="5c54e-172">Přidání atributu `Column` změní model zálohování `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="5c54e-173">Model zálohování `SchoolContext` se již neshoduje s databází.</span><span class="sxs-lookup"><span data-stu-id="5c54e-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="5c54e-174">Tato nesrovnalost bude vyřešena přidáním migrace později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="5c54e-175">Požadovaný atribut</span><span class="sxs-lookup"><span data-stu-id="5c54e-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="5c54e-176">Atribut `Required` zpřístupňuje vlastnosti název povinná pole.</span><span class="sxs-lookup"><span data-stu-id="5c54e-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="5c54e-177">Atribut `Required` není potřebný pro typy, které nejsou null, jako jsou například typy hodnot (například `DateTime`, `int` a `double`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="5c54e-178">Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="5c54e-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="5c54e-179">Pro vymáhání `MinimumLength` musí být použit atribut `Required` s `MinimumLength`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="5c54e-180">`MinimumLength` a `Required` povolí, aby se pro ověření vyplnily prázdné znaky.</span><span class="sxs-lookup"><span data-stu-id="5c54e-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="5c54e-181">Pro úplnou kontrolu nad řetězcem použijte atribut `RegularExpression`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="5c54e-182">Atribut zobrazení</span><span class="sxs-lookup"><span data-stu-id="5c54e-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="5c54e-183">Atribut `Display` určuje, že titulek pro textová pole by měl být "jméno", "příjmení", "celé jméno" a "datum zápisu".</span><span class="sxs-lookup"><span data-stu-id="5c54e-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="5c54e-184">Výchozí titulky neobsahovaly místo dělení slov, například "LastName".</span><span class="sxs-lookup"><span data-stu-id="5c54e-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="5c54e-185">Vytvoření migrace</span><span class="sxs-lookup"><span data-stu-id="5c54e-185">Create a migration</span></span>

<span data-ttu-id="5c54e-186">Spusťte aplikaci a pokračujte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="5c54e-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="5c54e-187">Je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="5c54e-187">An exception is thrown.</span></span> <span data-ttu-id="5c54e-188">Atribut `[Column]` způsobuje, že EF očekává, že najde sloupec s názvem `FirstName`, ale název sloupce v databázi je stále `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c54e-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c54e-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c54e-190">Chybová zpráva je podobná následujícímu příkladu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="5c54e-191">Do PMC zadejte následující příkazy, abyste vytvořili novou migraci a aktualizovali databázi:</span><span class="sxs-lookup"><span data-stu-id="5c54e-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="5c54e-192">První z těchto příkazů generuje následující varovnou zprávu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="5c54e-193">Upozornění je vygenerováno, protože pole s názvem jsou nyní omezena na 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="5c54e-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="5c54e-194">Pokud má název v databázi více než 50 znaků, bude ztraceno 51 k poslednímu znaku.</span><span class="sxs-lookup"><span data-stu-id="5c54e-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="5c54e-195">Otevřete tabulku student v SSOX:</span><span class="sxs-lookup"><span data-stu-id="5c54e-195">Open the Student table in SSOX:</span></span>

  ![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="5c54e-197">Před použitím migrace byly sloupce názvu typu [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="5c54e-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="5c54e-198">Sloupce s názvem jsou nyní `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="5c54e-199">Název sloupce se změnil z `FirstMidName` na `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5c54e-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c54e-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5c54e-201">Chybová zpráva je podobná následujícímu příkladu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="5c54e-202">Otevřete okno příkazového řádku ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-202">Open a command window in the project folder.</span></span> <span data-ttu-id="5c54e-203">Zadáním následujících příkazů vytvořte novou migraci a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="5c54e-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="5c54e-204">Příkaz aktualizace databáze zobrazí chybu jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="5c54e-205">Pro tento kurz se způsobem, jak se tato chyba zobrazí po této chybě, odstranit a znovu vytvořit počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="5c54e-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="5c54e-206">Další informace najdete v poznámkovém upozornění SQLite v horní části [kurzu migrace](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="5c54e-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="5c54e-207">Odstraňte složku *migrace* .</span><span class="sxs-lookup"><span data-stu-id="5c54e-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="5c54e-208">Spusťte následující příkazy k vyřazení databáze, vytvořte novou počáteční migraci a použijte migraci:</span><span class="sxs-lookup"><span data-stu-id="5c54e-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="5c54e-209">Projděte si tabulku student v nástroji SQLite.</span><span class="sxs-lookup"><span data-stu-id="5c54e-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="5c54e-210">Sloupec, který byl FirstMidName, je nyní FirstName.</span><span class="sxs-lookup"><span data-stu-id="5c54e-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="5c54e-211">Spusťte aplikaci a pokračujte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="5c54e-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="5c54e-212">Všimněte si, že časy nejsou vstupní ani se nezobrazují spolu s kalendářními daty.</span><span class="sxs-lookup"><span data-stu-id="5c54e-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="5c54e-213">Vyberte **vytvořit novou**a zkuste zadat název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="5c54e-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="5c54e-214">V následujících oddílech sestavení aplikace v některých fázích generuje chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="5c54e-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="5c54e-215">Pokyny určují, kdy se má aplikace sestavit.</span><span class="sxs-lookup"><span data-stu-id="5c54e-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="5c54e-216">Entita instruktor</span><span class="sxs-lookup"><span data-stu-id="5c54e-216">The Instructor Entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="5c54e-218">Vytvořte *modely/Instructor. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="5c54e-219">Více atributů může být na jednom řádku.</span><span class="sxs-lookup"><span data-stu-id="5c54e-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="5c54e-220">Atributy `HireDate` lze zapsat takto:</span><span class="sxs-lookup"><span data-stu-id="5c54e-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="5c54e-221">Navigační vlastnosti</span><span class="sxs-lookup"><span data-stu-id="5c54e-221">Navigation properties</span></span>

<span data-ttu-id="5c54e-222">Vlastnosti `CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5c54e-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="5c54e-223">Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="5c54e-224">Instruktor může mít maximálně jednu kancelář, takže vlastnost `OfficeAssignment` obsahuje jednu entitu `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="5c54e-225">`OfficeAssignment` je null, pokud není přiřazen žádný systém Office.</span><span class="sxs-lookup"><span data-stu-id="5c54e-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="5c54e-226">Entita OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="5c54e-226">The OfficeAssignment entity</span></span>

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="5c54e-228">Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="5c54e-229">Klíčový atribut</span><span class="sxs-lookup"><span data-stu-id="5c54e-229">The Key attribute</span></span>

<span data-ttu-id="5c54e-230">Atribut `[Key]` slouží k identifikaci vlastnosti jako primárního klíče (PK), pokud je název vlastnosti něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="5c54e-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="5c54e-231">Mezi entitami `Instructor` a `OfficeAssignment` existuje vztah 1:1 nebo 1:1.</span><span class="sxs-lookup"><span data-stu-id="5c54e-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="5c54e-232">Přiřazení kanceláře existuje jenom ve vztahu k instruktorovi, ke kterému je přiřazený.</span><span class="sxs-lookup"><span data-stu-id="5c54e-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="5c54e-233">@No__t-0 PK je také jeho cizí klíč (FK) k entitě `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="5c54e-234">EF Core nemůže automaticky rozpoznat `InstructorID` jako PK `OfficeAssignment`, protože `InstructorID` nedodržuje konvence pojmenování ID nebo classnameID.</span><span class="sxs-lookup"><span data-stu-id="5c54e-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="5c54e-235">Proto se atribut `Key` používá k identifikaci `InstructorID` jako PK:</span><span class="sxs-lookup"><span data-stu-id="5c54e-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="5c54e-236">Ve výchozím nastavení EF Core považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="5c54e-237">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="5c54e-237">The Instructor navigation property</span></span>

<span data-ttu-id="5c54e-238">Vlastnost navigace `Instructor.OfficeAssignment` může mít hodnotu null, protože pro daného instruktora pravděpodobně není k dispozici řádek `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="5c54e-239">Instruktor nemusí mít přiřazení kanceláře.</span><span class="sxs-lookup"><span data-stu-id="5c54e-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="5c54e-240">Navigační vlastnost `OfficeAssignment.Instructor` bude mít vždycky entitu instruktora, protože cizí klíč `InstructorID` je typu `int`, typ hodnoty, která není null.</span><span class="sxs-lookup"><span data-stu-id="5c54e-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="5c54e-241">Přiřazení kanceláře nemůže existovat bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="5c54e-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="5c54e-242">Pokud má entita `Instructor` související entitu `OfficeAssignment`, má Každá entita odkaz na druhou vlastnost v její navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5c54e-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="5c54e-243">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="5c54e-243">The Course Entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="5c54e-245">Aktualizujte *modely/Course. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="5c54e-246">Entita `Course` má vlastnost cizího klíče (FK) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="5c54e-247">`DepartmentID` odkazuje na související entitu `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="5c54e-248">Entita `Course` má vlastnost navigace `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="5c54e-249">EF Core nevyžaduje vlastnost cizího klíče pro datový model, pokud model má vlastnost navigace pro související entitu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="5c54e-250">EF Core v databázi automaticky vytvoří FKs bez ohledu na to, kde jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="5c54e-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="5c54e-251">EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automatické vytváření FKs.</span><span class="sxs-lookup"><span data-stu-id="5c54e-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="5c54e-252">Explicitní a efektivnější je však, že explicitně včetně FK v datovém modelu může zjednodušit a efektivněji dělat aktualizace.</span><span class="sxs-lookup"><span data-stu-id="5c54e-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="5c54e-253">*Představte* si například model, ve kterém není k dispozici vlastnost FK `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="5c54e-254">Když se načte entita kurzu, která se upraví:</span><span class="sxs-lookup"><span data-stu-id="5c54e-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="5c54e-255">Vlastnost `Department` má hodnotu null, pokud není explicitně načtena.</span><span class="sxs-lookup"><span data-stu-id="5c54e-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="5c54e-256">Chcete-li aktualizovat entitu kurzu, musí být nejprve načtena entita `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="5c54e-257">Pokud je vlastnost FK `DepartmentID` obsažena v datovém modelu, není nutné před aktualizací načíst entitu `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="5c54e-258">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="5c54e-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="5c54e-259">Atribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` určuje, že se má v rámci aplikace poskytnout PK místo vygenerované databází.</span><span class="sxs-lookup"><span data-stu-id="5c54e-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="5c54e-260">Ve výchozím nastavení EF Core předpokládá, že se hodnoty PK generují v databázi.</span><span class="sxs-lookup"><span data-stu-id="5c54e-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="5c54e-261">Vygenerovaná databáze obvykle představuje nejlepší přístup.</span><span class="sxs-lookup"><span data-stu-id="5c54e-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="5c54e-262">Pro entity `Course` určuje uživatel PK.</span><span class="sxs-lookup"><span data-stu-id="5c54e-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="5c54e-263">Například číslo kurzu, jako je například série 1000 pro matematické oddělení, série 2000 pro národní oddělení.</span><span class="sxs-lookup"><span data-stu-id="5c54e-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="5c54e-264">Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="5c54e-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="5c54e-265">Databáze může například automaticky vygenerovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován.</span><span class="sxs-lookup"><span data-stu-id="5c54e-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="5c54e-266">Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="5c54e-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5c54e-267">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="5c54e-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="5c54e-268">Vlastnosti cizího klíče (FK) a navigační vlastnosti v entitě `Course` odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="5c54e-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="5c54e-269">Kurz se přiřadí jednomu oddělení, takže existuje `DepartmentID` FK a navigační vlastnost `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="5c54e-270">V rámci kurzu může být zaregistrované několik studentů, takže navigační vlastnost `Enrollments` je kolekce:</span><span class="sxs-lookup"><span data-stu-id="5c54e-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="5c54e-271">Kurz může být výukou více instruktorů, takže navigační vlastnost `CourseAssignments` je kolekce:</span><span class="sxs-lookup"><span data-stu-id="5c54e-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="5c54e-272">`CourseAssignment` je vysvětleno [později](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="5c54e-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="5c54e-273">Entita oddělení</span><span class="sxs-lookup"><span data-stu-id="5c54e-273">The Department entity</span></span>

![Entita oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="5c54e-275">Vytvořte *modely/oddělení. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="5c54e-276">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="5c54e-276">The Column attribute</span></span>

<span data-ttu-id="5c54e-277">Dřív se použil atribut `Column` pro změnu mapování názvu sloupce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="5c54e-278">V kódu pro entitu `Department` se pro změnu mapování datových typů SQL používá atribut `Column`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="5c54e-279">Sloupec `Budget` je definován pomocí SQL Server typu Money v databázi:</span><span class="sxs-lookup"><span data-stu-id="5c54e-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="5c54e-280">Mapování sloupce není obecně vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="5c54e-280">Column mapping is generally not required.</span></span> <span data-ttu-id="5c54e-281">EF Core zvolí příslušný datový typ SQL Server na základě typu CLR pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5c54e-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="5c54e-282">Typ CLR `decimal` se mapuje na typ SQL Server `decimal`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="5c54e-283">`Budget` je pro měnu a datový typ Money je pro měnu vhodný.</span><span class="sxs-lookup"><span data-stu-id="5c54e-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5c54e-284">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="5c54e-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="5c54e-285">Vlastnosti FK a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="5c54e-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="5c54e-286">Oddělení může nebo nemusí mít správce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="5c54e-287">Správce je vždy instruktor.</span><span class="sxs-lookup"><span data-stu-id="5c54e-287">An administrator is always an instructor.</span></span> <span data-ttu-id="5c54e-288">Proto je vlastnost `InstructorID` obsažena jako FK pro entitu `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="5c54e-289">Navigační vlastnost má název `Administrator`, ale obsahuje entitu `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="5c54e-290">Otazník (?) v předchozím kódu určuje vlastnost s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="5c54e-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="5c54e-291">Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:</span><span class="sxs-lookup"><span data-stu-id="5c54e-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="5c54e-292">Podle konvence EF Core povoluje kaskádové odstranění pro FKs, která nejsou null a pro relace m:n.</span><span class="sxs-lookup"><span data-stu-id="5c54e-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="5c54e-293">Toto výchozí chování může způsobit cyklické kaskády odstraňování pravidel.</span><span class="sxs-lookup"><span data-stu-id="5c54e-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="5c54e-294">Cyklická kaskádová odstranění pravidel způsobují při přidání migrace výjimku.</span><span class="sxs-lookup"><span data-stu-id="5c54e-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="5c54e-295">Pokud byla například vlastnost `Department.InstructorID` definována jako hodnota, která není null, EF Core by nakonfigurovalo pravidlo kaskádového odstranění.</span><span class="sxs-lookup"><span data-stu-id="5c54e-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="5c54e-296">V takovém případě by se oddělení odstranilo, když se odstraní instruktor jako jeho správce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="5c54e-297">V tomto scénáři by pravidlo omezení mělo smysl.</span><span class="sxs-lookup"><span data-stu-id="5c54e-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="5c54e-298">Následující [rozhraní Fluent API](#fluent-api-alternative-to-attributes) by nastavilo pravidlo omezení a zakáže kaskádové odstranění.</span><span class="sxs-lookup"><span data-stu-id="5c54e-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="5c54e-299">Entita registrace</span><span class="sxs-lookup"><span data-stu-id="5c54e-299">The Enrollment entity</span></span>

<span data-ttu-id="5c54e-300">Záznam zápisu je pro jeden kurz, který přijímá jeden student.</span><span class="sxs-lookup"><span data-stu-id="5c54e-300">An enrollment record is for one course taken by one student.</span></span>

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="5c54e-302">Aktualizujte *modely/zápis. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5c54e-303">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="5c54e-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="5c54e-304">Vlastnosti CK a vlastnosti navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="5c54e-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="5c54e-305">Záznam zápisu je pro jeden kurz, takže existuje vlastnost FK `CourseID` a navigační vlastnost `Course`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="5c54e-306">Záznam zápisu je určen pro jednoho studenta, takže existuje vlastnost FK `StudentID` a navigační vlastnost `Student`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="5c54e-307">Relace m:n</span><span class="sxs-lookup"><span data-stu-id="5c54e-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="5c54e-308">Mezi entitami `Student` a `Course` existuje vztah m:n.</span><span class="sxs-lookup"><span data-stu-id="5c54e-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="5c54e-309">Entita `Enrollment` funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi.</span><span class="sxs-lookup"><span data-stu-id="5c54e-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="5c54e-310">"S datovou částí" znamená, že tabulka `Enrollment` obsahuje další data kromě FKs pro Spojené tabulky (v tomto případě je to PK a `Grade`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="5c54e-311">Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit.</span><span class="sxs-lookup"><span data-stu-id="5c54e-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="5c54e-312">(Tento diagram byl vygenerován pomocí [nástrojů EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6. x.</span><span class="sxs-lookup"><span data-stu-id="5c54e-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="5c54e-313">Vytvoření diagramu není součástí kurzu.)</span><span class="sxs-lookup"><span data-stu-id="5c54e-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

<span data-ttu-id="5c54e-315">Každá čára relace má 1 na jednom konci a hvězdičku (\*) na druhé straně, která indikuje relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="5c54e-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="5c54e-316">Pokud tabulka `Enrollment` neobsahuje informace o třídě, musí obsahovat pouze dvě FKs (`CourseID` a `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="5c54e-317">Tabulka JOIN typu m:n bez datové části se někdy označuje jako čistá spojovací tabulka (PJT).</span><span class="sxs-lookup"><span data-stu-id="5c54e-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="5c54e-318">Entity `Instructor` a `Course` mají relaci n:n pomocí tabulky Pure JOIN.</span><span class="sxs-lookup"><span data-stu-id="5c54e-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="5c54e-319">Poznámka: EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne.</span><span class="sxs-lookup"><span data-stu-id="5c54e-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="5c54e-320">Další informace najdete v tématu [relace m:n v EF Core 2,0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="5c54e-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="5c54e-321">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="5c54e-321">The CourseAssignment entity</span></span>

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="5c54e-323">Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="5c54e-324">Relace m:n pro každého-více kurzů vyžaduje tabulku JOIN a entita pro tuto tabulku JOIN je CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="5c54e-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="5c54e-326">Je běžné pojmenovat entitu JOIN `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="5c54e-327">Například tabulka pro spojení instruktora do kurzů, která používá tento model, by byla `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="5c54e-328">Doporučujeme však použít název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="5c54e-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="5c54e-329">Modely dat začínají jednoduchým a roste.</span><span class="sxs-lookup"><span data-stu-id="5c54e-329">Data models start out simple and grow.</span></span> <span data-ttu-id="5c54e-330">Spojování tabulek bez datové části (PJTs) se často vyvíjí, aby zahrnovalo datovou část.</span><span class="sxs-lookup"><span data-stu-id="5c54e-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="5c54e-331">Když začnete s popisným názvem entity, nemusíte při změně tabulky JOIN měnit název.</span><span class="sxs-lookup"><span data-stu-id="5c54e-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="5c54e-332">V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="5c54e-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="5c54e-333">Například knihy a zákazníci mohou být propojeny s entitou JOIN nazvanou hodnocení.</span><span class="sxs-lookup"><span data-stu-id="5c54e-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="5c54e-334">V případě vztahu n:n mezi jednotlivými jednotlivými selektory se `CourseAssignment` upřednostňuje přes `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="5c54e-335">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="5c54e-335">Composite key</span></span>

<span data-ttu-id="5c54e-336">Dva FKs v `CourseAssignment` (`InstructorID` a `CourseID`) společně identifikují každý řádek tabulky `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="5c54e-337">`CourseAssignment` nevyžaduje vyhrazený PK.</span><span class="sxs-lookup"><span data-stu-id="5c54e-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="5c54e-338">Vlastnosti `InstructorID` a `CourseID` fungují jako složené PK.</span><span class="sxs-lookup"><span data-stu-id="5c54e-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="5c54e-339">Jediným způsobem, jak zadat složené PKs EF Core je s *rozhraním API Fluent*.</span><span class="sxs-lookup"><span data-stu-id="5c54e-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="5c54e-340">V další části se dozvíte, jak nakonfigurovat složený PK.</span><span class="sxs-lookup"><span data-stu-id="5c54e-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="5c54e-341">Složený klíč zajišťuje:</span><span class="sxs-lookup"><span data-stu-id="5c54e-341">The composite key ensures that:</span></span>

* <span data-ttu-id="5c54e-342">Pro jeden kurz je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="5c54e-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="5c54e-343">Pro jednoho instruktora je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="5c54e-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="5c54e-344">Pro stejný instruktor a kurz nejsou povoleny vícenásobné řádky.</span><span class="sxs-lookup"><span data-stu-id="5c54e-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="5c54e-345">Entita spojení `Enrollment` definuje vlastní PK, takže je možné duplikovat toto řazení.</span><span class="sxs-lookup"><span data-stu-id="5c54e-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="5c54e-346">Chcete-li zabránit těmto duplicitám:</span><span class="sxs-lookup"><span data-stu-id="5c54e-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="5c54e-347">Přidejte do polí FK jedinečný index nebo</span><span class="sxs-lookup"><span data-stu-id="5c54e-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="5c54e-348">Nakonfigurujte `Enrollment` s primárním kombinovaným klíčem podobným `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="5c54e-349">Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="5c54e-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="5c54e-350">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="5c54e-350">Update the database context</span></span>

<span data-ttu-id="5c54e-351">Aktualizujte *data/SchoolContext. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="5c54e-352">Předchozí kód přidá nové entity a nakonfiguruje kompozitní PK entity `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="5c54e-353">Alternativa k atributům rozhraní Fluent API</span><span class="sxs-lookup"><span data-stu-id="5c54e-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="5c54e-354">Metoda `OnModelCreating` v předchozím kódu používá ke konfiguraci chování EF Core *rozhraní Fluent API* .</span><span class="sxs-lookup"><span data-stu-id="5c54e-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="5c54e-355">Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="5c54e-356">[Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem rozhraní Fluent API:</span><span class="sxs-lookup"><span data-stu-id="5c54e-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="5c54e-357">V tomto kurzu se rozhraní API Fluent používá jenom pro mapování databáze, které nejde s atributy dělat.</span><span class="sxs-lookup"><span data-stu-id="5c54e-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="5c54e-358">Rozhraní API Fluent ale může určovat většinu pravidel formátování, ověřování a mapování, která se dají provádět s atributy.</span><span class="sxs-lookup"><span data-stu-id="5c54e-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="5c54e-359">Některé atributy, jako je například `MinimumLength`, nelze použít s rozhraním API Fluent.</span><span class="sxs-lookup"><span data-stu-id="5c54e-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="5c54e-360">`MinimumLength` nezmění schéma, použije se pouze ověřovací pravidlo minimální délky.</span><span class="sxs-lookup"><span data-stu-id="5c54e-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="5c54e-361">Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit".</span><span class="sxs-lookup"><span data-stu-id="5c54e-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="5c54e-362">Atributy a rozhraní API Fluent lze kombinovat.</span><span class="sxs-lookup"><span data-stu-id="5c54e-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="5c54e-363">Existují některé konfigurace, které lze provést pouze s rozhraním API Fluent (určením složeného PK).</span><span class="sxs-lookup"><span data-stu-id="5c54e-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="5c54e-364">Existují některé konfigurace, které lze provádět pouze s atributy (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="5c54e-365">Doporučený postup pro použití rozhraní Fluent API nebo atributů:</span><span class="sxs-lookup"><span data-stu-id="5c54e-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="5c54e-366">Vyberte jednu z těchto dvou přístupů.</span><span class="sxs-lookup"><span data-stu-id="5c54e-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="5c54e-367">Používejte vybraný postup konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="5c54e-368">Některé atributy používané v tomto kurzu se používají pro:</span><span class="sxs-lookup"><span data-stu-id="5c54e-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="5c54e-369">Pouze ověření (například `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="5c54e-370">Pouze EF Core konfigurace (například `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="5c54e-371">Konfigurace ověření a EF Core (například `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="5c54e-372">Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="5c54e-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="5c54e-373">Diagram entit</span><span class="sxs-lookup"><span data-stu-id="5c54e-373">Entity diagram</span></span>

<span data-ttu-id="5c54e-374">Následující ilustrace znázorňuje diagram, který nástroje EF Power Tools vytvoří pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="5c54e-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="5c54e-376">Předchozí diagram znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="5c54e-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="5c54e-377">Několik čar relací 1:1 (1 až \*).</span><span class="sxs-lookup"><span data-stu-id="5c54e-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="5c54e-378">Čára relace 1:1 (1 – 0.. 1) mezi entitami `Instructor` a `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="5c54e-379">Čára relace 0.. 1 až n n mezi entitami `Instructor` a `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="5c54e-380">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="5c54e-380">Seed the database</span></span>

<span data-ttu-id="5c54e-381">Aktualizujte kód v *data/DbInitializer. cs*:</span><span class="sxs-lookup"><span data-stu-id="5c54e-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="5c54e-382">Předchozí kód poskytuje počáteční data pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="5c54e-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="5c54e-383">Většina tohoto kódu vytváří nové objekty entit a načítá vzorová data.</span><span class="sxs-lookup"><span data-stu-id="5c54e-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="5c54e-384">Ukázková data se používají k testování.</span><span class="sxs-lookup"><span data-stu-id="5c54e-384">The sample data is used for testing.</span></span> <span data-ttu-id="5c54e-385">Příklady toho, kolik tabulek Join lze dosazením, najdete v tématu `Enrollments` a `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="5c54e-386">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="5c54e-386">Add a migration</span></span>

<span data-ttu-id="5c54e-387">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="5c54e-387">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c54e-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c54e-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c54e-389">V PMC spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="5c54e-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="5c54e-390">Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="5c54e-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="5c54e-391">Pokud se spustí příkaz `database update`, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="5c54e-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="5c54e-392">V další části se dozvíte, co dělat s touto chybou.</span><span class="sxs-lookup"><span data-stu-id="5c54e-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5c54e-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c54e-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5c54e-394">Pokud přidáte migraci a spustíte příkaz `database update`, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="5c54e-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="5c54e-395">V další části se zobrazí informace o tom, jak se vyhnout této chybě.</span><span class="sxs-lookup"><span data-stu-id="5c54e-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="5c54e-396">Použití migrace nebo vyřazení a opětovné vytvoření</span><span class="sxs-lookup"><span data-stu-id="5c54e-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="5c54e-397">Teď, když máte existující databázi, musíte si představit, jak se na ni aplikují změny.</span><span class="sxs-lookup"><span data-stu-id="5c54e-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="5c54e-398">V tomto kurzu se zobrazí dvě alternativy:</span><span class="sxs-lookup"><span data-stu-id="5c54e-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="5c54e-399">[Vyřaďte a znovu vytvořte databázi](#drop).</span><span class="sxs-lookup"><span data-stu-id="5c54e-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="5c54e-400">Tuto část vyberte, pokud používáte SQLite.</span><span class="sxs-lookup"><span data-stu-id="5c54e-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="5c54e-401">[Použijte migraci na stávající databázi](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="5c54e-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="5c54e-402">Pokyny v této části fungují pouze pro SQL Server, **nikoli pro SQLite**.</span><span class="sxs-lookup"><span data-stu-id="5c54e-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="5c54e-403">Jedna volba funguje pro SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5c54e-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="5c54e-404">I když je metoda Apply složitější a časově náročná, jedná se o preferovaný přístup pro produkční prostředí z reálného světa.</span><span class="sxs-lookup"><span data-stu-id="5c54e-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="5c54e-405">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="5c54e-405">Drop and re-create the database</span></span>

<span data-ttu-id="5c54e-406">[Tuto část přeskočte](#apply-the-migration) , pokud používáte SQL Server a chcete provést postup použití migrace v následující části.</span><span class="sxs-lookup"><span data-stu-id="5c54e-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="5c54e-407">Chcete-li vynutit EF Core vytvoření nové databáze, vyřaďte a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="5c54e-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c54e-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c54e-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5c54e-409">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5c54e-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="5c54e-410">Odstraňte složku *migrace* a pak spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5c54e-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5c54e-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c54e-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5c54e-412">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="5c54e-413">Složka projektu obsahuje soubor *ContosoUniversity. csproj* .</span><span class="sxs-lookup"><span data-stu-id="5c54e-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="5c54e-414">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5c54e-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="5c54e-415">Odstraňte složku *migrace* a pak spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5c54e-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="5c54e-416">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c54e-416">Run the app.</span></span> <span data-ttu-id="5c54e-417">Spuštění aplikace spustí metodu `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="5c54e-418">@No__t-0 naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="5c54e-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c54e-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c54e-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c54e-420">Otevřete databázi v SSOX:</span><span class="sxs-lookup"><span data-stu-id="5c54e-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="5c54e-421">Pokud jste dříve otevřeli SSOX, klikněte na tlačítko **aktualizovat** .</span><span class="sxs-lookup"><span data-stu-id="5c54e-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="5c54e-422">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="5c54e-422">Expand the **Tables** node.</span></span> <span data-ttu-id="5c54e-423">Zobrazí se vytvořené tabulky.</span><span class="sxs-lookup"><span data-stu-id="5c54e-423">The created tables are displayed.</span></span>

  ![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="5c54e-425">Projděte si tabulku **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="5c54e-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="5c54e-426">Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data**.</span><span class="sxs-lookup"><span data-stu-id="5c54e-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="5c54e-427">Ověřte, že tabulka **CourseAssignment** obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="5c54e-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5c54e-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c54e-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5c54e-430">Použijte nástroj SQLite k prohlédnutí databáze:</span><span class="sxs-lookup"><span data-stu-id="5c54e-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="5c54e-431">Nové tabulky a sloupce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-431">New tables and columns.</span></span>
* <span data-ttu-id="5c54e-432">Osazená data v tabulkách, například tabulka **CourseAssignment** .</span><span class="sxs-lookup"><span data-stu-id="5c54e-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="5c54e-433">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="5c54e-433">Apply the migration</span></span>

<span data-ttu-id="5c54e-434">Tato část je volitelná.</span><span class="sxs-lookup"><span data-stu-id="5c54e-434">This section is optional.</span></span> <span data-ttu-id="5c54e-435">Tyto kroky fungují jenom pro SQL Server LocalDB a jenom v případě, že jste přeskočili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="5c54e-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="5c54e-436">Pokud jsou migrace spouštěny s existujícími daty, může dojít k omezením FK, která nesplňují stávající data.</span><span class="sxs-lookup"><span data-stu-id="5c54e-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="5c54e-437">S provozními daty je potřeba provést kroky pro migraci stávajících dat.</span><span class="sxs-lookup"><span data-stu-id="5c54e-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="5c54e-438">V této části najdete příklad opravy porušení omezení CK.</span><span class="sxs-lookup"><span data-stu-id="5c54e-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="5c54e-439">Neprovádějte změny kódu bez zálohy.</span><span class="sxs-lookup"><span data-stu-id="5c54e-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="5c54e-440">Neprovádějte změny kódu, pokud jste dokončili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="5c54e-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="5c54e-441">Soubor *{timestamp} _ComplexDataModel. cs* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="5c54e-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="5c54e-442">Předchozí kód přidá do tabulky `Course` nenulovou hodnotu `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="5c54e-443">Databáze z předchozího kurzu obsahuje řádky v `Course`, takže se tabulka nedá aktualizovat pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="5c54e-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="5c54e-444">Chcete-li provést migraci `ComplexDataModel` se stávajícími daty:</span><span class="sxs-lookup"><span data-stu-id="5c54e-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="5c54e-445">Změňte kód tak, aby měl nový sloupec (`DepartmentID`) výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="5c54e-446">Vytvořte falešné oddělení s názvem "Temp", které bude sloužit jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="5c54e-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="5c54e-447">Oprava omezení cizího klíče</span><span class="sxs-lookup"><span data-stu-id="5c54e-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="5c54e-448">Ve třídě migrace `ComplexDataModel` aktualizujte metodu `Up`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="5c54e-449">Otevřete soubor *{timestamp} _ComplexDataModel. cs* .</span><span class="sxs-lookup"><span data-stu-id="5c54e-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="5c54e-450">Odkomentujte řádek kódu, který přidá sloupec `DepartmentID` do tabulky `Course`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="5c54e-451">Přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="5c54e-451">Add the following highlighted code.</span></span> <span data-ttu-id="5c54e-452">Nový kód přejde za blok `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

<span data-ttu-id="5c54e-453">[! Code-CSharp [] (Úvod/Samples/cu30snapshots/5-Complex/migrations/ComplexDataModel. cs? Name = snippet_CreateDefaultValue & zvýraznění = 23-31)]</span><span class="sxs-lookup"><span data-stu-id="5c54e-453">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span></span>

<span data-ttu-id="5c54e-454">S předchozími změnami existující řádky `Course` budou po spuštění metody `ComplexDataModel.Up` souviset s oddělením "dočasného".</span><span class="sxs-lookup"><span data-stu-id="5c54e-454">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="5c54e-455">Pro účely tohoto kurzu je zjednodušený způsob zpracování situace, kterou tady vidíte.</span><span class="sxs-lookup"><span data-stu-id="5c54e-455">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="5c54e-456">Produkční aplikace by:</span><span class="sxs-lookup"><span data-stu-id="5c54e-456">A production app would:</span></span>

* <span data-ttu-id="5c54e-457">Vložte kód nebo skripty pro přidání `Department` řádků a souvisejících řádků `Course` do nových řádků `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-457">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="5c54e-458">Nepoužívejte oddělení "dočasné" nebo výchozí hodnotu pro `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-458">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c54e-459">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c54e-459">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5c54e-460">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5c54e-460">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="5c54e-461">Vzhledem k tomu, že metoda `DbInitializer.Initialize` je navržená tak, aby fungovala pouze s prázdnou databází, pomocí SSOX odstraňte všechny řádky v tabulkách student a Course.</span><span class="sxs-lookup"><span data-stu-id="5c54e-461">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="5c54e-462">(Kaskádová odstranění se postará o tabulku zápisu.)</span><span class="sxs-lookup"><span data-stu-id="5c54e-462">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5c54e-463">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c54e-463">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5c54e-464">Pokud používáte SQL Server LocalDB s Visual Studio Code, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5c54e-464">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="5c54e-465">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c54e-465">Run the app.</span></span> <span data-ttu-id="5c54e-466">Spuštění aplikace spustí metodu `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-466">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="5c54e-467">@No__t-0 naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="5c54e-467">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5c54e-468">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5c54e-468">Next steps</span></span>

<span data-ttu-id="5c54e-469">Následující dva kurzy ukazují, jak číst a aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="5c54e-469">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5c54e-470">[Předchozí kurz](xref:data/ef-rp/migrations)
> [Další kurz](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="5c54e-470">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5c54e-471">Předchozí kurzy pracovaly se základním datovým modelem, který se skládá ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="5c54e-471">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="5c54e-472">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-472">In this tutorial:</span></span>

* <span data-ttu-id="5c54e-473">Přidávají se další entity a vztahy.</span><span class="sxs-lookup"><span data-stu-id="5c54e-473">More entities and relationships are added.</span></span>
* <span data-ttu-id="5c54e-474">Datový model je přizpůsoben zadáním pravidel formátování, ověřování a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="5c54e-474">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="5c54e-475">Třídy entit pro dokončený datový model jsou znázorněny na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="5c54e-475">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="5c54e-477">Pokud narazíte na problémy, které nemůžete vyřešit, stáhněte [dokončenou aplikaci](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="5c54e-477">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="5c54e-478">Přizpůsobení datového modelu pomocí atributů</span><span class="sxs-lookup"><span data-stu-id="5c54e-478">Customize the data model with attributes</span></span>

<span data-ttu-id="5c54e-479">V této části je datový model přizpůsoben pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="5c54e-479">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="5c54e-480">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="5c54e-480">The DataType attribute</span></span>

<span data-ttu-id="5c54e-481">Na stránkách studenta se aktuálně zobrazuje čas pro datum registrace.</span><span class="sxs-lookup"><span data-stu-id="5c54e-481">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="5c54e-482">Pole data obvykle zobrazují pouze datum a čas.</span><span class="sxs-lookup"><span data-stu-id="5c54e-482">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="5c54e-483">Aktualizujte *modely/student. cs* následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-483">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="5c54e-484">Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) Určuje datový typ, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="5c54e-484">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="5c54e-485">V tomto případě by se měla zobrazit pouze datum, nikoli datum a čas.</span><span class="sxs-lookup"><span data-stu-id="5c54e-485">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="5c54e-486">[Výčet DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress atd. Atribut `DataType` může také aplikaci povolit automatické poskytování funkcí specifických pro typ.</span><span class="sxs-lookup"><span data-stu-id="5c54e-486">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="5c54e-487">Například:</span><span class="sxs-lookup"><span data-stu-id="5c54e-487">For example:</span></span>

* <span data-ttu-id="5c54e-488">Propojení `mailto:` se automaticky vytvoří pro `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-488">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="5c54e-489">Selektor data je k dispozici pro `DataType.Date` ve většině prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="5c54e-489">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="5c54e-490">Atribut `DataType` emituje atributy HTML 5 `data-` (vyslovení datové Přerušované čárky), které používají prohlížeče formátu HTML 5.</span><span class="sxs-lookup"><span data-stu-id="5c54e-490">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="5c54e-491">Atributy `DataType` neposkytují ověřování.</span><span class="sxs-lookup"><span data-stu-id="5c54e-491">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="5c54e-492">`DataType.Date` neurčuje formát data, které se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="5c54e-492">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="5c54e-493">Ve výchozím nastavení se pole Datum zobrazuje v závislosti na výchozích formátech na základě objektu [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serveru.</span><span class="sxs-lookup"><span data-stu-id="5c54e-493">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="5c54e-494">Atribut `DisplayFormat` slouží k explicitnímu zadání formátu data:</span><span class="sxs-lookup"><span data-stu-id="5c54e-494">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="5c54e-495">Nastavení `ApplyFormatInEditMode` určuje, že by mělo být formátování použito i pro uživatelské rozhraní úprav.</span><span class="sxs-lookup"><span data-stu-id="5c54e-495">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="5c54e-496">Některá pole by neměla používat `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-496">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="5c54e-497">Například symbol měny by neměl být v textovém poli pro úpravy obvykle zobrazen.</span><span class="sxs-lookup"><span data-stu-id="5c54e-497">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="5c54e-498">Atribut `DisplayFormat` může používat sám sebe.</span><span class="sxs-lookup"><span data-stu-id="5c54e-498">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="5c54e-499">Obecně je vhodné použít atribut `DataType` s atributem `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-499">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="5c54e-500">Atribut `DataType` vyjadřuje sémantiku dat na rozdíl od toho, jak ji vykreslit na obrazovce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-500">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="5c54e-501">Atribut `DataType` poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-501">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="5c54e-502">Prohlížeč může povolit funkce HTML5.</span><span class="sxs-lookup"><span data-stu-id="5c54e-502">The browser can enable HTML5 features.</span></span> <span data-ttu-id="5c54e-503">Například můžete zobrazit ovládací prvek kalendáře, symbol měny odpovídající národním prostředí, e-mailové odkazy, ověřování vstupu na straně klienta atd.</span><span class="sxs-lookup"><span data-stu-id="5c54e-503">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="5c54e-504">Ve výchozím nastavení prohlížeč vykresluje data pomocí správného formátu založeného na národním prostředí.</span><span class="sxs-lookup"><span data-stu-id="5c54e-504">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="5c54e-505">Další informace najdete v dokumentaci k [pomocníka značek \<input >](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5c54e-505">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="5c54e-506">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c54e-506">Run the app.</span></span> <span data-ttu-id="5c54e-507">Přejděte na stránku indexu studentů.</span><span class="sxs-lookup"><span data-stu-id="5c54e-507">Navigate to the Students Index page.</span></span> <span data-ttu-id="5c54e-508">Časy se už nezobrazují.</span><span class="sxs-lookup"><span data-stu-id="5c54e-508">Times are no longer displayed.</span></span> <span data-ttu-id="5c54e-509">Každé zobrazení, které používá model `Student`, zobrazuje datum bez času.</span><span class="sxs-lookup"><span data-stu-id="5c54e-509">Every view that uses the `Student` model displays the date without time.</span></span>

![Stránka indexu studentů zobrazující data bez časů](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="5c54e-511">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="5c54e-511">The StringLength attribute</span></span>

<span data-ttu-id="5c54e-512">Pravidla ověřování dat a chybové zprávy ověřování lze zadat pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="5c54e-512">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="5c54e-513">Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli.</span><span class="sxs-lookup"><span data-stu-id="5c54e-513">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="5c54e-514">Atribut `StringLength` také poskytuje ověřování na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="5c54e-514">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="5c54e-515">Minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="5c54e-515">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="5c54e-516">Aktualizujte model `Student` pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-516">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="5c54e-517">Předchozí kód omezuje názvy na více než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="5c54e-517">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="5c54e-518">Atribut `StringLength` nebrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="5c54e-518">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="5c54e-519">Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) se používá k aplikování omezení na vstup.</span><span class="sxs-lookup"><span data-stu-id="5c54e-519">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="5c54e-520">Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:</span><span class="sxs-lookup"><span data-stu-id="5c54e-520">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="5c54e-521">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="5c54e-521">Run the app:</span></span>

* <span data-ttu-id="5c54e-522">Přejděte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="5c54e-522">Navigate to the Students page.</span></span>
* <span data-ttu-id="5c54e-523">Vyberte **vytvořit novou**a zadejte název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="5c54e-523">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="5c54e-524">Vyberte **vytvořit**, ověřování na straně klienta zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-524">Select **Create**, client-side validation shows an error message.</span></span>

![Stránka indexu studentů zobrazující chyby délky řetězce](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="5c54e-526">V **Průzkumník objektů systému SQL Server** (SSOX) otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .</span><span class="sxs-lookup"><span data-stu-id="5c54e-526">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů v SSOX před migracemi](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="5c54e-528">Na předchozím obrázku je znázorněno schéma pro tabulku `Student`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-528">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="5c54e-529">Pole název mají typ `nvarchar(MAX)`, protože migrace nebyly v databázi spuštěny.</span><span class="sxs-lookup"><span data-stu-id="5c54e-529">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="5c54e-530">Když se v tomto kurzu spustí migrace později, pole název se změní na `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-530">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="5c54e-531">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="5c54e-531">The Column attribute</span></span>

<span data-ttu-id="5c54e-532">Atributy mohou řídit způsob, jakým jsou třídy a vlastnosti mapovány na databázi.</span><span class="sxs-lookup"><span data-stu-id="5c54e-532">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="5c54e-533">V této části se k mapování názvu vlastnosti `FirstMidName` na FirstName v databázi používá atribut `Column`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-533">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="5c54e-534">Při vytvoření databáze se názvy vlastností v modelu používají pro názvy sloupců (s výjimkou případů, kdy se používá atribut `Column`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-534">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="5c54e-535">Model `Student` používá pro pole první název `FirstMidName`, protože pole může obsahovat také prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="5c54e-535">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="5c54e-536">Aktualizujte soubor *student.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-536">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="5c54e-537">S předchozí změnou `Student.FirstMidName` v aplikaci mapuje na sloupec `FirstName` tabulky `Student`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-537">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="5c54e-538">Přidání atributu `Column` změní model zálohování `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-538">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="5c54e-539">Model zálohování `SchoolContext` se již neshoduje s databází.</span><span class="sxs-lookup"><span data-stu-id="5c54e-539">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="5c54e-540">Pokud je aplikace spuštěná před použitím migrace, vygeneruje se tato výjimka:</span><span class="sxs-lookup"><span data-stu-id="5c54e-540">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="5c54e-541">Aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="5c54e-541">To update the DB:</span></span>

* <span data-ttu-id="5c54e-542">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="5c54e-542">Build the project.</span></span>
* <span data-ttu-id="5c54e-543">Otevřete okno příkazového řádku ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-543">Open a command window in the project folder.</span></span> <span data-ttu-id="5c54e-544">Zadáním následujících příkazů vytvořte novou migraci a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="5c54e-544">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c54e-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c54e-545">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5c54e-546">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c54e-546">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="5c54e-547">Příkaz `migrations add ColumnFirstName` generuje následující varovnou zprávu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-547">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="5c54e-548">Upozornění je vygenerováno, protože pole s názvem jsou nyní omezena na 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="5c54e-548">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="5c54e-549">Pokud má název v databázi více než 50 znaků, ztratí se 51 na poslední znak.</span><span class="sxs-lookup"><span data-stu-id="5c54e-549">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="5c54e-550">Otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c54e-550">Test the app.</span></span>

<span data-ttu-id="5c54e-551">Otevřete tabulku student v SSOX:</span><span class="sxs-lookup"><span data-stu-id="5c54e-551">Open the Student table in SSOX:</span></span>

![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="5c54e-553">Před použitím migrace byly sloupce názvu typu [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="5c54e-553">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="5c54e-554">Sloupce s názvem jsou nyní `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-554">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="5c54e-555">Název sloupce se změnil z `FirstMidName` na `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-555">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="5c54e-556">V následující části sestavení aplikace v některých fázích generuje chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="5c54e-556">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="5c54e-557">Pokyny určují, kdy se má aplikace sestavit.</span><span class="sxs-lookup"><span data-stu-id="5c54e-557">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="5c54e-558">Aktualizace entity studenta</span><span class="sxs-lookup"><span data-stu-id="5c54e-558">Student entity update</span></span>

![Entita studenta](complex-data-model/_static/student-entity.png)

<span data-ttu-id="5c54e-560">Aktualizujte *modely/student. cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-560">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="5c54e-561">Požadovaný atribut</span><span class="sxs-lookup"><span data-stu-id="5c54e-561">The Required attribute</span></span>

<span data-ttu-id="5c54e-562">Atribut `Required` zpřístupňuje vlastnosti název povinná pole.</span><span class="sxs-lookup"><span data-stu-id="5c54e-562">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="5c54e-563">Atribut `Required` není potřebný pro typy, které nejsou null, jako jsou například typy hodnot (`DateTime`, `int`, `double` atd.).</span><span class="sxs-lookup"><span data-stu-id="5c54e-563">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="5c54e-564">Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="5c54e-564">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="5c54e-565">Atribut `Required` může být nahrazen parametrem minimální délky v atributu `StringLength`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-565">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="5c54e-566">Atribut zobrazení</span><span class="sxs-lookup"><span data-stu-id="5c54e-566">The Display attribute</span></span>

<span data-ttu-id="5c54e-567">Atribut `Display` určuje, že titulek pro textová pole by měl být "jméno", "příjmení", "celé jméno" a "datum zápisu".</span><span class="sxs-lookup"><span data-stu-id="5c54e-567">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="5c54e-568">Výchozí titulky neobsahovaly místo dělení slov, například "LastName".</span><span class="sxs-lookup"><span data-stu-id="5c54e-568">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="5c54e-569">Vypočítaná vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="5c54e-569">The FullName calculated property</span></span>

<span data-ttu-id="5c54e-570">`FullName` je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="5c54e-570">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="5c54e-571">`FullName` nelze nastavit, má pouze přistupující objekt get.</span><span class="sxs-lookup"><span data-stu-id="5c54e-571">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="5c54e-572">V databázi se nevytvoří žádný sloupec `FullName`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-572">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="5c54e-573">Vytvořit entitu instruktora</span><span class="sxs-lookup"><span data-stu-id="5c54e-573">Create the Instructor Entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="5c54e-575">Vytvořte *modely/Instructor. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-575">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="5c54e-576">Více atributů může být na jednom řádku.</span><span class="sxs-lookup"><span data-stu-id="5c54e-576">Multiple attributes can be on one line.</span></span> <span data-ttu-id="5c54e-577">Atributy `HireDate` lze zapsat takto:</span><span class="sxs-lookup"><span data-stu-id="5c54e-577">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="5c54e-578">Navigační vlastnosti CourseAssignments a OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="5c54e-578">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="5c54e-579">Vlastnosti `CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5c54e-579">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="5c54e-580">Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-580">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="5c54e-581">Pokud navigační vlastnost obsahuje více entit:</span><span class="sxs-lookup"><span data-stu-id="5c54e-581">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="5c54e-582">Musí se jednat o typ seznamu, kde je možné přidávat, odstraňovat a aktualizovat položky.</span><span class="sxs-lookup"><span data-stu-id="5c54e-582">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="5c54e-583">Mezi typy vlastností navigace patří:</span><span class="sxs-lookup"><span data-stu-id="5c54e-583">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="5c54e-584">Pokud je zadána hodnota `ICollection<T>`, EF Core ve výchozím nastavení vytvoří kolekci `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-584">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="5c54e-585">Entita `CourseAssignment` je vysvětlena v části relace m:n.</span><span class="sxs-lookup"><span data-stu-id="5c54e-585">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="5c54e-586">Firemní pravidla společnosti Contoso vysokých škol, že vyučující může mít maximálně jednu kancelář.</span><span class="sxs-lookup"><span data-stu-id="5c54e-586">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="5c54e-587">Vlastnost `OfficeAssignment` obsahuje jednu entitu `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-587">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="5c54e-588">`OfficeAssignment` je null, pokud není přiřazen žádný systém Office.</span><span class="sxs-lookup"><span data-stu-id="5c54e-588">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="5c54e-589">Vytvoření entity OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="5c54e-589">Create the OfficeAssignment entity</span></span>

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="5c54e-591">Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-591">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="5c54e-592">Klíčový atribut</span><span class="sxs-lookup"><span data-stu-id="5c54e-592">The Key attribute</span></span>

<span data-ttu-id="5c54e-593">Atribut `[Key]` slouží k identifikaci vlastnosti jako primárního klíče (PK), pokud je název vlastnosti něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="5c54e-593">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="5c54e-594">Mezi entitami `Instructor` a `OfficeAssignment` existuje vztah 1:1 nebo 1:1.</span><span class="sxs-lookup"><span data-stu-id="5c54e-594">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="5c54e-595">Přiřazení kanceláře existuje jenom ve vztahu k instruktorovi, ke kterému je přiřazený.</span><span class="sxs-lookup"><span data-stu-id="5c54e-595">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="5c54e-596">@No__t-0 PK je také jeho cizí klíč (FK) k entitě `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-596">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="5c54e-597">EF Core nemůže automaticky rozpoznat `InstructorID` jako PK v `OfficeAssignment` z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="5c54e-597">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="5c54e-598">@no__t – 0 nedodržuje konvence pojmenování ID nebo classnameID.</span><span class="sxs-lookup"><span data-stu-id="5c54e-598">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="5c54e-599">Proto se atribut `Key` používá k identifikaci `InstructorID` jako PK:</span><span class="sxs-lookup"><span data-stu-id="5c54e-599">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="5c54e-600">Ve výchozím nastavení EF Core považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-600">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="5c54e-601">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="5c54e-601">The Instructor navigation property</span></span>

<span data-ttu-id="5c54e-602">Navigační vlastnost `OfficeAssignment` pro entitu `Instructor` má hodnotu null, protože:</span><span class="sxs-lookup"><span data-stu-id="5c54e-602">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="5c54e-603">Typy odkazů (například třídy mohou mít hodnotu null).</span><span class="sxs-lookup"><span data-stu-id="5c54e-603">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="5c54e-604">Instruktor nemusí mít přiřazení kanceláře.</span><span class="sxs-lookup"><span data-stu-id="5c54e-604">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="5c54e-605">Entita `OfficeAssignment` má vlastnost navigace, která není @no__t null, protože:</span><span class="sxs-lookup"><span data-stu-id="5c54e-605">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="5c54e-606">`InstructorID` je nepovoluje hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="5c54e-606">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="5c54e-607">Přiřazení kanceláře nemůže existovat bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="5c54e-607">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="5c54e-608">Pokud má entita `Instructor` související entitu `OfficeAssignment`, má Každá entita odkaz na druhou vlastnost v její navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5c54e-608">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="5c54e-609">Atribut `[Required]` lze použít pro navigační vlastnost `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-609">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="5c54e-610">Předchozí kód určuje, že musí existovat související instruktor.</span><span class="sxs-lookup"><span data-stu-id="5c54e-610">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="5c54e-611">Předchozí kód není potřebný, protože cizí klíč `InstructorID` (což je také PK) je nepovoluje hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="5c54e-611">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="5c54e-612">Úprava entity kurzu</span><span class="sxs-lookup"><span data-stu-id="5c54e-612">Modify the Course Entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="5c54e-614">Aktualizujte *modely/Course. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5c54e-614">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="5c54e-615">Entita `Course` má vlastnost cizího klíče (FK) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-615">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="5c54e-616">`DepartmentID` odkazuje na související entitu `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-616">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="5c54e-617">Entita `Course` má vlastnost navigace `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-617">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="5c54e-618">EF Core nevyžaduje vlastnost FK pro datový model, pokud model má vlastnost navigace pro související entitu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-618">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="5c54e-619">EF Core v databázi automaticky vytvoří FKs bez ohledu na to, kde jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="5c54e-619">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="5c54e-620">EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automatické vytváření FKs.</span><span class="sxs-lookup"><span data-stu-id="5c54e-620">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="5c54e-621">Pokud se v datovém modelu nachází FK, může být aktualizace jednodušší a efektivnější.</span><span class="sxs-lookup"><span data-stu-id="5c54e-621">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="5c54e-622">*Představte* si například model, ve kterém není k dispozici vlastnost FK `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-622">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="5c54e-623">Když se načte entita kurzu, která se upraví:</span><span class="sxs-lookup"><span data-stu-id="5c54e-623">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="5c54e-624">Entita `Department` má hodnotu null, pokud není explicitně načtena.</span><span class="sxs-lookup"><span data-stu-id="5c54e-624">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="5c54e-625">Chcete-li aktualizovat entitu kurzu, musí být nejprve načtena entita `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-625">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="5c54e-626">Pokud je vlastnost FK `DepartmentID` obsažena v datovém modelu, není nutné před aktualizací načíst entitu `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-626">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="5c54e-627">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="5c54e-627">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="5c54e-628">Atribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` určuje, že se má v rámci aplikace poskytnout PK místo vygenerované databází.</span><span class="sxs-lookup"><span data-stu-id="5c54e-628">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="5c54e-629">Ve výchozím nastavení EF Core předpokládá, že jsou hodnoty PK generovány databází.</span><span class="sxs-lookup"><span data-stu-id="5c54e-629">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="5c54e-630">DATABÁZE vygenerovala hodnoty PK většinou nejlepšího přístupu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-630">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="5c54e-631">Pro entity `Course` určuje uživatel PK.</span><span class="sxs-lookup"><span data-stu-id="5c54e-631">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="5c54e-632">Například číslo kurzu, jako je například série 1000 pro matematické oddělení, série 2000 pro národní oddělení.</span><span class="sxs-lookup"><span data-stu-id="5c54e-632">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="5c54e-633">Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="5c54e-633">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="5c54e-634">DATABÁZE může například automaticky vygenerovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován.</span><span class="sxs-lookup"><span data-stu-id="5c54e-634">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="5c54e-635">Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="5c54e-635">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5c54e-636">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="5c54e-636">Foreign key and navigation properties</span></span>

<span data-ttu-id="5c54e-637">Vlastnosti cizího klíče (FK) a navigační vlastnosti v entitě `Course` odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="5c54e-637">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="5c54e-638">Kurz se přiřadí jednomu oddělení, takže existuje `DepartmentID` FK a navigační vlastnost `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-638">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="5c54e-639">V rámci kurzu může být zaregistrované několik studentů, takže navigační vlastnost `Enrollments` je kolekce:</span><span class="sxs-lookup"><span data-stu-id="5c54e-639">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="5c54e-640">Kurz může být výukou více instruktorů, takže navigační vlastnost `CourseAssignments` je kolekce:</span><span class="sxs-lookup"><span data-stu-id="5c54e-640">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="5c54e-641">`CourseAssignment` je vysvětleno [později](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="5c54e-641">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="5c54e-642">Vytvořit entitu oddělení</span><span class="sxs-lookup"><span data-stu-id="5c54e-642">Create the Department entity</span></span>

![Entita oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="5c54e-644">Vytvořte *modely/oddělení. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-644">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="5c54e-645">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="5c54e-645">The Column attribute</span></span>

<span data-ttu-id="5c54e-646">Dřív se použil atribut `Column` pro změnu mapování názvu sloupce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-646">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="5c54e-647">V kódu pro entitu `Department` se pro změnu mapování datových typů SQL používá atribut `Column`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-647">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="5c54e-648">Sloupec `Budget` je definován pomocí SQL Server peněžního typu v databázi:</span><span class="sxs-lookup"><span data-stu-id="5c54e-648">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="5c54e-649">Mapování sloupce není obecně vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="5c54e-649">Column mapping is generally not required.</span></span> <span data-ttu-id="5c54e-650">EF Core všeobecně vybere vhodný SQL Server datový typ založený na typu CLR pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5c54e-650">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="5c54e-651">Typ CLR `decimal` se mapuje na typ SQL Server `decimal`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-651">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="5c54e-652">`Budget` je pro měnu a datový typ Money je pro měnu vhodný.</span><span class="sxs-lookup"><span data-stu-id="5c54e-652">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5c54e-653">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="5c54e-653">Foreign key and navigation properties</span></span>

<span data-ttu-id="5c54e-654">Vlastnosti FK a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="5c54e-654">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="5c54e-655">Oddělení může nebo nemusí mít správce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-655">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="5c54e-656">Správce je vždy instruktor.</span><span class="sxs-lookup"><span data-stu-id="5c54e-656">An administrator is always an instructor.</span></span> <span data-ttu-id="5c54e-657">Proto je vlastnost `InstructorID` obsažena jako FK pro entitu `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-657">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="5c54e-658">Navigační vlastnost má název `Administrator`, ale obsahuje entitu `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-658">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="5c54e-659">Otazník (?) v předchozím kódu určuje vlastnost s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="5c54e-659">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="5c54e-660">Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:</span><span class="sxs-lookup"><span data-stu-id="5c54e-660">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="5c54e-661">Poznámka: podle konvence EF Core povoluje kaskádové odstranění pro FKs, která nejsou null a pro relace m:n.</span><span class="sxs-lookup"><span data-stu-id="5c54e-661">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="5c54e-662">Kaskádové odstranění může mít za následek cyklické Kaskádové odstraňování pravidel.</span><span class="sxs-lookup"><span data-stu-id="5c54e-662">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="5c54e-663">Cyklická kaskádová odstranění pravidel způsobí výjimku při přidání migrace.</span><span class="sxs-lookup"><span data-stu-id="5c54e-663">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="5c54e-664">Pokud je například vlastnost `Department.InstructorID` definována jako nenulová hodnota:</span><span class="sxs-lookup"><span data-stu-id="5c54e-664">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="5c54e-665">EF Core konfiguruje pravidlo kaskádového odstranění, které odstraní oddělení při odstranění instruktora.</span><span class="sxs-lookup"><span data-stu-id="5c54e-665">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="5c54e-666">Odstranění oddělení, když se odstraní instruktor, není zamýšleným chováním.</span><span class="sxs-lookup"><span data-stu-id="5c54e-666">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="5c54e-667">Následující [rozhraní Fluent API](#fluent-api-alternative-to-attributes) by místo kaskádového nastavilo pravidlo omezení.</span><span class="sxs-lookup"><span data-stu-id="5c54e-667">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="5c54e-668">Předchozí kód zakáže kaskádové odstranění na základě vztahu oddělení a instruktor.</span><span class="sxs-lookup"><span data-stu-id="5c54e-668">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="5c54e-669">Aktualizace entity registrace</span><span class="sxs-lookup"><span data-stu-id="5c54e-669">Update the Enrollment entity</span></span>

<span data-ttu-id="5c54e-670">Záznam zápisu je pro jeden kurz, který přijímá jeden student.</span><span class="sxs-lookup"><span data-stu-id="5c54e-670">An enrollment record is for one course taken by one student.</span></span>

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="5c54e-672">Aktualizujte *modely/zápis. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-672">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5c54e-673">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="5c54e-673">Foreign key and navigation properties</span></span>

<span data-ttu-id="5c54e-674">Vlastnosti CK a vlastnosti navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="5c54e-674">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="5c54e-675">Záznam zápisu je pro jeden kurz, takže existuje vlastnost FK `CourseID` a navigační vlastnost `Course`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-675">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="5c54e-676">Záznam zápisu je určen pro jednoho studenta, takže existuje vlastnost FK `StudentID` a navigační vlastnost `Student`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-676">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="5c54e-677">Relace m:n</span><span class="sxs-lookup"><span data-stu-id="5c54e-677">Many-to-Many Relationships</span></span>

<span data-ttu-id="5c54e-678">Mezi entitami `Student` a `Course` existuje vztah m:n.</span><span class="sxs-lookup"><span data-stu-id="5c54e-678">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="5c54e-679">Entita `Enrollment` funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi.</span><span class="sxs-lookup"><span data-stu-id="5c54e-679">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="5c54e-680">"S datovou částí" znamená, že tabulka `Enrollment` obsahuje další data kromě FKs pro Spojené tabulky (v tomto případě je to PK a `Grade`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-680">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="5c54e-681">Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit.</span><span class="sxs-lookup"><span data-stu-id="5c54e-681">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="5c54e-682">(Tento diagram byl vygenerován pomocí [nástrojů EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6. x.</span><span class="sxs-lookup"><span data-stu-id="5c54e-682">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="5c54e-683">Vytvoření diagramu není součástí kurzu.)</span><span class="sxs-lookup"><span data-stu-id="5c54e-683">Creating the diagram isn't part of the tutorial.)</span></span>

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

<span data-ttu-id="5c54e-685">Každá čára relace má 1 na jednom konci a hvězdičku (\*) na druhé straně, která indikuje relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="5c54e-685">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="5c54e-686">Pokud tabulka `Enrollment` neobsahuje informace o třídě, musí obsahovat pouze dvě FKs (`CourseID` a `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-686">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="5c54e-687">Tabulka JOIN typu m:n bez datové části se někdy označuje jako čistá spojovací tabulka (PJT).</span><span class="sxs-lookup"><span data-stu-id="5c54e-687">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="5c54e-688">Entity `Instructor` a `Course` mají relaci n:n pomocí tabulky Pure JOIN.</span><span class="sxs-lookup"><span data-stu-id="5c54e-688">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="5c54e-689">Poznámka: EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne.</span><span class="sxs-lookup"><span data-stu-id="5c54e-689">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="5c54e-690">Další informace najdete v tématu [relace m:n v EF Core 2,0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="5c54e-690">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="5c54e-691">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="5c54e-691">The CourseAssignment entity</span></span>

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="5c54e-693">Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c54e-693">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="5c54e-694">Instruktory do kurzů</span><span class="sxs-lookup"><span data-stu-id="5c54e-694">Instructor-to-Courses</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="5c54e-696">Vztah n:n od instruktora do více kurzů:</span><span class="sxs-lookup"><span data-stu-id="5c54e-696">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="5c54e-697">Vyžaduje tabulku JOIN, která musí být reprezentována sadou entit.</span><span class="sxs-lookup"><span data-stu-id="5c54e-697">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="5c54e-698">Je čistá spojovací tabulka (tabulka bez datové části).</span><span class="sxs-lookup"><span data-stu-id="5c54e-698">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="5c54e-699">Je běžné pojmenovat entitu JOIN `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-699">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="5c54e-700">Například tabulka s instruktorem do kurzů, která používá tento model, je `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-700">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="5c54e-701">Doporučujeme však použít název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="5c54e-701">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="5c54e-702">Modely dat začínají jednoduchým a roste.</span><span class="sxs-lookup"><span data-stu-id="5c54e-702">Data models start out simple and grow.</span></span> <span data-ttu-id="5c54e-703">Spojení bez datové části (PJTs) se často rozvíjejí, aby zahrnovalo datovou část.</span><span class="sxs-lookup"><span data-stu-id="5c54e-703">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="5c54e-704">Když začnete s popisným názvem entity, nemusíte při změně tabulky JOIN měnit název.</span><span class="sxs-lookup"><span data-stu-id="5c54e-704">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="5c54e-705">V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="5c54e-705">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="5c54e-706">Například knihy a zákazníci mohou být propojeny s entitou JOIN nazvanou hodnocení.</span><span class="sxs-lookup"><span data-stu-id="5c54e-706">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="5c54e-707">V případě vztahu n:n mezi jednotlivými jednotlivými selektory se `CourseAssignment` upřednostňuje přes `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-707">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="5c54e-708">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="5c54e-708">Composite key</span></span>

<span data-ttu-id="5c54e-709">FKs nemohou mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="5c54e-709">FKs are not nullable.</span></span> <span data-ttu-id="5c54e-710">Dva FKs v `CourseAssignment` (`InstructorID` a `CourseID`) společně identifikují každý řádek tabulky `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-710">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="5c54e-711">`CourseAssignment` nevyžaduje vyhrazený PK.</span><span class="sxs-lookup"><span data-stu-id="5c54e-711">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="5c54e-712">Vlastnosti `InstructorID` a `CourseID` fungují jako složené PK.</span><span class="sxs-lookup"><span data-stu-id="5c54e-712">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="5c54e-713">Jediným způsobem, jak zadat složené PKs EF Core je s *rozhraním API Fluent*.</span><span class="sxs-lookup"><span data-stu-id="5c54e-713">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="5c54e-714">V další části se dozvíte, jak nakonfigurovat složený PK.</span><span class="sxs-lookup"><span data-stu-id="5c54e-714">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="5c54e-715">Složený klíč zajišťuje:</span><span class="sxs-lookup"><span data-stu-id="5c54e-715">The composite key ensures:</span></span>

* <span data-ttu-id="5c54e-716">Pro jeden kurz je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="5c54e-716">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="5c54e-717">Pro jednoho instruktora je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="5c54e-717">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="5c54e-718">Více řádků pro stejný instruktor a kurz není povoleno.</span><span class="sxs-lookup"><span data-stu-id="5c54e-718">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="5c54e-719">Entita spojení `Enrollment` definuje vlastní PK, takže je možné duplikovat toto řazení.</span><span class="sxs-lookup"><span data-stu-id="5c54e-719">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="5c54e-720">Chcete-li zabránit těmto duplicitám:</span><span class="sxs-lookup"><span data-stu-id="5c54e-720">To prevent such duplicates:</span></span>

* <span data-ttu-id="5c54e-721">Přidejte do polí FK jedinečný index nebo</span><span class="sxs-lookup"><span data-stu-id="5c54e-721">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="5c54e-722">Nakonfigurujte `Enrollment` s primárním kombinovaným klíčem podobným `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-722">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="5c54e-723">Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="5c54e-723">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="5c54e-724">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="5c54e-724">Update the DB context</span></span>

<span data-ttu-id="5c54e-725">Do *data/SchoolContext. cs*přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="5c54e-725">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="5c54e-726">Předchozí kód přidá nové entity a nakonfiguruje kompozitní PK entity `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-726">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="5c54e-727">Alternativa k atributům rozhraní Fluent API</span><span class="sxs-lookup"><span data-stu-id="5c54e-727">Fluent API alternative to attributes</span></span>

<span data-ttu-id="5c54e-728">Metoda `OnModelCreating` v předchozím kódu používá ke konfiguraci chování EF Core *rozhraní Fluent API* .</span><span class="sxs-lookup"><span data-stu-id="5c54e-728">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="5c54e-729">Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-729">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="5c54e-730">[Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem rozhraní Fluent API:</span><span class="sxs-lookup"><span data-stu-id="5c54e-730">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="5c54e-731">V tomto kurzu se rozhraní API Fluent používá jenom pro mapování DB, které nejde s atributy dělat.</span><span class="sxs-lookup"><span data-stu-id="5c54e-731">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="5c54e-732">Rozhraní API Fluent ale může určovat většinu pravidel formátování, ověřování a mapování, která se dají provádět s atributy.</span><span class="sxs-lookup"><span data-stu-id="5c54e-732">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="5c54e-733">Některé atributy, jako je například `MinimumLength`, nelze použít s rozhraním API Fluent.</span><span class="sxs-lookup"><span data-stu-id="5c54e-733">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="5c54e-734">`MinimumLength` nezmění schéma, použije se pouze ověřovací pravidlo minimální délky.</span><span class="sxs-lookup"><span data-stu-id="5c54e-734">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="5c54e-735">Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit".</span><span class="sxs-lookup"><span data-stu-id="5c54e-735">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="5c54e-736">Atributy a rozhraní API Fluent lze kombinovat.</span><span class="sxs-lookup"><span data-stu-id="5c54e-736">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="5c54e-737">Existují některé konfigurace, které lze provést pouze s rozhraním API Fluent (určením složeného PK).</span><span class="sxs-lookup"><span data-stu-id="5c54e-737">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="5c54e-738">Existují některé konfigurace, které lze provádět pouze s atributy (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-738">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="5c54e-739">Doporučený postup pro použití rozhraní Fluent API nebo atributů:</span><span class="sxs-lookup"><span data-stu-id="5c54e-739">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="5c54e-740">Vyberte jednu z těchto dvou přístupů.</span><span class="sxs-lookup"><span data-stu-id="5c54e-740">Choose one of these two approaches.</span></span>
* <span data-ttu-id="5c54e-741">Používejte vybraný postup konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="5c54e-741">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="5c54e-742">Některé atributy použité v tomto kurzu se používají pro:</span><span class="sxs-lookup"><span data-stu-id="5c54e-742">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="5c54e-743">Pouze ověření (například `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-743">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="5c54e-744">Pouze EF Core konfigurace (například `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-744">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="5c54e-745">Konfigurace ověření a EF Core (například `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="5c54e-745">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="5c54e-746">Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="5c54e-746">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="5c54e-747">Diagram entit znázorňující vztahy</span><span class="sxs-lookup"><span data-stu-id="5c54e-747">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="5c54e-748">Následující ilustrace znázorňuje diagram, který nástroje EF Power Tools vytvoří pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="5c54e-748">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="5c54e-750">Předchozí diagram znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="5c54e-750">The preceding diagram shows:</span></span>

* <span data-ttu-id="5c54e-751">Několik čar relací 1:1 (1 až \*).</span><span class="sxs-lookup"><span data-stu-id="5c54e-751">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="5c54e-752">Čára relace 1:1 (1 – 0.. 1) mezi entitami `Instructor` a `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-752">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="5c54e-753">Čára relace 0.. 1 až n n mezi entitami `Instructor` a `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-753">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="5c54e-754">Osazení databáze pomocí testovacích dat</span><span class="sxs-lookup"><span data-stu-id="5c54e-754">Seed the DB with Test Data</span></span>

<span data-ttu-id="5c54e-755">Aktualizujte kód v *data/DbInitializer. cs*:</span><span class="sxs-lookup"><span data-stu-id="5c54e-755">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="5c54e-756">Předchozí kód poskytuje počáteční data pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="5c54e-756">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="5c54e-757">Většina tohoto kódu vytváří nové objekty entit a načítá vzorová data.</span><span class="sxs-lookup"><span data-stu-id="5c54e-757">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="5c54e-758">Ukázková data se používají k testování.</span><span class="sxs-lookup"><span data-stu-id="5c54e-758">The sample data is used for testing.</span></span> <span data-ttu-id="5c54e-759">Příklady toho, kolik tabulek Join lze dosazením, najdete v tématu `Enrollments` a `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-759">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="5c54e-760">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="5c54e-760">Add a migration</span></span>

<span data-ttu-id="5c54e-761">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="5c54e-761">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c54e-762">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c54e-762">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5c54e-763">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c54e-763">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="5c54e-764">Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="5c54e-764">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="5c54e-765">Pokud se spustí příkaz `database update`, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="5c54e-765">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="5c54e-766">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="5c54e-766">Apply the migration</span></span>

<span data-ttu-id="5c54e-767">Teď, když máte existující databázi, musíte si představit, jak v nich použít budoucí změny.</span><span class="sxs-lookup"><span data-stu-id="5c54e-767">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="5c54e-768">V tomto kurzu se dozvíte dva přístupy:</span><span class="sxs-lookup"><span data-stu-id="5c54e-768">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="5c54e-769">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="5c54e-769">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="5c54e-770">[Použijte migraci na stávající databázi](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="5c54e-770">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="5c54e-771">I když je tato metoda složitější a časově náročná, jedná se o preferovaný přístup pro produkční prostředí z reálného světa.</span><span class="sxs-lookup"><span data-stu-id="5c54e-771">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="5c54e-772">**Poznámka**: Toto je volitelný oddíl tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-772">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="5c54e-773">Můžete provést kroky odkládací a znovu vytvořit a tuto část přeskočit.</span><span class="sxs-lookup"><span data-stu-id="5c54e-773">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="5c54e-774">Pokud chcete postupovat podle kroků v této části, neprovádějte kroky odkládací a znovu vytvořit.</span><span class="sxs-lookup"><span data-stu-id="5c54e-774">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="5c54e-775">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="5c54e-775">Drop and re-create the database</span></span>

<span data-ttu-id="5c54e-776">Kód v aktualizovaném `DbInitializer` přidá počáteční data pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="5c54e-776">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="5c54e-777">Pokud chcete vynutit EF Core vytvoření nové databáze, vyřaďte a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="5c54e-777">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c54e-778">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c54e-778">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c54e-779">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5c54e-779">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="5c54e-780">Pokud chcete získat informace o nápovědě, spusťte `Get-Help about_EntityFrameworkCore` z PMC.</span><span class="sxs-lookup"><span data-stu-id="5c54e-780">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5c54e-781">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c54e-781">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5c54e-782">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-782">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="5c54e-783">Složka projektu obsahuje soubor *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="5c54e-783">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="5c54e-784">V příkazovém okně zadejte následující:</span><span class="sxs-lookup"><span data-stu-id="5c54e-784">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="5c54e-785">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c54e-785">Run the app.</span></span> <span data-ttu-id="5c54e-786">Spuštění aplikace spustí metodu `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-786">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="5c54e-787">@No__t-0 naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="5c54e-787">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="5c54e-788">Otevřete databázi v SSOX:</span><span class="sxs-lookup"><span data-stu-id="5c54e-788">Open the DB in SSOX:</span></span>

* <span data-ttu-id="5c54e-789">Pokud jste dříve otevřeli SSOX, klikněte na tlačítko **aktualizovat** .</span><span class="sxs-lookup"><span data-stu-id="5c54e-789">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="5c54e-790">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="5c54e-790">Expand the **Tables** node.</span></span> <span data-ttu-id="5c54e-791">Zobrazí se vytvořené tabulky.</span><span class="sxs-lookup"><span data-stu-id="5c54e-791">The created tables are displayed.</span></span>

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="5c54e-793">Projděte si tabulku **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="5c54e-793">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="5c54e-794">Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data**.</span><span class="sxs-lookup"><span data-stu-id="5c54e-794">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="5c54e-795">Ověřte, že tabulka **CourseAssignment** obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="5c54e-795">Verify the **CourseAssignment** table contains data.</span></span>

![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="5c54e-797">Použít migraci na existující databázi</span><span class="sxs-lookup"><span data-stu-id="5c54e-797">Apply the migration to the existing database</span></span>

<span data-ttu-id="5c54e-798">Tato část je volitelná.</span><span class="sxs-lookup"><span data-stu-id="5c54e-798">This section is optional.</span></span> <span data-ttu-id="5c54e-799">Tyto kroky fungují pouze v případě, že jste přeskočili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="5c54e-799">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="5c54e-800">Pokud jsou migrace spouštěny s existujícími daty, může dojít k omezením FK, která nesplňují stávající data.</span><span class="sxs-lookup"><span data-stu-id="5c54e-800">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="5c54e-801">S provozními daty je potřeba provést kroky pro migraci stávajících dat.</span><span class="sxs-lookup"><span data-stu-id="5c54e-801">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="5c54e-802">V této části najdete příklad opravy porušení omezení CK.</span><span class="sxs-lookup"><span data-stu-id="5c54e-802">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="5c54e-803">Neprovádějte změny kódu bez zálohy.</span><span class="sxs-lookup"><span data-stu-id="5c54e-803">Don't make these code changes without a backup.</span></span> <span data-ttu-id="5c54e-804">Neprovádějte změny kódu, pokud jste dokončili předchozí oddíl a aktualizovali databázi.</span><span class="sxs-lookup"><span data-stu-id="5c54e-804">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="5c54e-805">Soubor *{timestamp} _ComplexDataModel. cs* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="5c54e-805">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="5c54e-806">Předchozí kód přidá do tabulky `Course` nenulovou hodnotu `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-806">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="5c54e-807">DATABÁZE z předchozího kurzu obsahuje řádky v `Course`, takže se tabulka nedá aktualizovat pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="5c54e-807">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="5c54e-808">Chcete-li provést migraci `ComplexDataModel` se stávajícími daty:</span><span class="sxs-lookup"><span data-stu-id="5c54e-808">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="5c54e-809">Změňte kód tak, aby měl nový sloupec (`DepartmentID`) výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5c54e-809">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="5c54e-810">Vytvořte falešné oddělení s názvem "Temp", které bude sloužit jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="5c54e-810">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="5c54e-811">Oprava omezení cizího klíče</span><span class="sxs-lookup"><span data-stu-id="5c54e-811">Fix the foreign key constraints</span></span>

<span data-ttu-id="5c54e-812">Aktualizujte třídy `ComplexDataModel` `Up`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-812">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="5c54e-813">Otevřete soubor *{timestamp} _ComplexDataModel. cs* .</span><span class="sxs-lookup"><span data-stu-id="5c54e-813">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="5c54e-814">Odkomentujte řádek kódu, který přidá sloupec `DepartmentID` do tabulky `Course`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-814">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="5c54e-815">Přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="5c54e-815">Add the following highlighted code.</span></span> <span data-ttu-id="5c54e-816">Nový kód přejde za blok `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="5c54e-816">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="5c54e-817">V předchozích změnách budou existující řádky `Course` po spuštění metody `ComplexDataModel` `Up` souviset s oddělením "dočasného".</span><span class="sxs-lookup"><span data-stu-id="5c54e-817">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="5c54e-818">Produkční aplikace by:</span><span class="sxs-lookup"><span data-stu-id="5c54e-818">A production app would:</span></span>

* <span data-ttu-id="5c54e-819">Vložte kód nebo skripty pro přidání `Department` řádků a souvisejících řádků `Course` do nových řádků `Department`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-819">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="5c54e-820">Nepoužívejte oddělení "dočasné" nebo výchozí hodnotu pro `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="5c54e-820">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="5c54e-821">Další kurz se zabývá souvisejícími daty.</span><span class="sxs-lookup"><span data-stu-id="5c54e-821">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c54e-822">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="5c54e-822">Additional resources</span></span>

* [<span data-ttu-id="5c54e-823">Verze tohoto kurzu pro YouTube (část 1)</span><span class="sxs-lookup"><span data-stu-id="5c54e-823">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="5c54e-824">Verze tohoto kurzu pro YouTube (část 2)</span><span class="sxs-lookup"><span data-stu-id="5c54e-824">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="5c54e-825">[Předchozí](xref:data/ef-rp/migrations)@no__t – 1 –[Další](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="5c54e-825">[Previous](xref:data/ef-rp/migrations)
> [Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
