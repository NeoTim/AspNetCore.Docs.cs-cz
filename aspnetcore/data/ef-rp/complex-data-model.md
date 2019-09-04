---
title: Razor Pages s EF Core v ASP.NET Core-datový model 5 z 8
author: tdykstra
description: V tomto kurzu přidejte další entity a vztahy a upravte datový model zadáním formátování, ověřování a pravidel mapování.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: ab29cf687c80551d275cae69f28b7576016bfff6
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238121"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="10857-103">Razor Pages s EF Core v ASP.NET Core-datový model 5 z 8</span><span class="sxs-lookup"><span data-stu-id="10857-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="10857-104">Podle [Petr Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="10857-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10857-105">Předchozí kurzy pracovaly se základním datovým modelem, který se skládá ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="10857-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="10857-106">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="10857-106">In this tutorial:</span></span>

* <span data-ttu-id="10857-107">Přidávají se další entity a vztahy.</span><span class="sxs-lookup"><span data-stu-id="10857-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="10857-108">Datový model je přizpůsoben zadáním pravidel formátování, ověřování a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="10857-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="10857-109">Dokončený datový model je zobrazený na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="10857-109">The completed data model is shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="10857-111">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="10857-111">The Student entity</span></span>

![Entita studenta](complex-data-model/_static/student-entity.png)

<span data-ttu-id="10857-113">Nahraďte kód v *modelů/student. cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="10857-114">Předchozí kód přidá `FullName` vlastnost a přidá následující atributy do existujících vlastností:</span><span class="sxs-lookup"><span data-stu-id="10857-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="10857-115">Vypočítaná vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="10857-115">The FullName calculated property</span></span>

<span data-ttu-id="10857-116">`FullName`je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="10857-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="10857-117">`FullName`nedá se nastavit, takže má jenom přístup Get.</span><span class="sxs-lookup"><span data-stu-id="10857-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="10857-118">V `FullName` databázi není vytvořen žádný sloupec.</span><span class="sxs-lookup"><span data-stu-id="10857-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="10857-119">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="10857-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="10857-120">Pro data o registraci studenta aktuálně zobrazují všechny stránky denní dobu a datum, i když se jedná o relevantní datum.</span><span class="sxs-lookup"><span data-stu-id="10857-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="10857-121">Pomocí atributů datových poznámek můžete vytvořit jednu změnu kódu, která bude opravovat formát zobrazení na každé stránce, která zobrazuje data.</span><span class="sxs-lookup"><span data-stu-id="10857-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="10857-122">Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) Určuje datový typ, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="10857-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="10857-123">V tomto případě by se měla zobrazit pouze datum, nikoli datum a čas.</span><span class="sxs-lookup"><span data-stu-id="10857-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="10857-124">[Výčet DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress atd. `DataType` Atribut také může aplikaci povolit automatické poskytování funkcí specifických pro typ.</span><span class="sxs-lookup"><span data-stu-id="10857-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="10857-125">Příklad:</span><span class="sxs-lookup"><span data-stu-id="10857-125">For example:</span></span>

* <span data-ttu-id="10857-126">Automaticky se vytvoří `DataType.EmailAddress`odkaz pro. `mailto:`</span><span class="sxs-lookup"><span data-stu-id="10857-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="10857-127">Selektor data je k `DataType.Date` dispozici ve většině prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="10857-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="10857-128">Atribut emituje atributy HTML 5 `data-` (vyslovení data pomlčky). `DataType`</span><span class="sxs-lookup"><span data-stu-id="10857-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="10857-129">`DataType` Atributy neposkytují ověřování.</span><span class="sxs-lookup"><span data-stu-id="10857-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="10857-130">Atribut DisplayFormat</span><span class="sxs-lookup"><span data-stu-id="10857-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="10857-131">`DataType.Date`neurčuje formát data, které se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="10857-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="10857-132">Ve výchozím nastavení se pole Datum zobrazuje v závislosti na výchozích formátech na základě objektu [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serveru.</span><span class="sxs-lookup"><span data-stu-id="10857-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="10857-133">`DisplayFormat` Atribut slouží k explicitnímu zadání formátu data.</span><span class="sxs-lookup"><span data-stu-id="10857-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="10857-134">Toto `ApplyFormatInEditMode` nastavení určuje, že by mělo být formátování použito také na uživatelské rozhraní pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="10857-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="10857-135">Některá pole byste neměli `ApplyFormatInEditMode`používat.</span><span class="sxs-lookup"><span data-stu-id="10857-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="10857-136">Například symbol měny by neměl být v textovém poli pro úpravy obvykle zobrazen.</span><span class="sxs-lookup"><span data-stu-id="10857-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="10857-137">`DisplayFormat` Atribut může používat sám sebe.</span><span class="sxs-lookup"><span data-stu-id="10857-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="10857-138">Obecně je vhodné použít `DataType` atribut `DisplayFormat` s atributem.</span><span class="sxs-lookup"><span data-stu-id="10857-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="10857-139">`DataType` Atribut předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce.</span><span class="sxs-lookup"><span data-stu-id="10857-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="10857-140">Atribut poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`: `DataType`</span><span class="sxs-lookup"><span data-stu-id="10857-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="10857-141">Prohlížeč může povolit funkce HTML5.</span><span class="sxs-lookup"><span data-stu-id="10857-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="10857-142">Například můžete zobrazit ovládací prvek kalendáře, symbol měny odpovídající národním prostředí, e-mailové odkazy a ověření vstupu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="10857-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="10857-143">Ve výchozím nastavení prohlížeč vykresluje data pomocí správného formátu založeného na národním prostředí.</span><span class="sxs-lookup"><span data-stu-id="10857-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="10857-144">Další informace najdete v [ \<dokumentaci k rutině Input > Tag](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="10857-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="10857-145">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="10857-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="10857-146">Pravidla ověřování dat a chybové zprávy ověřování lze zadat pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="10857-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="10857-147">Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli.</span><span class="sxs-lookup"><span data-stu-id="10857-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="10857-148">Zobrazený kód omezuje názvy na více než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="10857-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="10857-149">Příklad, který nastaví minimální délku řetězce, se zobrazí [později](#the-required-attribute).</span><span class="sxs-lookup"><span data-stu-id="10857-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="10857-150">`StringLength` Atribut také poskytuje ověřování na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="10857-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="10857-151">Minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="10857-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="10857-152">`StringLength` Atribut nebrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="10857-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="10857-153">Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) lze použít k uplatnění omezení na vstup.</span><span class="sxs-lookup"><span data-stu-id="10857-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="10857-154">Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:</span><span class="sxs-lookup"><span data-stu-id="10857-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="10857-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10857-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="10857-156">V **Průzkumník objektů systému SQL Server** (SSOX) otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .</span><span class="sxs-lookup"><span data-stu-id="10857-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů v SSOX před migracemi](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="10857-158">Na předchozím obrázku je znázorněno schéma pro `Student` tabulku.</span><span class="sxs-lookup"><span data-stu-id="10857-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="10857-159">Pole název mají typ `nvarchar(MAX)`.</span><span class="sxs-lookup"><span data-stu-id="10857-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="10857-160">Když se vytvoří migrace a použije se později v tomto kurzu, pole název se změní `nvarchar(50)` v důsledku atributů délky řetězce.</span><span class="sxs-lookup"><span data-stu-id="10857-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="10857-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10857-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="10857-162">V nástroji SQLite si Projděte definice sloupců pro `Student` tabulku.</span><span class="sxs-lookup"><span data-stu-id="10857-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="10857-163">Pole název mají typ `Text`.</span><span class="sxs-lookup"><span data-stu-id="10857-163">The name fields have type `Text`.</span></span> <span data-ttu-id="10857-164">Všimněte si, že je voláno `FirstMidName`pole jméno.</span><span class="sxs-lookup"><span data-stu-id="10857-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="10857-165">V další části změníte název tohoto sloupce na `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="10857-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="10857-166">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="10857-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="10857-167">Atributy mohou řídit způsob, jakým jsou třídy a vlastnosti mapovány na databázi.</span><span class="sxs-lookup"><span data-stu-id="10857-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="10857-168">V modelu je atribut použit k mapování názvu `FirstMidName` vlastnosti na hodnotu "FirstName" v databázi. `Column` `Student`</span><span class="sxs-lookup"><span data-stu-id="10857-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="10857-169">Při vytvoření databáze se názvy vlastností v modelu používají pro názvy sloupců (kromě případu, `Column` kdy se atribut používá).</span><span class="sxs-lookup"><span data-stu-id="10857-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="10857-170">`Student` Model používá`FirstMidName` pole pro první název, protože pole může obsahovat také prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="10857-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="10857-171">`[Column]` S `FirstName` `Student` atributem se vdatovémmodelumapuje`Student.FirstMidName` na sloupec tabulky.</span><span class="sxs-lookup"><span data-stu-id="10857-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="10857-172">Přidání `Column` atributu změní model `SchoolContext`zálohování.</span><span class="sxs-lookup"><span data-stu-id="10857-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="10857-173">Model, který `SchoolContext` zálohování již nevyhovuje, se neshoduje s databází.</span><span class="sxs-lookup"><span data-stu-id="10857-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="10857-174">Tato nesrovnalost bude vyřešena přidáním migrace později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="10857-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="10857-175">Požadovaný atribut</span><span class="sxs-lookup"><span data-stu-id="10857-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="10857-176">`Required` Atribut nastaví název vlastnosti povinná pole.</span><span class="sxs-lookup"><span data-stu-id="10857-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="10857-177">Atribut není potřebný pro typy `DateTime`, `int`které neumožňují hodnotu null, jako jsou například typy hodnot (například, `double`, a). `Required`</span><span class="sxs-lookup"><span data-stu-id="10857-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="10857-178">Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="10857-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="10857-179">Atribut musí být použit s `MinimumLength` pro `MinimumLength` vymáhání. `Required`</span><span class="sxs-lookup"><span data-stu-id="10857-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="10857-180">`MinimumLength`a `Required` umožňují, aby prázdné znaky splňovaly ověření.</span><span class="sxs-lookup"><span data-stu-id="10857-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="10857-181">`RegularExpression` Použijte atribut pro úplnou kontrolu nad řetězcem.</span><span class="sxs-lookup"><span data-stu-id="10857-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="10857-182">Atribut zobrazení</span><span class="sxs-lookup"><span data-stu-id="10857-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="10857-183">`Display` Atribut určuje, že titulek pro textová pole by měl být "jméno", "příjmení", "celé jméno" a "datum zápisu".</span><span class="sxs-lookup"><span data-stu-id="10857-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="10857-184">Výchozí titulky neobsahovaly místo dělení slov, například "LastName".</span><span class="sxs-lookup"><span data-stu-id="10857-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="10857-185">Vytvoření migrace</span><span class="sxs-lookup"><span data-stu-id="10857-185">Create a migration</span></span>

<span data-ttu-id="10857-186">Spusťte aplikaci a pokračujte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="10857-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="10857-187">Je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="10857-187">An exception is thrown.</span></span> <span data-ttu-id="10857-188">Atribut způsobí, že EF očekává, že najde sloupec s `FirstName`názvem, ale název sloupce v databázi je stále `FirstMidName`. `[Column]`</span><span class="sxs-lookup"><span data-stu-id="10857-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="10857-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10857-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="10857-190">Chybová zpráva je podobná následujícímu příkladu:</span><span class="sxs-lookup"><span data-stu-id="10857-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="10857-191">Do PMC zadejte následující příkazy, abyste vytvořili novou migraci a aktualizovali databázi:</span><span class="sxs-lookup"><span data-stu-id="10857-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="10857-192">První z těchto příkazů generuje následující varovnou zprávu:</span><span class="sxs-lookup"><span data-stu-id="10857-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="10857-193">Upozornění je vygenerováno, protože pole s názvem jsou nyní omezena na 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="10857-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="10857-194">Pokud má název v databázi více než 50 znaků, bude ztraceno 51 k poslednímu znaku.</span><span class="sxs-lookup"><span data-stu-id="10857-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="10857-195">Otevřete tabulku student v SSOX:</span><span class="sxs-lookup"><span data-stu-id="10857-195">Open the Student table in SSOX:</span></span>

  ![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="10857-197">Před použitím migrace byly sloupce názvu typu [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="10857-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="10857-198">Sloupce názvů jsou nyní `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="10857-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="10857-199">Změnil se název sloupce z `FirstMidName` na. `FirstName`</span><span class="sxs-lookup"><span data-stu-id="10857-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="10857-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10857-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="10857-201">Chybová zpráva je podobná následujícímu příkladu:</span><span class="sxs-lookup"><span data-stu-id="10857-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="10857-202">Otevřete okno příkazového řádku ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="10857-202">Open a command window in the project folder.</span></span> <span data-ttu-id="10857-203">Zadáním následujících příkazů vytvořte novou migraci a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="10857-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```console
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="10857-204">Příkaz aktualizace databáze zobrazí chybu jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="10857-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="10857-205">Pro tento kurz se způsobem, jak se tato chyba zobrazí po této chybě, odstranit a znovu vytvořit počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="10857-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="10857-206">Další informace najdete v poznámkovém upozornění SQLite v horní části [kurzu migrace](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="10857-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="10857-207">Odstraňte složku *migrace* .</span><span class="sxs-lookup"><span data-stu-id="10857-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="10857-208">Spusťte následující příkazy k vyřazení databáze, vytvořte novou počáteční migraci a použijte migraci:</span><span class="sxs-lookup"><span data-stu-id="10857-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```console
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="10857-209">Projděte si tabulku student v nástroji SQLite.</span><span class="sxs-lookup"><span data-stu-id="10857-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="10857-210">Sloupec, který byl FirstMidName, je nyní FirstName.</span><span class="sxs-lookup"><span data-stu-id="10857-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="10857-211">Spusťte aplikaci a pokračujte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="10857-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="10857-212">Všimněte si, že časy nejsou vstupní ani se nezobrazují spolu s kalendářními daty.</span><span class="sxs-lookup"><span data-stu-id="10857-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="10857-213">Vyberte **vytvořit novou**a zkuste zadat název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="10857-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="10857-214">V následujících oddílech sestavení aplikace v některých fázích generuje chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="10857-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="10857-215">Pokyny určují, kdy se má aplikace sestavit.</span><span class="sxs-lookup"><span data-stu-id="10857-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="10857-216">Entita instruktor</span><span class="sxs-lookup"><span data-stu-id="10857-216">The Instructor Entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="10857-218">Vytvořte *modely/Instructor. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="10857-219">Více atributů může být na jednom řádku.</span><span class="sxs-lookup"><span data-stu-id="10857-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="10857-220">`HireDate` Atributy mohou být zapsány následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="10857-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="10857-221">Navigační vlastnosti</span><span class="sxs-lookup"><span data-stu-id="10857-221">Navigation properties</span></span>

<span data-ttu-id="10857-222">Vlastnosti `CourseAssignments` a`OfficeAssignment` jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="10857-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="10857-223">Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.</span><span class="sxs-lookup"><span data-stu-id="10857-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="10857-224">Instruktor může mít maximálně jednu kancelář, takže `OfficeAssignment` vlastnost obsahuje jednu `OfficeAssignment` entitu.</span><span class="sxs-lookup"><span data-stu-id="10857-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="10857-225">`OfficeAssignment`má hodnotu null, pokud není přiřazen žádný systém Office.</span><span class="sxs-lookup"><span data-stu-id="10857-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="10857-226">Entita OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="10857-226">The OfficeAssignment entity</span></span>

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="10857-228">Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="10857-229">Klíčový atribut</span><span class="sxs-lookup"><span data-stu-id="10857-229">The Key attribute</span></span>

<span data-ttu-id="10857-230">`[Key]` Atribut slouží k identifikaci vlastnosti jako primárního klíče (PK), pokud je název vlastnosti něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="10857-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="10857-231">Mezi `Instructor` entitami a `OfficeAssignment` je relace 1:1 nebo jedna.</span><span class="sxs-lookup"><span data-stu-id="10857-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="10857-232">Přiřazení kanceláře existuje jenom ve vztahu k instruktorovi, ke kterému je přiřazený.</span><span class="sxs-lookup"><span data-stu-id="10857-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="10857-233">PK je také jeho cizí klíč (FK) `Instructor` k entitě. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="10857-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="10857-234">EF Core nemůže automaticky rozpoznat `InstructorID` jako `OfficeAssignment` PK, protože `InstructorID` nedodržuje konvence pojmenování ID nebo classnameID.</span><span class="sxs-lookup"><span data-stu-id="10857-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="10857-235">Proto atribut slouží k identifikaci `InstructorID` jako PK: `Key`</span><span class="sxs-lookup"><span data-stu-id="10857-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="10857-236">Ve výchozím nastavení EF Core považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.</span><span class="sxs-lookup"><span data-stu-id="10857-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="10857-237">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="10857-237">The Instructor navigation property</span></span>

<span data-ttu-id="10857-238">Navigační vlastnost může mít hodnotu null, protože pro daný instruktor nemusí `OfficeAssignment` existovat řádek. `Instructor.OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="10857-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="10857-239">Instruktor nemusí mít přiřazení kanceláře.</span><span class="sxs-lookup"><span data-stu-id="10857-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="10857-240">Navigační vlastnost bude mít vždycky entitu instruktora, protože typ cizího klíče `int` `InstructorID` je, typ hodnoty, která není null. `OfficeAssignment.Instructor`</span><span class="sxs-lookup"><span data-stu-id="10857-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="10857-241">Přiřazení kanceláře nemůže existovat bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="10857-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="10857-242">Pokud má `OfficeAssignment` entita související entitu, Každá entita má odkaz na jinou entitu v její navigační vlastnosti. `Instructor`</span><span class="sxs-lookup"><span data-stu-id="10857-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="10857-243">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="10857-243">The Course Entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="10857-245">Aktualizujte *modely/Course. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="10857-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="10857-246">Entita má vlastnost `DepartmentID`cizího klíče (FK). `Course`</span><span class="sxs-lookup"><span data-stu-id="10857-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="10857-247">`DepartmentID`odkazuje na související `Department` entitu.</span><span class="sxs-lookup"><span data-stu-id="10857-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="10857-248">`Course` Entita má vlastnost navigace. `Department`</span><span class="sxs-lookup"><span data-stu-id="10857-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="10857-249">EF Core nevyžaduje vlastnost cizího klíče pro datový model, pokud model má vlastnost navigace pro související entitu.</span><span class="sxs-lookup"><span data-stu-id="10857-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="10857-250">EF Core v databázi automaticky vytvoří FKs bez ohledu na to, kde jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="10857-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="10857-251">EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automatické vytváření FKs.</span><span class="sxs-lookup"><span data-stu-id="10857-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="10857-252">Explicitní a efektivnější je však, že explicitně včetně FK v datovém modelu může zjednodušit a efektivněji dělat aktualizace.</span><span class="sxs-lookup"><span data-stu-id="10857-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="10857-253">Zvažte například model, ve kterém *není obsažena* vlastnost `DepartmentID` FK.</span><span class="sxs-lookup"><span data-stu-id="10857-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="10857-254">Když se načte entita kurzu, která se upraví:</span><span class="sxs-lookup"><span data-stu-id="10857-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="10857-255">`Department` Vlastnost má hodnotu null, pokud není explicitně načtena.</span><span class="sxs-lookup"><span data-stu-id="10857-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="10857-256">Chcete-li aktualizovat entitu kurzu `Department` , je nutné nejprve načíst entitu.</span><span class="sxs-lookup"><span data-stu-id="10857-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="10857-257">Pokud je vlastnost `DepartmentID` FK obsažena v datovém modelu, není nutné `Department` načíst entitu před aktualizací.</span><span class="sxs-lookup"><span data-stu-id="10857-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="10857-258">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="10857-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="10857-259">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` Atribut určuje, zda je v rámci aplikace poskytnuta PK místo vygenerovaného databází.</span><span class="sxs-lookup"><span data-stu-id="10857-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="10857-260">Ve výchozím nastavení EF Core předpokládá, že se hodnoty PK generují v databázi.</span><span class="sxs-lookup"><span data-stu-id="10857-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="10857-261">Vygenerovaná databáze obvykle představuje nejlepší přístup.</span><span class="sxs-lookup"><span data-stu-id="10857-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="10857-262">Pro `Course` entity určuje uživatel PK.</span><span class="sxs-lookup"><span data-stu-id="10857-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="10857-263">Například číslo kurzu, jako je například série 1000 pro matematické oddělení, série 2000 pro národní oddělení.</span><span class="sxs-lookup"><span data-stu-id="10857-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="10857-264">`DatabaseGenerated` Atribut lze také použít ke generování výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="10857-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="10857-265">Databáze může například automaticky vygenerovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován.</span><span class="sxs-lookup"><span data-stu-id="10857-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="10857-266">Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="10857-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="10857-267">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="10857-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="10857-268">Vlastnosti cizího klíče (FK) a navigační vlastnosti v `Course` entitě odráží následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="10857-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="10857-269">Kurz se přiřadí jednomu oddělení, takže je k dispozici `DepartmentID` FK `Department` a navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="10857-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="10857-270">V rámci kurzu může být zaregistrované několik studentů, takže `Enrollments` navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="10857-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="10857-271">Kurz může být výukou více instruktorů, takže `CourseAssignments` navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="10857-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="10857-272">`CourseAssignment`je vysvětleno [později](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="10857-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="10857-273">Entita oddělení</span><span class="sxs-lookup"><span data-stu-id="10857-273">The Department entity</span></span>

![Entita oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="10857-275">Vytvořte *modely/oddělení. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="10857-276">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="10857-276">The Column attribute</span></span>

<span data-ttu-id="10857-277">Dřív se `Column` použil atribut pro změnu mapování názvu sloupce.</span><span class="sxs-lookup"><span data-stu-id="10857-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="10857-278">V kódu pro `Department` entitu `Column` se atribut používá ke změně mapování datových typů SQL.</span><span class="sxs-lookup"><span data-stu-id="10857-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="10857-279">`Budget` Sloupec je definovaný pomocí SQL Server peněžního typu v databázi:</span><span class="sxs-lookup"><span data-stu-id="10857-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="10857-280">Mapování sloupce není obecně vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="10857-280">Column mapping is generally not required.</span></span> <span data-ttu-id="10857-281">EF Core zvolí příslušný datový typ SQL Server na základě typu CLR pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="10857-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="10857-282">Typ CLR `decimal` se mapuje na typ SQL Server `decimal` .</span><span class="sxs-lookup"><span data-stu-id="10857-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="10857-283">`Budget`je pro měnu a datový typ Money je pro měnu vhodný.</span><span class="sxs-lookup"><span data-stu-id="10857-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="10857-284">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="10857-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="10857-285">Vlastnosti FK a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="10857-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="10857-286">Oddělení může nebo nemusí mít správce.</span><span class="sxs-lookup"><span data-stu-id="10857-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="10857-287">Správce je vždy instruktor.</span><span class="sxs-lookup"><span data-stu-id="10857-287">An administrator is always an instructor.</span></span> <span data-ttu-id="10857-288">Proto je `Instructor` vlastnost obsažena jako FK pro entitu. `InstructorID`</span><span class="sxs-lookup"><span data-stu-id="10857-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="10857-289">Navigační vlastnost má název `Administrator` , ale `Instructor` obsahuje entitu:</span><span class="sxs-lookup"><span data-stu-id="10857-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="10857-290">Otazník (?) v předchozím kódu určuje vlastnost s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="10857-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="10857-291">Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:</span><span class="sxs-lookup"><span data-stu-id="10857-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="10857-292">Podle konvence EF Core povoluje kaskádové odstranění pro FKs, která nejsou null a pro relace m:n.</span><span class="sxs-lookup"><span data-stu-id="10857-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="10857-293">Toto výchozí chování může způsobit cyklické kaskády odstraňování pravidel.</span><span class="sxs-lookup"><span data-stu-id="10857-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="10857-294">Cyklická kaskádová odstranění pravidel způsobují při přidání migrace výjimku.</span><span class="sxs-lookup"><span data-stu-id="10857-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="10857-295">Pokud je `Department.InstructorID` například vlastnost definovaná jako nepovolená, EF Core by nakonfigurovala pravidlo kaskádového odstranění.</span><span class="sxs-lookup"><span data-stu-id="10857-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="10857-296">V takovém případě by se oddělení odstranilo, když se odstraní instruktor jako jeho správce.</span><span class="sxs-lookup"><span data-stu-id="10857-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="10857-297">V tomto scénáři by pravidlo omezení mělo smysl.</span><span class="sxs-lookup"><span data-stu-id="10857-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="10857-298">Následující [rozhraní Fluent API](#fluent-api-alternative-to-attributes) by nastavilo pravidlo omezení a zakáže kaskádové odstranění.</span><span class="sxs-lookup"><span data-stu-id="10857-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="10857-299">Registrace entity</span><span class="sxs-lookup"><span data-stu-id="10857-299">The Enrollment entity</span></span>

<span data-ttu-id="10857-300">Záznam zápisu je pro jeden kurz, který přijímá jeden student.</span><span class="sxs-lookup"><span data-stu-id="10857-300">An enrollment record is for one course taken by one student.</span></span>

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="10857-302">Aktualizujte *modely/zápis. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="10857-303">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="10857-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="10857-304">Vlastnosti CK a vlastnosti navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="10857-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="10857-305">Záznam zápisu je pro jeden kurz, takže existuje `CourseID` vlastnost FK `Course` a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="10857-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="10857-306">Záznam zápisu je určen pro jednoho studenta, takže existuje `StudentID` vlastnost FK `Student` a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="10857-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="10857-307">Relace m:n</span><span class="sxs-lookup"><span data-stu-id="10857-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="10857-308">Mezi `Student` entitami a `Course` existuje vztah m:n.</span><span class="sxs-lookup"><span data-stu-id="10857-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="10857-309">Entita funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi. `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="10857-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="10857-310">"S datovou částí" znamená `Enrollment` , že tabulka obsahuje další data kromě FKs pro Spojené tabulky (v tomto případě PK a `Grade`).</span><span class="sxs-lookup"><span data-stu-id="10857-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="10857-311">Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit.</span><span class="sxs-lookup"><span data-stu-id="10857-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="10857-312">(Tento diagram byl vygenerován pomocí [nástrojů EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6. x.</span><span class="sxs-lookup"><span data-stu-id="10857-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="10857-313">Vytvoření diagramu není součástí kurzu.)</span><span class="sxs-lookup"><span data-stu-id="10857-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

<span data-ttu-id="10857-315">Každá čára relace má 1 na jednom konci a hvězdičku (\*) na druhé straně, která indikuje relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="10857-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="10857-316">Pokud tabulka neobsahovala informace o třídě, musí obsahovat pouze dvě FKs (`CourseID` a `StudentID`). `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="10857-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="10857-317">Tabulka JOIN typu m:n bez datové části se někdy označuje jako čistá spojovací tabulka (PJT).</span><span class="sxs-lookup"><span data-stu-id="10857-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="10857-318">Entity `Instructor` a`Course` mají relaci n:n pomocí tabulky Pure JOIN.</span><span class="sxs-lookup"><span data-stu-id="10857-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="10857-319">Poznámka: EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne.</span><span class="sxs-lookup"><span data-stu-id="10857-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="10857-320">Další informace najdete v tématu [relace m:n v EF Core 2,0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="10857-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="10857-321">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="10857-321">The CourseAssignment entity</span></span>

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="10857-323">Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="10857-324">Relace m:n pro každého-více kurzů vyžaduje tabulku JOIN a entita pro tuto tabulku JOIN je CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="10857-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="10857-326">Je běžné pojmenovat entitu `EntityName1EntityName2`JOIN.</span><span class="sxs-lookup"><span data-stu-id="10857-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="10857-327">Například tabulka pro spojení instruktora do kurzů, kterou používá tento model `CourseInstructor`, bude.</span><span class="sxs-lookup"><span data-stu-id="10857-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="10857-328">Doporučujeme však použít název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="10857-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="10857-329">Modely dat začínají jednoduchým a roste.</span><span class="sxs-lookup"><span data-stu-id="10857-329">Data models start out simple and grow.</span></span> <span data-ttu-id="10857-330">Spojování tabulek bez datové části (PJTs) se často vyvíjí, aby zahrnovalo datovou část.</span><span class="sxs-lookup"><span data-stu-id="10857-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="10857-331">Když začnete s popisným názvem entity, nemusíte při změně tabulky JOIN měnit název.</span><span class="sxs-lookup"><span data-stu-id="10857-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="10857-332">V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="10857-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="10857-333">Například knihy a zákazníci mohou být propojeny s entitou JOIN nazvanou hodnocení.</span><span class="sxs-lookup"><span data-stu-id="10857-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="10857-334">Pro relaci `CourseAssignment` n:n v instruktorech na více kurzů se používá přednost před `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="10857-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="10857-335">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="10857-335">Composite key</span></span>

<span data-ttu-id="10857-336">Dva FKs v `CourseAssignment` (`InstructorID` `CourseID` a`CourseAssignment` ) společně identifikují každý řádek tabulky.</span><span class="sxs-lookup"><span data-stu-id="10857-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="10857-337">`CourseAssignment`nevyžaduje vyhrazený PK.</span><span class="sxs-lookup"><span data-stu-id="10857-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="10857-338">Vlastnosti `InstructorID` a`CourseID` fungují jako složené PK.</span><span class="sxs-lookup"><span data-stu-id="10857-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="10857-339">Jediným způsobem, jak zadat složené PKs EF Core je s *rozhraním API Fluent*.</span><span class="sxs-lookup"><span data-stu-id="10857-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="10857-340">V další části se dozvíte, jak nakonfigurovat složený PK.</span><span class="sxs-lookup"><span data-stu-id="10857-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="10857-341">Složený klíč zajišťuje:</span><span class="sxs-lookup"><span data-stu-id="10857-341">The composite key ensures that:</span></span>

* <span data-ttu-id="10857-342">Pro jeden kurz je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="10857-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="10857-343">Pro jednoho instruktora je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="10857-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="10857-344">Pro stejný instruktor a kurz nejsou povoleny vícenásobné řádky.</span><span class="sxs-lookup"><span data-stu-id="10857-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="10857-345">Entita `Enrollment` JOIN definuje vlastní PK, takže je možné duplikovat toto řazení.</span><span class="sxs-lookup"><span data-stu-id="10857-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="10857-346">Chcete-li zabránit těmto duplicitám:</span><span class="sxs-lookup"><span data-stu-id="10857-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="10857-347">Přidejte do polí FK jedinečný index nebo</span><span class="sxs-lookup"><span data-stu-id="10857-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="10857-348">Nakonfigurujte `Enrollment` pomocí primárního složeného klíče `CourseAssignment`podobného.</span><span class="sxs-lookup"><span data-stu-id="10857-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="10857-349">Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="10857-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="10857-350">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="10857-350">Update the database context</span></span>

<span data-ttu-id="10857-351">Aktualizujte *data/SchoolContext. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="10857-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="10857-352">Předchozí kód přidá nové entity a nakonfiguruje `CourseAssignment` neseparovaný PK entity.</span><span class="sxs-lookup"><span data-stu-id="10857-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="10857-353">Alternativa k atributům rozhraní Fluent API</span><span class="sxs-lookup"><span data-stu-id="10857-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="10857-354">Metoda v předchozím kódu používá *rozhraní Fluent API* ke konfiguraci chování EF Core. `OnModelCreating`</span><span class="sxs-lookup"><span data-stu-id="10857-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="10857-355">Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu.</span><span class="sxs-lookup"><span data-stu-id="10857-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="10857-356">[Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem rozhraní Fluent API:</span><span class="sxs-lookup"><span data-stu-id="10857-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="10857-357">V tomto kurzu se rozhraní API Fluent používá jenom pro mapování databáze, které nejde s atributy dělat.</span><span class="sxs-lookup"><span data-stu-id="10857-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="10857-358">Rozhraní API Fluent ale může určovat většinu pravidel formátování, ověřování a mapování, která se dají provádět s atributy.</span><span class="sxs-lookup"><span data-stu-id="10857-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="10857-359">Některé atributy, `MinimumLength` jako například, se nedají použít s rozhraním API Fluent.</span><span class="sxs-lookup"><span data-stu-id="10857-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="10857-360">`MinimumLength`nemění schéma, používá pouze ověřovací pravidlo minimální délky.</span><span class="sxs-lookup"><span data-stu-id="10857-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="10857-361">Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit".</span><span class="sxs-lookup"><span data-stu-id="10857-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="10857-362">Atributy a rozhraní API Fluent lze kombinovat.</span><span class="sxs-lookup"><span data-stu-id="10857-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="10857-363">Existují některé konfigurace, které lze provést pouze s rozhraním API Fluent (určením složeného PK).</span><span class="sxs-lookup"><span data-stu-id="10857-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="10857-364">Existují některé konfigurace, které lze provádět pouze s atributy (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="10857-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="10857-365">Doporučený postup pro použití rozhraní Fluent API nebo atributů:</span><span class="sxs-lookup"><span data-stu-id="10857-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="10857-366">Vyberte jednu z těchto dvou přístupů.</span><span class="sxs-lookup"><span data-stu-id="10857-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="10857-367">Používejte vybraný postup konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="10857-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="10857-368">Některé atributy používané v tomto kurzu se používají pro:</span><span class="sxs-lookup"><span data-stu-id="10857-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="10857-369">Pouze ověření (například `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="10857-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="10857-370">Pouze konfigurace EF Core (například `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="10857-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="10857-371">Konfigurace ověřování a EF Core (například `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="10857-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="10857-372">Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="10857-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="10857-373">Diagram entit</span><span class="sxs-lookup"><span data-stu-id="10857-373">Entity diagram</span></span>

<span data-ttu-id="10857-374">Následující ilustrace znázorňuje diagram, který nástroje EF Power Tools vytvoří pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="10857-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="10857-376">Předchozí diagram znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="10857-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="10857-377">Několik čar relací 1:1 (1 až \*).</span><span class="sxs-lookup"><span data-stu-id="10857-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="10857-378">Čára relace 1:1 (1 – 0.. 1) mezi `Instructor` entitami a. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="10857-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="10857-379">Čára relace nula až n-many (0.. 1 až ×) mezi `Instructor` entitami a. `Department`</span><span class="sxs-lookup"><span data-stu-id="10857-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="10857-380">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="10857-380">Seed the database</span></span>

<span data-ttu-id="10857-381">Aktualizujte kód v *data/DbInitializer. cs*:</span><span class="sxs-lookup"><span data-stu-id="10857-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="10857-382">Předchozí kód poskytuje počáteční data pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="10857-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="10857-383">Většina tohoto kódu vytváří nové objekty entit a načítá vzorová data.</span><span class="sxs-lookup"><span data-stu-id="10857-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="10857-384">Ukázková data se používají k testování.</span><span class="sxs-lookup"><span data-stu-id="10857-384">The sample data is used for testing.</span></span> <span data-ttu-id="10857-385">V `Enrollments` tématu `CourseAssignments` a najdete příklady, jak lze dosazení tabulek JOIN typu many.</span><span class="sxs-lookup"><span data-stu-id="10857-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="10857-386">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="10857-386">Add a migration</span></span>

<span data-ttu-id="10857-387">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="10857-387">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="10857-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10857-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="10857-389">V PMC spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="10857-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="10857-390">Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="10857-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="10857-391">Pokud se `database update` příkaz spustí, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="10857-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="10857-392">V další části se dozvíte, co dělat s touto chybou.</span><span class="sxs-lookup"><span data-stu-id="10857-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="10857-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10857-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="10857-394">Pokud přidáte migraci a spustíte `database update` příkaz, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="10857-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="10857-395">V další části se zobrazí informace o tom, jak se vyhnout této chybě.</span><span class="sxs-lookup"><span data-stu-id="10857-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="10857-396">Použití migrace nebo vyřazení a opětovné vytvoření</span><span class="sxs-lookup"><span data-stu-id="10857-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="10857-397">Teď, když máte existující databázi, musíte si představit, jak se na ni aplikují změny.</span><span class="sxs-lookup"><span data-stu-id="10857-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="10857-398">V tomto kurzu se zobrazí dvě alternativy:</span><span class="sxs-lookup"><span data-stu-id="10857-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="10857-399">[Vyřaďte a znovu vytvořte databázi](#drop).</span><span class="sxs-lookup"><span data-stu-id="10857-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="10857-400">Tuto část vyberte, pokud používáte SQLite.</span><span class="sxs-lookup"><span data-stu-id="10857-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="10857-401">[Použijte migraci na stávající databázi](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="10857-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="10857-402">Pokyny v této části fungují pouze pro SQL Server, **nikoli pro SQLite**.</span><span class="sxs-lookup"><span data-stu-id="10857-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="10857-403">Jedna volba funguje pro SQL Server.</span><span class="sxs-lookup"><span data-stu-id="10857-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="10857-404">I když je metoda Apply složitější a časově náročná, jedná se o preferovaný přístup pro produkční prostředí z reálného světa.</span><span class="sxs-lookup"><span data-stu-id="10857-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="10857-405">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="10857-405">Drop and re-create the database</span></span>

<span data-ttu-id="10857-406">[Tuto část přeskočte](#apply-the-migration) , pokud používáte SQL Server a chcete provést postup použití migrace v následující části.</span><span class="sxs-lookup"><span data-stu-id="10857-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="10857-407">Chcete-li vynutit EF Core vytvoření nové databáze, vyřaďte a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="10857-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="10857-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10857-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="10857-409">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="10857-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="10857-410">Odstraňte složku *migrace* a pak spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="10857-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="10857-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10857-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="10857-412">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="10857-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="10857-413">Složka projektu obsahuje soubor *ContosoUniversity. csproj* .</span><span class="sxs-lookup"><span data-stu-id="10857-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="10857-414">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="10857-414">Run the following command:</span></span>

  ```console
  dotnet ef database drop --force
  ```

* <span data-ttu-id="10857-415">Odstraňte složku *migrace* a pak spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="10857-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="10857-416">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="10857-416">Run the app.</span></span> <span data-ttu-id="10857-417">Spuštění aplikace spustí `DbInitializer.Initialize` metodu.</span><span class="sxs-lookup"><span data-stu-id="10857-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="10857-418">`DbInitializer.Initialize` Naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="10857-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="10857-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10857-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="10857-420">Otevřete databázi v SSOX:</span><span class="sxs-lookup"><span data-stu-id="10857-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="10857-421">Pokud jste dříve otevřeli SSOX, klikněte na tlačítko **aktualizovat** .</span><span class="sxs-lookup"><span data-stu-id="10857-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="10857-422">Rozbalte **tabulky** uzlu.</span><span class="sxs-lookup"><span data-stu-id="10857-422">Expand the **Tables** node.</span></span> <span data-ttu-id="10857-423">Zobrazí se vytvořené tabulky.</span><span class="sxs-lookup"><span data-stu-id="10857-423">The created tables are displayed.</span></span>

  ![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="10857-425">Projděte si tabulku **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="10857-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="10857-426">Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data**.</span><span class="sxs-lookup"><span data-stu-id="10857-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="10857-427">Ověřte, že tabulka **CourseAssignment** obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="10857-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="10857-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10857-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="10857-430">Použijte nástroj SQLite k prohlédnutí databáze:</span><span class="sxs-lookup"><span data-stu-id="10857-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="10857-431">Nové tabulky a sloupce.</span><span class="sxs-lookup"><span data-stu-id="10857-431">New tables and columns.</span></span>
* <span data-ttu-id="10857-432">Osazená data v tabulkách, například tabulka **CourseAssignment** .</span><span class="sxs-lookup"><span data-stu-id="10857-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="10857-433">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="10857-433">Apply the migration</span></span>

<span data-ttu-id="10857-434">Tato část je volitelná.</span><span class="sxs-lookup"><span data-stu-id="10857-434">This section is optional.</span></span> <span data-ttu-id="10857-435">Tyto kroky fungují jenom pro SQL Server LocalDB a jenom v případě, že jste přeskočili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="10857-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="10857-436">Pokud jsou migrace spouštěny s existujícími daty, může dojít k omezením FK, která nesplňují stávající data.</span><span class="sxs-lookup"><span data-stu-id="10857-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="10857-437">S provozními daty je potřeba provést kroky pro migraci stávajících dat.</span><span class="sxs-lookup"><span data-stu-id="10857-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="10857-438">V této části najdete příklad opravy porušení omezení CK.</span><span class="sxs-lookup"><span data-stu-id="10857-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="10857-439">Neprovádějte změny kódu bez zálohy.</span><span class="sxs-lookup"><span data-stu-id="10857-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="10857-440">Neprovádějte změny kódu, pokud jste dokončili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="10857-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="10857-441">Soubor *{timestamp} _ComplexDataModel. cs* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="10857-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="10857-442">Předchozí kód přidá `Course` do tabulky nenulovou hodnotu `DepartmentID` typu FK.</span><span class="sxs-lookup"><span data-stu-id="10857-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="10857-443">Databáze z předchozího kurzu obsahuje řádky v `Course`, aby bylo možné tabulku aktualizovat pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="10857-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="10857-444">Chcete-li migrovat práci s existujícími daty: `ComplexDataModel`</span><span class="sxs-lookup"><span data-stu-id="10857-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="10857-445">Změňte kód tak, aby nový sloupec (`DepartmentID`) poskytl výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="10857-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="10857-446">Vytvořte falešné oddělení s názvem "Temp", které bude sloužit jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="10857-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="10857-447">Oprava omezení cizího klíče</span><span class="sxs-lookup"><span data-stu-id="10857-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="10857-448">Ve třídě `Up` migrace aktualizujte metodu: `ComplexDataModel`</span><span class="sxs-lookup"><span data-stu-id="10857-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="10857-449">Otevřete soubor *{timestamp} _ComplexDataModel. cs* .</span><span class="sxs-lookup"><span data-stu-id="10857-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="10857-450">Odkomentujte řádek kódu, který přidá `DepartmentID` sloupec `Course` do tabulky.</span><span class="sxs-lookup"><span data-stu-id="10857-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="10857-451">Přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="10857-451">Add the following highlighted code.</span></span> <span data-ttu-id="10857-452">Nový kód přejde za `.CreateTable( name: "Department"` blok:</span><span class="sxs-lookup"><span data-stu-id="10857-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

<span data-ttu-id="10857-453">[! Code-CSharp [] (Úvod/Samples/cu30snapshots/5-Complex/migrations/ComplexDataModel. cs? Name = snippet_CreateDefaultValue & zvýraznění = 23-31)]</span><span class="sxs-lookup"><span data-stu-id="10857-453">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span></span>

<span data-ttu-id="10857-454">V předchozích změnách budou existující `Course` řádky při spuštění `ComplexDataModel.Up` metody v relaci s "dočasným" oddělením.</span><span class="sxs-lookup"><span data-stu-id="10857-454">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="10857-455">Pro účely tohoto kurzu je zjednodušený způsob zpracování situace, kterou tady vidíte.</span><span class="sxs-lookup"><span data-stu-id="10857-455">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="10857-456">Produkční aplikace by:</span><span class="sxs-lookup"><span data-stu-id="10857-456">A production app would:</span></span>

* <span data-ttu-id="10857-457">Zahrnout kód nebo skripty pro přidání `Department` řádků a souvisejících `Course` řádků do nových `Department` řádků.</span><span class="sxs-lookup"><span data-stu-id="10857-457">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="10857-458">Nepoužívejte oddělení "dočasné" nebo výchozí hodnotu pro `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="10857-458">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="10857-459">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10857-459">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="10857-460">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="10857-460">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="10857-461">Vzhledem k tomu, že Metodajenavrženatak,abyfungovalapouzesprázdnoudatabází,použijteSSOXkodstraněnívšechřádkůvtabulkáchstudentaCourse.`DbInitializer.Initialize`</span><span class="sxs-lookup"><span data-stu-id="10857-461">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="10857-462">(Kaskádová odstranění se postará o tabulku zápisu.)</span><span class="sxs-lookup"><span data-stu-id="10857-462">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="10857-463">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10857-463">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="10857-464">Pokud používáte SQL Server LocalDB s Visual Studio Code, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="10857-464">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```console
  dotnet ef database update
  ```

---

<span data-ttu-id="10857-465">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="10857-465">Run the app.</span></span> <span data-ttu-id="10857-466">Spuštění aplikace spustí `DbInitializer.Initialize` metodu.</span><span class="sxs-lookup"><span data-stu-id="10857-466">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="10857-467">`DbInitializer.Initialize` Naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="10857-467">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="10857-468">Další postup</span><span class="sxs-lookup"><span data-stu-id="10857-468">Next steps</span></span>

<span data-ttu-id="10857-469">Následující dva kurzy ukazují, jak číst a aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="10857-469">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="10857-470">[Předchozí kurz](xref:data/ef-rp/migrations)
> –[Další kurz](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="10857-470">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10857-471">Předchozí kurzy pracovaly se základním datovým modelem, který se skládá ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="10857-471">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="10857-472">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="10857-472">In this tutorial:</span></span>

* <span data-ttu-id="10857-473">Přidávají se další entity a vztahy.</span><span class="sxs-lookup"><span data-stu-id="10857-473">More entities and relationships are added.</span></span>
* <span data-ttu-id="10857-474">Datový model je přizpůsoben zadáním pravidel formátování, ověřování a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="10857-474">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="10857-475">Třídy entit pro dokončený datový model jsou znázorněny na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="10857-475">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="10857-477">Pokud narazíte na problémy, které nemůžete [vyřešit,](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)Stáhněte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="10857-477">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="10857-478">Přizpůsobení datového modelu pomocí atributů</span><span class="sxs-lookup"><span data-stu-id="10857-478">Customize the data model with attributes</span></span>

<span data-ttu-id="10857-479">V této části je datový model přizpůsoben pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="10857-479">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="10857-480">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="10857-480">The DataType attribute</span></span>

<span data-ttu-id="10857-481">Na stránkách studenta se aktuálně zobrazuje čas pro datum registrace.</span><span class="sxs-lookup"><span data-stu-id="10857-481">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="10857-482">Pole data obvykle zobrazují pouze datum a čas.</span><span class="sxs-lookup"><span data-stu-id="10857-482">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="10857-483">Aktualizujte *modely/student. cs* následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-483">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="10857-484">Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) Určuje datový typ, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="10857-484">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="10857-485">V tomto případě by se měla zobrazit pouze datum, nikoli datum a čas.</span><span class="sxs-lookup"><span data-stu-id="10857-485">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="10857-486">[Výčet DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress atd. `DataType` Atribut také může aplikaci povolit automatické poskytování funkcí specifických pro typ.</span><span class="sxs-lookup"><span data-stu-id="10857-486">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="10857-487">Příklad:</span><span class="sxs-lookup"><span data-stu-id="10857-487">For example:</span></span>

* <span data-ttu-id="10857-488">Automaticky se vytvoří `DataType.EmailAddress`odkaz pro. `mailto:`</span><span class="sxs-lookup"><span data-stu-id="10857-488">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="10857-489">Selektor data je k `DataType.Date` dispozici ve většině prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="10857-489">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="10857-490">Atribut vygeneruje atributy HTML 5 `data-` (vyslovované datové přerušované), které používají prohlížeče formátu HTML 5. `DataType`</span><span class="sxs-lookup"><span data-stu-id="10857-490">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="10857-491">`DataType` Atributy neposkytují ověřování.</span><span class="sxs-lookup"><span data-stu-id="10857-491">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="10857-492">`DataType.Date`neurčuje formát data, které se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="10857-492">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="10857-493">Ve výchozím nastavení se pole Datum zobrazuje v závislosti na výchozích formátech na základě objektu [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serveru.</span><span class="sxs-lookup"><span data-stu-id="10857-493">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="10857-494">`DisplayFormat` Atribut slouží k explicitnímu zadání formátu data:</span><span class="sxs-lookup"><span data-stu-id="10857-494">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="10857-495">Toto `ApplyFormatInEditMode` nastavení určuje, že by mělo být formátování použito také na uživatelské rozhraní pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="10857-495">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="10857-496">Některá pole byste neměli `ApplyFormatInEditMode`používat.</span><span class="sxs-lookup"><span data-stu-id="10857-496">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="10857-497">Například symbol měny by neměl být v textovém poli pro úpravy obvykle zobrazen.</span><span class="sxs-lookup"><span data-stu-id="10857-497">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="10857-498">`DisplayFormat` Atribut může používat sám sebe.</span><span class="sxs-lookup"><span data-stu-id="10857-498">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="10857-499">Obecně je vhodné použít `DataType` atribut `DisplayFormat` s atributem.</span><span class="sxs-lookup"><span data-stu-id="10857-499">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="10857-500">`DataType` Atribut předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce.</span><span class="sxs-lookup"><span data-stu-id="10857-500">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="10857-501">Atribut poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`: `DataType`</span><span class="sxs-lookup"><span data-stu-id="10857-501">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="10857-502">Prohlížeč může povolit funkce HTML5.</span><span class="sxs-lookup"><span data-stu-id="10857-502">The browser can enable HTML5 features.</span></span> <span data-ttu-id="10857-503">Například můžete zobrazit ovládací prvek kalendáře, symbol měny odpovídající národním prostředí, e-mailové odkazy, ověřování vstupu na straně klienta atd.</span><span class="sxs-lookup"><span data-stu-id="10857-503">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="10857-504">Ve výchozím nastavení prohlížeč vykresluje data pomocí správného formátu založeného na národním prostředí.</span><span class="sxs-lookup"><span data-stu-id="10857-504">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="10857-505">Další informace najdete v [ \<dokumentaci k rutině Input > Tag](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="10857-505">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="10857-506">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="10857-506">Run the app.</span></span> <span data-ttu-id="10857-507">Přejděte na stránku indexu studentů.</span><span class="sxs-lookup"><span data-stu-id="10857-507">Navigate to the Students Index page.</span></span> <span data-ttu-id="10857-508">Časy se už nezobrazují.</span><span class="sxs-lookup"><span data-stu-id="10857-508">Times are no longer displayed.</span></span> <span data-ttu-id="10857-509">Každé zobrazení, které používá `Student` model, zobrazuje datum bez času.</span><span class="sxs-lookup"><span data-stu-id="10857-509">Every view that uses the `Student` model displays the date without time.</span></span>

![Stránka indexu studentů zobrazující data bez časů](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="10857-511">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="10857-511">The StringLength attribute</span></span>

<span data-ttu-id="10857-512">Pravidla ověřování dat a chybové zprávy ověřování lze zadat pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="10857-512">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="10857-513">Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli.</span><span class="sxs-lookup"><span data-stu-id="10857-513">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="10857-514">`StringLength` Atribut také poskytuje ověřování na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="10857-514">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="10857-515">Minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="10857-515">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="10857-516">Aktualizujte `Student` model pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="10857-516">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="10857-517">Předchozí kód omezuje názvy na více než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="10857-517">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="10857-518">`StringLength` Atribut nebrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="10857-518">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="10857-519">Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) se používá k aplikování omezení na vstup.</span><span class="sxs-lookup"><span data-stu-id="10857-519">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="10857-520">Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:</span><span class="sxs-lookup"><span data-stu-id="10857-520">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="10857-521">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="10857-521">Run the app:</span></span>

* <span data-ttu-id="10857-522">Přejděte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="10857-522">Navigate to the Students page.</span></span>
* <span data-ttu-id="10857-523">Vyberte **vytvořit novou**a zadejte název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="10857-523">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="10857-524">Vyberte **vytvořit**, ověřování na straně klienta zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="10857-524">Select **Create**, client-side validation shows an error message.</span></span>

![Stránka indexu studentů zobrazující chyby délky řetězce](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="10857-526">V **Průzkumník objektů systému SQL Server** (SSOX) otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .</span><span class="sxs-lookup"><span data-stu-id="10857-526">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů v SSOX před migracemi](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="10857-528">Na předchozím obrázku je znázorněno schéma pro `Student` tabulku.</span><span class="sxs-lookup"><span data-stu-id="10857-528">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="10857-529">Pole název mají typ `nvarchar(MAX)` , protože migrace nejsou v databázi spuštěny.</span><span class="sxs-lookup"><span data-stu-id="10857-529">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="10857-530">Po spuštění migrace později v tomto kurzu se změní `nvarchar(50)`pole název.</span><span class="sxs-lookup"><span data-stu-id="10857-530">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="10857-531">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="10857-531">The Column attribute</span></span>

<span data-ttu-id="10857-532">Atributy mohou řídit způsob, jakým jsou třídy a vlastnosti mapovány na databázi.</span><span class="sxs-lookup"><span data-stu-id="10857-532">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="10857-533">V této části `Column` se atribut používá pro mapování názvu `FirstMidName` vlastnosti na "FirstName" v databázi.</span><span class="sxs-lookup"><span data-stu-id="10857-533">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="10857-534">Při vytvoření databáze se názvy vlastností v modelu používají pro názvy sloupců (kromě případu, `Column` kdy se atribut používá).</span><span class="sxs-lookup"><span data-stu-id="10857-534">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="10857-535">`Student` Model používá`FirstMidName` pole pro první název, protože pole může obsahovat také prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="10857-535">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="10857-536">Aktualizujte soubor *student.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="10857-536">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="10857-537">`Student.FirstMidName` V předchozí změně se v aplikaci mapuje `FirstName` na sloupec `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="10857-537">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="10857-538">Přidání `Column` atributu změní model `SchoolContext`zálohování.</span><span class="sxs-lookup"><span data-stu-id="10857-538">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="10857-539">Model, který `SchoolContext` zálohování již nevyhovuje, se neshoduje s databází.</span><span class="sxs-lookup"><span data-stu-id="10857-539">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="10857-540">Pokud je aplikace spuštěná před použitím migrace, vygeneruje se tato výjimka:</span><span class="sxs-lookup"><span data-stu-id="10857-540">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="10857-541">Aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="10857-541">To update the DB:</span></span>

* <span data-ttu-id="10857-542">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="10857-542">Build the project.</span></span>
* <span data-ttu-id="10857-543">Otevřete okno příkazového řádku ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="10857-543">Open a command window in the project folder.</span></span> <span data-ttu-id="10857-544">Zadáním následujících příkazů vytvořte novou migraci a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="10857-544">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="10857-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10857-545">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="10857-546">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10857-546">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="10857-547">`migrations add ColumnFirstName` Příkaz vygeneruje následující varovnou zprávu:</span><span class="sxs-lookup"><span data-stu-id="10857-547">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="10857-548">Upozornění je vygenerováno, protože pole s názvem jsou nyní omezena na 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="10857-548">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="10857-549">Pokud má název v databázi více než 50 znaků, ztratí se 51 na poslední znak.</span><span class="sxs-lookup"><span data-stu-id="10857-549">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="10857-550">Otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="10857-550">Test the app.</span></span>

<span data-ttu-id="10857-551">Otevřete tabulku student v SSOX:</span><span class="sxs-lookup"><span data-stu-id="10857-551">Open the Student table in SSOX:</span></span>

![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="10857-553">Před použitím migrace byly sloupce názvu typu [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="10857-553">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="10857-554">Sloupce názvů jsou nyní `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="10857-554">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="10857-555">Změnil se název sloupce z `FirstMidName` na. `FirstName`</span><span class="sxs-lookup"><span data-stu-id="10857-555">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="10857-556">V následující části sestavení aplikace v některých fázích generuje chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="10857-556">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="10857-557">Pokyny určují, kdy se má aplikace sestavit.</span><span class="sxs-lookup"><span data-stu-id="10857-557">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="10857-558">Aktualizace entity studenta</span><span class="sxs-lookup"><span data-stu-id="10857-558">Student entity update</span></span>

![Entita studenta](complex-data-model/_static/student-entity.png)

<span data-ttu-id="10857-560">Aktualizujte *modely/student. cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-560">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="10857-561">Požadovaný atribut</span><span class="sxs-lookup"><span data-stu-id="10857-561">The Required attribute</span></span>

<span data-ttu-id="10857-562">`Required` Atribut nastaví název vlastnosti povinná pole.</span><span class="sxs-lookup"><span data-stu-id="10857-562">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="10857-563">Atribut není potřebný pro typy, které neumožňují hodnotu null, jako jsou`DateTime`typy hodnot `double`(, `int`, atd.). `Required`</span><span class="sxs-lookup"><span data-stu-id="10857-563">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="10857-564">Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="10857-564">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="10857-565">Atribut může být nahrazen parametrem minimální délky `StringLength` v atributu: `Required`</span><span class="sxs-lookup"><span data-stu-id="10857-565">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="10857-566">Atribut zobrazení</span><span class="sxs-lookup"><span data-stu-id="10857-566">The Display attribute</span></span>

<span data-ttu-id="10857-567">`Display` Atribut určuje, že titulek pro textová pole by měl být "jméno", "příjmení", "celé jméno" a "datum zápisu".</span><span class="sxs-lookup"><span data-stu-id="10857-567">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="10857-568">Výchozí titulky neobsahovaly místo dělení slov, například "LastName".</span><span class="sxs-lookup"><span data-stu-id="10857-568">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="10857-569">Vypočítaná vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="10857-569">The FullName calculated property</span></span>

<span data-ttu-id="10857-570">`FullName`je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="10857-570">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="10857-571">`FullName`nelze ji nastavit, má pouze přistupující objekt get.</span><span class="sxs-lookup"><span data-stu-id="10857-571">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="10857-572">V `FullName` databázi není vytvořen žádný sloupec.</span><span class="sxs-lookup"><span data-stu-id="10857-572">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="10857-573">Vytvořit entitu instruktora</span><span class="sxs-lookup"><span data-stu-id="10857-573">Create the Instructor Entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="10857-575">Vytvořte *modely/Instructor. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-575">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="10857-576">Více atributů může být na jednom řádku.</span><span class="sxs-lookup"><span data-stu-id="10857-576">Multiple attributes can be on one line.</span></span> <span data-ttu-id="10857-577">`HireDate` Atributy mohou být zapsány následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="10857-577">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="10857-578">Navigační vlastnosti CourseAssignments a OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="10857-578">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="10857-579">Vlastnosti `CourseAssignments` a`OfficeAssignment` jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="10857-579">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="10857-580">Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.</span><span class="sxs-lookup"><span data-stu-id="10857-580">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="10857-581">Pokud navigační vlastnost obsahuje více entit:</span><span class="sxs-lookup"><span data-stu-id="10857-581">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="10857-582">Musí se jednat o typ seznamu, kde je možné přidávat, odstraňovat a aktualizovat položky.</span><span class="sxs-lookup"><span data-stu-id="10857-582">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="10857-583">Mezi typy vlastností navigace patří:</span><span class="sxs-lookup"><span data-stu-id="10857-583">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="10857-584">Pokud `ICollection<T>` je zadáno, EF Core `HashSet<T>` vytvoří kolekci ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="10857-584">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="10857-585">`CourseAssignment` Entita je vysvětlena v části u vztahů m:n.</span><span class="sxs-lookup"><span data-stu-id="10857-585">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="10857-586">Firemní pravidla společnosti Contoso vysokých škol, že vyučující může mít maximálně jednu kancelář.</span><span class="sxs-lookup"><span data-stu-id="10857-586">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="10857-587">Vlastnost obsahuje jednu `OfficeAssignment`entitu. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="10857-587">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="10857-588">`OfficeAssignment`má hodnotu null, pokud není přiřazen žádný systém Office.</span><span class="sxs-lookup"><span data-stu-id="10857-588">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="10857-589">Vytvoření entity OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="10857-589">Create the OfficeAssignment entity</span></span>

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="10857-591">Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-591">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="10857-592">Klíčový atribut</span><span class="sxs-lookup"><span data-stu-id="10857-592">The Key attribute</span></span>

<span data-ttu-id="10857-593">`[Key]` Atribut slouží k identifikaci vlastnosti jako primárního klíče (PK), pokud je název vlastnosti něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="10857-593">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="10857-594">Mezi `Instructor` entitami a `OfficeAssignment` je relace 1:1 nebo jedna.</span><span class="sxs-lookup"><span data-stu-id="10857-594">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="10857-595">Přiřazení kanceláře existuje jenom ve vztahu k instruktorovi, ke kterému je přiřazený.</span><span class="sxs-lookup"><span data-stu-id="10857-595">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="10857-596">PK je také jeho cizí klíč (FK) `Instructor` k entitě. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="10857-596">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="10857-597">EF Core nemůže automaticky rozpoznat `InstructorID` jako PK z těchto `OfficeAssignment` důvodů:</span><span class="sxs-lookup"><span data-stu-id="10857-597">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="10857-598">`InstructorID`nedodržuje konvence pojmenování ID nebo classnameID.</span><span class="sxs-lookup"><span data-stu-id="10857-598">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="10857-599">Proto atribut slouží k identifikaci `InstructorID` jako PK: `Key`</span><span class="sxs-lookup"><span data-stu-id="10857-599">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="10857-600">Ve výchozím nastavení EF Core považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.</span><span class="sxs-lookup"><span data-stu-id="10857-600">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="10857-601">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="10857-601">The Instructor navigation property</span></span>

<span data-ttu-id="10857-602">Vlastnost navigace pro entitu `Instructor` může mít hodnotu null, protože: `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="10857-602">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="10857-603">Typy odkazů (například třídy mohou mít hodnotu null).</span><span class="sxs-lookup"><span data-stu-id="10857-603">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="10857-604">Instruktor nemusí mít přiřazení kanceláře.</span><span class="sxs-lookup"><span data-stu-id="10857-604">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="10857-605">Entita `OfficeAssignment` má vlastnost navigace, která neumožňuje hodnotu null `Instructor` , protože:</span><span class="sxs-lookup"><span data-stu-id="10857-605">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="10857-606">`InstructorID`hodnota nemůže být null.</span><span class="sxs-lookup"><span data-stu-id="10857-606">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="10857-607">Přiřazení kanceláře nemůže existovat bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="10857-607">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="10857-608">Pokud má `OfficeAssignment` entita související entitu, Každá entita má odkaz na jinou entitu v její navigační vlastnosti. `Instructor`</span><span class="sxs-lookup"><span data-stu-id="10857-608">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="10857-609">Atribut lze použít `Instructor` pro navigační vlastnost: `[Required]`</span><span class="sxs-lookup"><span data-stu-id="10857-609">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="10857-610">Předchozí kód určuje, že musí existovat související instruktor.</span><span class="sxs-lookup"><span data-stu-id="10857-610">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="10857-611">Předchozí kód není potřebný, protože `InstructorID` cizí klíč (což je také klíč PK) je nepovoluje hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="10857-611">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="10857-612">Úprava entity kurzu</span><span class="sxs-lookup"><span data-stu-id="10857-612">Modify the Course Entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="10857-614">Aktualizujte *modely/Course. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="10857-614">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="10857-615">Entita má vlastnost `DepartmentID`cizího klíče (FK). `Course`</span><span class="sxs-lookup"><span data-stu-id="10857-615">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="10857-616">`DepartmentID`odkazuje na související `Department` entitu.</span><span class="sxs-lookup"><span data-stu-id="10857-616">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="10857-617">`Course` Entita má vlastnost navigace. `Department`</span><span class="sxs-lookup"><span data-stu-id="10857-617">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="10857-618">EF Core nevyžaduje vlastnost FK pro datový model, pokud model má vlastnost navigace pro související entitu.</span><span class="sxs-lookup"><span data-stu-id="10857-618">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="10857-619">EF Core v databázi automaticky vytvoří FKs bez ohledu na to, kde jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="10857-619">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="10857-620">EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automatické vytváření FKs.</span><span class="sxs-lookup"><span data-stu-id="10857-620">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="10857-621">Pokud se v datovém modelu nachází FK, může být aktualizace jednodušší a efektivnější.</span><span class="sxs-lookup"><span data-stu-id="10857-621">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="10857-622">Zvažte například model, ve kterém *není obsažena* vlastnost `DepartmentID` FK.</span><span class="sxs-lookup"><span data-stu-id="10857-622">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="10857-623">Když se načte entita kurzu, která se upraví:</span><span class="sxs-lookup"><span data-stu-id="10857-623">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="10857-624">`Department` Entita má hodnotu null, pokud není explicitně načtena.</span><span class="sxs-lookup"><span data-stu-id="10857-624">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="10857-625">Chcete-li aktualizovat entitu kurzu `Department` , je nutné nejprve načíst entitu.</span><span class="sxs-lookup"><span data-stu-id="10857-625">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="10857-626">Pokud je vlastnost `DepartmentID` FK obsažena v datovém modelu, není nutné `Department` načíst entitu před aktualizací.</span><span class="sxs-lookup"><span data-stu-id="10857-626">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="10857-627">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="10857-627">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="10857-628">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` Atribut určuje, zda je v rámci aplikace poskytnuta PK místo vygenerovaného databází.</span><span class="sxs-lookup"><span data-stu-id="10857-628">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="10857-629">Ve výchozím nastavení EF Core předpokládá, že jsou hodnoty PK generovány databází.</span><span class="sxs-lookup"><span data-stu-id="10857-629">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="10857-630">DATABÁZE vygenerovala hodnoty PK většinou nejlepšího přístupu.</span><span class="sxs-lookup"><span data-stu-id="10857-630">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="10857-631">Pro `Course` entity určuje uživatel PK.</span><span class="sxs-lookup"><span data-stu-id="10857-631">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="10857-632">Například číslo kurzu, jako je například série 1000 pro matematické oddělení, série 2000 pro národní oddělení.</span><span class="sxs-lookup"><span data-stu-id="10857-632">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="10857-633">`DatabaseGenerated` Atribut lze také použít ke generování výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="10857-633">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="10857-634">DATABÁZE může například automaticky vygenerovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován.</span><span class="sxs-lookup"><span data-stu-id="10857-634">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="10857-635">Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="10857-635">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="10857-636">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="10857-636">Foreign key and navigation properties</span></span>

<span data-ttu-id="10857-637">Vlastnosti cizího klíče (FK) a navigační vlastnosti v `Course` entitě odráží následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="10857-637">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="10857-638">Kurz se přiřadí jednomu oddělení, takže je k dispozici `DepartmentID` FK `Department` a navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="10857-638">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="10857-639">V rámci kurzu může být zaregistrované několik studentů, takže `Enrollments` navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="10857-639">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="10857-640">Kurz může být výukou více instruktorů, takže `CourseAssignments` navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="10857-640">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="10857-641">`CourseAssignment`je vysvětleno [později](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="10857-641">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="10857-642">Vytvořit entitu oddělení</span><span class="sxs-lookup"><span data-stu-id="10857-642">Create the Department entity</span></span>

![Entita oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="10857-644">Vytvořte *modely/oddělení. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-644">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="10857-645">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="10857-645">The Column attribute</span></span>

<span data-ttu-id="10857-646">Dřív se `Column` použil atribut pro změnu mapování názvu sloupce.</span><span class="sxs-lookup"><span data-stu-id="10857-646">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="10857-647">V kódu pro `Department` entitu `Column` se atribut používá ke změně mapování datových typů SQL.</span><span class="sxs-lookup"><span data-stu-id="10857-647">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="10857-648">`Budget` Sloupec je definovaný pomocí SQL Server peněžního typu v databázi:</span><span class="sxs-lookup"><span data-stu-id="10857-648">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="10857-649">Mapování sloupce není obecně vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="10857-649">Column mapping is generally not required.</span></span> <span data-ttu-id="10857-650">EF Core všeobecně vybere vhodný SQL Server datový typ založený na typu CLR pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="10857-650">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="10857-651">Typ CLR `decimal` se mapuje na typ SQL Server `decimal` .</span><span class="sxs-lookup"><span data-stu-id="10857-651">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="10857-652">`Budget`je pro měnu a datový typ Money je pro měnu vhodný.</span><span class="sxs-lookup"><span data-stu-id="10857-652">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="10857-653">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="10857-653">Foreign key and navigation properties</span></span>

<span data-ttu-id="10857-654">Vlastnosti FK a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="10857-654">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="10857-655">Oddělení může nebo nemusí mít správce.</span><span class="sxs-lookup"><span data-stu-id="10857-655">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="10857-656">Správce je vždy instruktor.</span><span class="sxs-lookup"><span data-stu-id="10857-656">An administrator is always an instructor.</span></span> <span data-ttu-id="10857-657">Proto je `Instructor` vlastnost obsažena jako FK pro entitu. `InstructorID`</span><span class="sxs-lookup"><span data-stu-id="10857-657">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="10857-658">Navigační vlastnost má název `Administrator` , ale `Instructor` obsahuje entitu:</span><span class="sxs-lookup"><span data-stu-id="10857-658">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="10857-659">Otazník (?) v předchozím kódu určuje vlastnost s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="10857-659">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="10857-660">Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:</span><span class="sxs-lookup"><span data-stu-id="10857-660">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="10857-661">Poznámka: Podle konvence EF Core povoluje kaskádové odstranění pro FKs, která nejsou null a pro relace m:n.</span><span class="sxs-lookup"><span data-stu-id="10857-661">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="10857-662">Kaskádové odstranění může mít za následek cyklické Kaskádové odstraňování pravidel.</span><span class="sxs-lookup"><span data-stu-id="10857-662">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="10857-663">Cyklická kaskádová odstranění pravidel způsobí výjimku při přidání migrace.</span><span class="sxs-lookup"><span data-stu-id="10857-663">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="10857-664">Pokud byla například `Department.InstructorID` vlastnost definována jako nepovolená hodnota null:</span><span class="sxs-lookup"><span data-stu-id="10857-664">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="10857-665">EF Core konfiguruje pravidlo kaskádového odstranění, které odstraní oddělení při odstranění instruktora.</span><span class="sxs-lookup"><span data-stu-id="10857-665">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="10857-666">Odstranění oddělení, když se odstraní instruktor, není zamýšleným chováním.</span><span class="sxs-lookup"><span data-stu-id="10857-666">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="10857-667">Následující [rozhraní Fluent API](#fluent-api-alternative-to-attributes) by místo kaskádového nastavilo pravidlo omezení.</span><span class="sxs-lookup"><span data-stu-id="10857-667">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="10857-668">Předchozí kód zakáže kaskádové odstranění na základě vztahu oddělení a instruktor.</span><span class="sxs-lookup"><span data-stu-id="10857-668">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="10857-669">Aktualizace entity registrace</span><span class="sxs-lookup"><span data-stu-id="10857-669">Update the Enrollment entity</span></span>

<span data-ttu-id="10857-670">Záznam zápisu je pro jeden kurz, který přijímá jeden student.</span><span class="sxs-lookup"><span data-stu-id="10857-670">An enrollment record is for one course taken by one student.</span></span>

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="10857-672">Aktualizujte *modely/zápis. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-672">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="10857-673">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="10857-673">Foreign key and navigation properties</span></span>

<span data-ttu-id="10857-674">Vlastnosti CK a vlastnosti navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="10857-674">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="10857-675">Záznam zápisu je pro jeden kurz, takže existuje `CourseID` vlastnost FK `Course` a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="10857-675">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="10857-676">Záznam zápisu je určen pro jednoho studenta, takže existuje `StudentID` vlastnost FK `Student` a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="10857-676">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="10857-677">Relace m:n</span><span class="sxs-lookup"><span data-stu-id="10857-677">Many-to-Many Relationships</span></span>

<span data-ttu-id="10857-678">Mezi `Student` entitami a `Course` existuje vztah m:n.</span><span class="sxs-lookup"><span data-stu-id="10857-678">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="10857-679">Entita funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi. `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="10857-679">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="10857-680">"S datovou částí" znamená `Enrollment` , že tabulka obsahuje další data kromě FKs pro Spojené tabulky (v tomto případě PK a `Grade`).</span><span class="sxs-lookup"><span data-stu-id="10857-680">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="10857-681">Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit.</span><span class="sxs-lookup"><span data-stu-id="10857-681">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="10857-682">(Tento diagram byl vygenerován pomocí [nástrojů EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6. x.</span><span class="sxs-lookup"><span data-stu-id="10857-682">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="10857-683">Vytvoření diagramu není součástí kurzu.)</span><span class="sxs-lookup"><span data-stu-id="10857-683">Creating the diagram isn't part of the tutorial.)</span></span>

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

<span data-ttu-id="10857-685">Každá čára relace má 1 na jednom konci a hvězdičku (\*) na druhé straně, která indikuje relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="10857-685">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="10857-686">Pokud tabulka neobsahovala informace o třídě, musí obsahovat pouze dvě FKs (`CourseID` a `StudentID`). `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="10857-686">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="10857-687">Tabulka JOIN typu m:n bez datové části se někdy označuje jako čistá spojovací tabulka (PJT).</span><span class="sxs-lookup"><span data-stu-id="10857-687">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="10857-688">Entity `Instructor` a`Course` mají relaci n:n pomocí tabulky Pure JOIN.</span><span class="sxs-lookup"><span data-stu-id="10857-688">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="10857-689">Poznámka: EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne.</span><span class="sxs-lookup"><span data-stu-id="10857-689">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="10857-690">Další informace najdete v tématu [relace m:n v EF Core 2,0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="10857-690">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="10857-691">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="10857-691">The CourseAssignment entity</span></span>

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="10857-693">Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="10857-693">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="10857-694">Instruktory do kurzů</span><span class="sxs-lookup"><span data-stu-id="10857-694">Instructor-to-Courses</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="10857-696">Vztah n:n od instruktora do více kurzů:</span><span class="sxs-lookup"><span data-stu-id="10857-696">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="10857-697">Vyžaduje tabulku JOIN, která musí být reprezentována sadou entit.</span><span class="sxs-lookup"><span data-stu-id="10857-697">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="10857-698">Je čistá spojovací tabulka (tabulka bez datové části).</span><span class="sxs-lookup"><span data-stu-id="10857-698">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="10857-699">Je běžné pojmenovat entitu `EntityName1EntityName2`JOIN.</span><span class="sxs-lookup"><span data-stu-id="10857-699">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="10857-700">Například tabulka pro spojení instruktora do kurzů, která používá tento model, je `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="10857-700">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="10857-701">Doporučujeme však použít název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="10857-701">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="10857-702">Modely dat začínají jednoduchým a roste.</span><span class="sxs-lookup"><span data-stu-id="10857-702">Data models start out simple and grow.</span></span> <span data-ttu-id="10857-703">Spojení bez datové části (PJTs) se často rozvíjejí, aby zahrnovalo datovou část.</span><span class="sxs-lookup"><span data-stu-id="10857-703">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="10857-704">Když začnete s popisným názvem entity, nemusíte při změně tabulky JOIN měnit název.</span><span class="sxs-lookup"><span data-stu-id="10857-704">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="10857-705">V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="10857-705">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="10857-706">Například knihy a zákazníci mohou být propojeny s entitou JOIN nazvanou hodnocení.</span><span class="sxs-lookup"><span data-stu-id="10857-706">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="10857-707">Pro relaci `CourseAssignment` n:n v instruktorech na více kurzů se používá přednost před `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="10857-707">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="10857-708">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="10857-708">Composite key</span></span>

<span data-ttu-id="10857-709">FKs nemohou mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="10857-709">FKs are not nullable.</span></span> <span data-ttu-id="10857-710">Dva FKs v `CourseAssignment` (`InstructorID` `CourseID` a`CourseAssignment` ) společně identifikují každý řádek tabulky.</span><span class="sxs-lookup"><span data-stu-id="10857-710">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="10857-711">`CourseAssignment`nevyžaduje vyhrazený PK.</span><span class="sxs-lookup"><span data-stu-id="10857-711">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="10857-712">Vlastnosti `InstructorID` a`CourseID` fungují jako složené PK.</span><span class="sxs-lookup"><span data-stu-id="10857-712">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="10857-713">Jediným způsobem, jak zadat složené PKs EF Core je s *rozhraním API Fluent*.</span><span class="sxs-lookup"><span data-stu-id="10857-713">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="10857-714">V další části se dozvíte, jak nakonfigurovat složený PK.</span><span class="sxs-lookup"><span data-stu-id="10857-714">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="10857-715">Složený klíč zajišťuje:</span><span class="sxs-lookup"><span data-stu-id="10857-715">The composite key ensures:</span></span>

* <span data-ttu-id="10857-716">Pro jeden kurz je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="10857-716">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="10857-717">Pro jednoho instruktora je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="10857-717">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="10857-718">Více řádků pro stejný instruktor a kurz není povoleno.</span><span class="sxs-lookup"><span data-stu-id="10857-718">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="10857-719">Entita `Enrollment` JOIN definuje vlastní PK, takže je možné duplikovat toto řazení.</span><span class="sxs-lookup"><span data-stu-id="10857-719">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="10857-720">Chcete-li zabránit těmto duplicitám:</span><span class="sxs-lookup"><span data-stu-id="10857-720">To prevent such duplicates:</span></span>

* <span data-ttu-id="10857-721">Přidejte do polí FK jedinečný index nebo</span><span class="sxs-lookup"><span data-stu-id="10857-721">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="10857-722">Nakonfigurujte `Enrollment` pomocí primárního složeného klíče `CourseAssignment`podobného.</span><span class="sxs-lookup"><span data-stu-id="10857-722">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="10857-723">Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="10857-723">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="10857-724">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="10857-724">Update the DB context</span></span>

<span data-ttu-id="10857-725">Do *data/SchoolContext. cs*přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="10857-725">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="10857-726">Předchozí kód přidá nové entity a nakonfiguruje `CourseAssignment` neseparovaný PK entity.</span><span class="sxs-lookup"><span data-stu-id="10857-726">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="10857-727">Alternativa k atributům rozhraní Fluent API</span><span class="sxs-lookup"><span data-stu-id="10857-727">Fluent API alternative to attributes</span></span>

<span data-ttu-id="10857-728">Metoda v předchozím kódu používá *rozhraní Fluent API* ke konfiguraci chování EF Core. `OnModelCreating`</span><span class="sxs-lookup"><span data-stu-id="10857-728">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="10857-729">Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu.</span><span class="sxs-lookup"><span data-stu-id="10857-729">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="10857-730">[Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem rozhraní Fluent API:</span><span class="sxs-lookup"><span data-stu-id="10857-730">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="10857-731">V tomto kurzu se rozhraní API Fluent používá jenom pro mapování DB, které nejde s atributy dělat.</span><span class="sxs-lookup"><span data-stu-id="10857-731">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="10857-732">Rozhraní API Fluent ale může určovat většinu pravidel formátování, ověřování a mapování, která se dají provádět s atributy.</span><span class="sxs-lookup"><span data-stu-id="10857-732">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="10857-733">Některé atributy, `MinimumLength` jako například, se nedají použít s rozhraním API Fluent.</span><span class="sxs-lookup"><span data-stu-id="10857-733">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="10857-734">`MinimumLength`nemění schéma, používá pouze ověřovací pravidlo minimální délky.</span><span class="sxs-lookup"><span data-stu-id="10857-734">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="10857-735">Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit".</span><span class="sxs-lookup"><span data-stu-id="10857-735">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="10857-736">Atributy a rozhraní API Fluent lze kombinovat.</span><span class="sxs-lookup"><span data-stu-id="10857-736">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="10857-737">Existují některé konfigurace, které lze provést pouze s rozhraním API Fluent (určením složeného PK).</span><span class="sxs-lookup"><span data-stu-id="10857-737">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="10857-738">Existují některé konfigurace, které lze provádět pouze s atributy (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="10857-738">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="10857-739">Doporučený postup pro použití rozhraní Fluent API nebo atributů:</span><span class="sxs-lookup"><span data-stu-id="10857-739">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="10857-740">Vyberte jednu z těchto dvou přístupů.</span><span class="sxs-lookup"><span data-stu-id="10857-740">Choose one of these two approaches.</span></span>
* <span data-ttu-id="10857-741">Používejte vybraný postup konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="10857-741">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="10857-742">Některé atributy použité v tomto kurzu se používají pro:</span><span class="sxs-lookup"><span data-stu-id="10857-742">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="10857-743">Pouze ověření (například `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="10857-743">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="10857-744">Pouze konfigurace EF Core (například `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="10857-744">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="10857-745">Konfigurace ověřování a EF Core (například `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="10857-745">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="10857-746">Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="10857-746">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="10857-747">Diagram entit znázorňující vztahy</span><span class="sxs-lookup"><span data-stu-id="10857-747">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="10857-748">Následující ilustrace znázorňuje diagram, který nástroje EF Power Tools vytvoří pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="10857-748">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="10857-750">Předchozí diagram znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="10857-750">The preceding diagram shows:</span></span>

* <span data-ttu-id="10857-751">Několik čar relací 1:1 (1 až \*).</span><span class="sxs-lookup"><span data-stu-id="10857-751">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="10857-752">Čára relace 1:1 (1 – 0.. 1) mezi `Instructor` entitami a. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="10857-752">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="10857-753">Čára relace nula až n-many (0.. 1 až ×) mezi `Instructor` entitami a. `Department`</span><span class="sxs-lookup"><span data-stu-id="10857-753">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="10857-754">Osazení databáze pomocí testovacích dat</span><span class="sxs-lookup"><span data-stu-id="10857-754">Seed the DB with Test Data</span></span>

<span data-ttu-id="10857-755">Aktualizujte kód v *data/DbInitializer. cs*:</span><span class="sxs-lookup"><span data-stu-id="10857-755">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="10857-756">Předchozí kód poskytuje počáteční data pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="10857-756">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="10857-757">Většina tohoto kódu vytváří nové objekty entit a načítá vzorová data.</span><span class="sxs-lookup"><span data-stu-id="10857-757">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="10857-758">Ukázková data se používají k testování.</span><span class="sxs-lookup"><span data-stu-id="10857-758">The sample data is used for testing.</span></span> <span data-ttu-id="10857-759">V `Enrollments` tématu `CourseAssignments` a najdete příklady, jak lze dosazení tabulek JOIN typu many.</span><span class="sxs-lookup"><span data-stu-id="10857-759">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="10857-760">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="10857-760">Add a migration</span></span>

<span data-ttu-id="10857-761">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="10857-761">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="10857-762">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10857-762">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="10857-763">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10857-763">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="10857-764">Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="10857-764">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="10857-765">Pokud se `database update` příkaz spustí, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="10857-765">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="10857-766">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="10857-766">Apply the migration</span></span>

<span data-ttu-id="10857-767">Teď, když máte existující databázi, musíte si představit, jak v nich použít budoucí změny.</span><span class="sxs-lookup"><span data-stu-id="10857-767">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="10857-768">V tomto kurzu se dozvíte dva přístupy:</span><span class="sxs-lookup"><span data-stu-id="10857-768">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="10857-769">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="10857-769">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="10857-770">[Použijte migraci na stávající databázi](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="10857-770">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="10857-771">I když je tato metoda složitější a časově náročná, jedná se o preferovaný přístup pro produkční prostředí z reálného světa.</span><span class="sxs-lookup"><span data-stu-id="10857-771">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="10857-772">**Poznámka:** Toto je volitelný oddíl tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="10857-772">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="10857-773">Můžete provést kroky odkládací a znovu vytvořit a tuto část přeskočit.</span><span class="sxs-lookup"><span data-stu-id="10857-773">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="10857-774">Pokud chcete postupovat podle kroků v této části, neprovádějte kroky odkládací a znovu vytvořit.</span><span class="sxs-lookup"><span data-stu-id="10857-774">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="10857-775">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="10857-775">Drop and re-create the database</span></span>

<span data-ttu-id="10857-776">Kód v aktualizovaném `DbInitializer` přidání počátečních dat pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="10857-776">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="10857-777">Pokud chcete vynutit EF Core vytvoření nové databáze, vyřaďte a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="10857-777">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="10857-778">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10857-778">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="10857-779">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="10857-779">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="10857-780">Pokud `Get-Help about_EntityFrameworkCore` chcete získat informace o nápovědě, spusťte z PMC.</span><span class="sxs-lookup"><span data-stu-id="10857-780">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="10857-781">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10857-781">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="10857-782">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="10857-782">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="10857-783">Složka projektu obsahuje soubor *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="10857-783">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="10857-784">V příkazovém okně zadejte následující:</span><span class="sxs-lookup"><span data-stu-id="10857-784">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

<span data-ttu-id="10857-785">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="10857-785">Run the app.</span></span> <span data-ttu-id="10857-786">Spuštění aplikace spustí `DbInitializer.Initialize` metodu.</span><span class="sxs-lookup"><span data-stu-id="10857-786">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="10857-787">`DbInitializer.Initialize` Naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="10857-787">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="10857-788">Otevřete databázi v SSOX:</span><span class="sxs-lookup"><span data-stu-id="10857-788">Open the DB in SSOX:</span></span>

* <span data-ttu-id="10857-789">Pokud jste dříve otevřeli SSOX, klikněte na tlačítko **aktualizovat** .</span><span class="sxs-lookup"><span data-stu-id="10857-789">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="10857-790">Rozbalte **tabulky** uzlu.</span><span class="sxs-lookup"><span data-stu-id="10857-790">Expand the **Tables** node.</span></span> <span data-ttu-id="10857-791">Zobrazí se vytvořené tabulky.</span><span class="sxs-lookup"><span data-stu-id="10857-791">The created tables are displayed.</span></span>

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="10857-793">Projděte si tabulku **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="10857-793">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="10857-794">Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data**.</span><span class="sxs-lookup"><span data-stu-id="10857-794">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="10857-795">Ověřte, že tabulka **CourseAssignment** obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="10857-795">Verify the **CourseAssignment** table contains data.</span></span>

![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="10857-797">Použít migraci na existující databázi</span><span class="sxs-lookup"><span data-stu-id="10857-797">Apply the migration to the existing database</span></span>

<span data-ttu-id="10857-798">Tato část je volitelná.</span><span class="sxs-lookup"><span data-stu-id="10857-798">This section is optional.</span></span> <span data-ttu-id="10857-799">Tyto kroky fungují pouze v případě, že jste přeskočili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="10857-799">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="10857-800">Pokud jsou migrace spouštěny s existujícími daty, může dojít k omezením FK, která nesplňují stávající data.</span><span class="sxs-lookup"><span data-stu-id="10857-800">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="10857-801">S provozními daty je potřeba provést kroky pro migraci stávajících dat.</span><span class="sxs-lookup"><span data-stu-id="10857-801">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="10857-802">V této části najdete příklad opravy porušení omezení CK.</span><span class="sxs-lookup"><span data-stu-id="10857-802">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="10857-803">Neprovádějte změny kódu bez zálohy.</span><span class="sxs-lookup"><span data-stu-id="10857-803">Don't make these code changes without a backup.</span></span> <span data-ttu-id="10857-804">Neprovádějte změny kódu, pokud jste dokončili předchozí oddíl a aktualizovali databázi.</span><span class="sxs-lookup"><span data-stu-id="10857-804">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="10857-805">Soubor *{timestamp} _ComplexDataModel. cs* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="10857-805">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="10857-806">Předchozí kód přidá `Course` do tabulky nenulovou hodnotu `DepartmentID` typu FK.</span><span class="sxs-lookup"><span data-stu-id="10857-806">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="10857-807">Databáze z předchozího kurzu obsahuje řádky v `Course`, takže nelze aktualizovat tabulku pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="10857-807">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="10857-808">Chcete-li migrovat práci s existujícími daty: `ComplexDataModel`</span><span class="sxs-lookup"><span data-stu-id="10857-808">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="10857-809">Změňte kód tak, aby nový sloupec (`DepartmentID`) poskytl výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="10857-809">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="10857-810">Vytvořte falešné oddělení s názvem "Temp", které bude sloužit jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="10857-810">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="10857-811">Oprava omezení cizího klíče</span><span class="sxs-lookup"><span data-stu-id="10857-811">Fix the foreign key constraints</span></span>

<span data-ttu-id="10857-812">Aktualizujte metodu `Up`třídy: `ComplexDataModel`</span><span class="sxs-lookup"><span data-stu-id="10857-812">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="10857-813">Otevřete soubor *{timestamp} _ComplexDataModel. cs* .</span><span class="sxs-lookup"><span data-stu-id="10857-813">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="10857-814">Odkomentujte řádek kódu, který přidá `DepartmentID` sloupec `Course` do tabulky.</span><span class="sxs-lookup"><span data-stu-id="10857-814">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="10857-815">Přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="10857-815">Add the following highlighted code.</span></span> <span data-ttu-id="10857-816">Nový kód přejde za `.CreateTable( name: "Department"` blok:</span><span class="sxs-lookup"><span data-stu-id="10857-816">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="10857-817">V předchozích změnách budou existující `Course` řádky při spuštění `Up` metody v relaci s "dočasným" oddělením `ComplexDataModel` .</span><span class="sxs-lookup"><span data-stu-id="10857-817">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="10857-818">Produkční aplikace by:</span><span class="sxs-lookup"><span data-stu-id="10857-818">A production app would:</span></span>

* <span data-ttu-id="10857-819">Zahrnout kód nebo skripty pro přidání `Department` řádků a souvisejících `Course` řádků do nových `Department` řádků.</span><span class="sxs-lookup"><span data-stu-id="10857-819">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="10857-820">Nepoužívejte oddělení "dočasné" nebo výchozí hodnotu pro `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="10857-820">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="10857-821">Další kurz se zabývá souvisejícími daty.</span><span class="sxs-lookup"><span data-stu-id="10857-821">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10857-822">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="10857-822">Additional resources</span></span>

* [<span data-ttu-id="10857-823">Verze tohoto kurzu pro YouTube (část 1)</span><span class="sxs-lookup"><span data-stu-id="10857-823">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="10857-824">Verze tohoto kurzu pro YouTube (část 2)</span><span class="sxs-lookup"><span data-stu-id="10857-824">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="10857-825">[Předchozí](xref:data/ef-rp/migrations)Další
> [](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="10857-825">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
