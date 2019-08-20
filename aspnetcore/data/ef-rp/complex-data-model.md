---
title: Razor Pages s EF Core v ASP.NET Core-datový model 5 z 8
author: tdykstra
description: V tomto kurzu přidejte další entity a vztahy a upravte datový model zadáním formátování, ověřování a pravidel mapování.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 8a1c0759453b02f4ce1c45471a8f93da626f8261
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583284"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="c1ff3-103">Razor Pages s EF Core v ASP.NET Core-datový model 5 z 8</span><span class="sxs-lookup"><span data-stu-id="c1ff3-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="c1ff3-104">Podle [Petr Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c1ff3-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1ff3-105">Předchozí kurzy pracovaly se základním datovým modelem, který se skládá ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="c1ff3-106">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-106">In this tutorial:</span></span>

* <span data-ttu-id="c1ff3-107">Přidávají se další entity a vztahy.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="c1ff3-108">Datový model je přizpůsoben zadáním pravidel formátování, ověřování a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="c1ff3-109">Dokončený datový model je zobrazený na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-109">The completed data model is shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="c1ff3-111">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="c1ff3-111">The Student entity</span></span>

![Entita studenta](complex-data-model/_static/student-entity.png)

<span data-ttu-id="c1ff3-113">Nahraďte kód v *modelů/student. cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="c1ff3-114">Předchozí kód přidá `FullName` vlastnost a přidá následující atributy do existujících vlastností:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="c1ff3-115">Vypočítaná vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="c1ff3-115">The FullName calculated property</span></span>

<span data-ttu-id="c1ff3-116">`FullName`je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="c1ff3-117">`FullName`nedá se nastavit, takže má jenom přístup Get.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="c1ff3-118">V `FullName` databázi není vytvořen žádný sloupec.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="c1ff3-119">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="c1ff3-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="c1ff3-120">Pro data o registraci studenta aktuálně zobrazují všechny stránky denní dobu a datum, i když se jedná o relevantní datum.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="c1ff3-121">Pomocí atributů datových poznámek můžete vytvořit jednu změnu kódu, která bude opravovat formát zobrazení na každé stránce, která zobrazuje data.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="c1ff3-122">Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) Určuje datový typ, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="c1ff3-123">V tomto případě by se měla zobrazit pouze datum, nikoli datum a čas.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="c1ff3-124">[Výčet DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress atd. `DataType` Atribut také může aplikaci povolit automatické poskytování funkcí specifických pro typ.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="c1ff3-125">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-125">For example:</span></span>

* <span data-ttu-id="c1ff3-126">Automaticky se vytvoří `DataType.EmailAddress`odkaz pro. `mailto:`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="c1ff3-127">Selektor data je k `DataType.Date` dispozici ve většině prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="c1ff3-128">Atribut emituje atributy HTML 5 `data-` (vyslovení data pomlčky). `DataType`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="c1ff3-129">`DataType` Atributy neposkytují ověřování.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="c1ff3-130">Atribut DisplayFormat</span><span class="sxs-lookup"><span data-stu-id="c1ff3-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="c1ff3-131">`DataType.Date`neurčuje formát data, které se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="c1ff3-132">Ve výchozím nastavení se pole Datum zobrazuje v závislosti na výchozích formátech na základě objektu [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serveru.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="c1ff3-133">`DisplayFormat` Atribut slouží k explicitnímu zadání formátu data.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="c1ff3-134">Toto `ApplyFormatInEditMode` nastavení určuje, že by mělo být formátování použito také na uživatelské rozhraní pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="c1ff3-135">Některá pole byste neměli `ApplyFormatInEditMode`používat.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="c1ff3-136">Například symbol měny by neměl být v textovém poli pro úpravy obvykle zobrazen.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="c1ff3-137">`DisplayFormat` Atribut může používat sám sebe.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="c1ff3-138">Obecně je vhodné použít `DataType` atribut `DisplayFormat` s atributem.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="c1ff3-139">`DataType` Atribut předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="c1ff3-140">Atribut poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`: `DataType`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="c1ff3-141">Prohlížeč může povolit funkce HTML5.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="c1ff3-142">Například můžete zobrazit ovládací prvek kalendáře, symbol měny odpovídající národním prostředí, e-mailové odkazy a ověření vstupu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="c1ff3-143">Ve výchozím nastavení prohlížeč vykresluje data pomocí správného formátu založeného na národním prostředí.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="c1ff3-144">Další informace najdete v [ \<dokumentaci k rutině Input > Tag](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="c1ff3-145">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="c1ff3-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="c1ff3-146">Pravidla ověřování dat a chybové zprávy ověřování lze zadat pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="c1ff3-147">Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="c1ff3-148">Zobrazený kód omezuje názvy na více než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="c1ff3-149">Příklad, který nastaví minimální délku řetězce, se zobrazí [později](#the-required-attribute).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="c1ff3-150">`StringLength` Atribut také poskytuje ověřování na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="c1ff3-151">Minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="c1ff3-152">`StringLength` Atribut nebrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="c1ff3-153">Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) lze použít k uplatnění omezení na vstup.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="c1ff3-154">Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1ff3-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1ff3-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1ff3-156">V **Průzkumník objektů systému SQL Server** (SSOX) otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů v SSOX před migracemi](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="c1ff3-158">Na předchozím obrázku je znázorněno schéma pro `Student` tabulku.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="c1ff3-159">Pole název mají typ `nvarchar(MAX)`.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="c1ff3-160">Když se vytvoří migrace a použije se později v tomto kurzu, pole název se změní `nvarchar(50)` v důsledku atributů délky řetězce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c1ff3-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1ff3-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1ff3-162">V nástroji SQLite si Projděte definice sloupců pro `Student` tabulku.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="c1ff3-163">Pole název mají typ `Text`.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-163">The name fields have type `Text`.</span></span> <span data-ttu-id="c1ff3-164">Všimněte si, že je voláno `FirstMidName`pole jméno.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="c1ff3-165">V další části změníte název tohoto sloupce na `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="c1ff3-166">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="c1ff3-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="c1ff3-167">Atributy mohou řídit způsob, jakým jsou třídy a vlastnosti mapovány na databázi.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="c1ff3-168">V modelu je atribut použit k mapování názvu `FirstMidName` vlastnosti na hodnotu "FirstName" v databázi. `Column` `Student`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="c1ff3-169">Při vytvoření databáze se názvy vlastností v modelu používají pro názvy sloupců (kromě případu, `Column` kdy se atribut používá).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="c1ff3-170">`Student` Model používá`FirstMidName` pole pro první název, protože pole může obsahovat také prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="c1ff3-171">`[Column]` S `FirstName` `Student` atributem se vdatovémmodelumapuje`Student.FirstMidName` na sloupec tabulky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="c1ff3-172">Přidání `Column` atributu změní model `SchoolContext`zálohování.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="c1ff3-173">Model, který `SchoolContext` zálohování již nevyhovuje, se neshoduje s databází.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="c1ff3-174">Tato nesrovnalost bude vyřešena přidáním migrace později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="c1ff3-175">Požadovaný atribut</span><span class="sxs-lookup"><span data-stu-id="c1ff3-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="c1ff3-176">`Required` Atribut nastaví název vlastnosti povinná pole.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="c1ff3-177">Atribut není potřebný pro typy `DateTime`, `int`které neumožňují hodnotu null, jako jsou například typy hodnot (například, `double`, a). `Required`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="c1ff3-178">Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="c1ff3-179">Atribut může být nahrazen parametrem minimální délky `StringLength` v atributu: `Required`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-179">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="c1ff3-180">Atribut zobrazení</span><span class="sxs-lookup"><span data-stu-id="c1ff3-180">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="c1ff3-181">`Display` Atribut určuje, že titulek pro textová pole by měl být "jméno", "příjmení", "celé jméno" a "datum zápisu".</span><span class="sxs-lookup"><span data-stu-id="c1ff3-181">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="c1ff3-182">Výchozí titulky neobsahovaly místo dělení slov, například "LastName".</span><span class="sxs-lookup"><span data-stu-id="c1ff3-182">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="c1ff3-183">Vytvoření migrace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-183">Create a migration</span></span>

<span data-ttu-id="c1ff3-184">Spusťte aplikaci a pokračujte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-184">Run the app and go to the Students page.</span></span> <span data-ttu-id="c1ff3-185">Je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-185">An exception is thrown.</span></span> <span data-ttu-id="c1ff3-186">Atribut způsobí, že EF očekává, že najde sloupec s `FirstName`názvem, ale název sloupce v databázi je stále `FirstMidName`. `[Column]`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-186">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1ff3-187">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1ff3-187">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1ff3-188">Chybová zpráva je podobná následujícímu příkladu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-188">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="c1ff3-189">Do PMC zadejte následující příkazy, abyste vytvořili novou migraci a aktualizovali databázi:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-189">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="c1ff3-190">První z těchto příkazů generuje následující varovnou zprávu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-190">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="c1ff3-191">Upozornění je vygenerováno, protože pole s názvem jsou nyní omezena na 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-191">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="c1ff3-192">Pokud má název v databázi více než 50 znaků, bude ztraceno 51 k poslednímu znaku.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-192">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="c1ff3-193">Otevřete tabulku student v SSOX:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-193">Open the Student table in SSOX:</span></span>

  ![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="c1ff3-195">Před použitím migrace byly sloupce názvu typu [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-195">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="c1ff3-196">Sloupce názvů jsou nyní `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-196">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="c1ff3-197">Změnil se název sloupce z `FirstMidName` na. `FirstName`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-197">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c1ff3-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1ff3-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1ff3-199">Chybová zpráva je podobná následujícímu příkladu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-199">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="c1ff3-200">Otevřete okno příkazového řádku ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-200">Open a command window in the project folder.</span></span> <span data-ttu-id="c1ff3-201">Zadáním následujících příkazů vytvořte novou migraci a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-201">Enter the following commands to create a new migration and update the database:</span></span>

  ```console
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="c1ff3-202">Příkaz aktualizace databáze zobrazí chybu jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-202">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="c1ff3-203">Pro tento kurz se způsobem, jak se tato chyba zobrazí po této chybě, odstranit a znovu vytvořit počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-203">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="c1ff3-204">Další informace najdete v poznámkovém upozornění SQLite v horní části [kurzu migrace](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-204">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="c1ff3-205">Odstraňte složku *migrace* .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-205">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="c1ff3-206">Spusťte následující příkazy k vyřazení databáze, vytvořte novou počáteční migraci a použijte migraci:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-206">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```console
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="c1ff3-207">Projděte si tabulku student v nástroji SQLite.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-207">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="c1ff3-208">Sloupec, který byl FirstMidName, je nyní FirstName.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-208">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="c1ff3-209">Spusťte aplikaci a pokračujte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-209">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="c1ff3-210">Všimněte si, že časy nejsou vstupní ani se nezobrazují spolu s kalendářními daty.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-210">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="c1ff3-211">Vyberte **vytvořit novou**a zkuste zadat název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-211">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="c1ff3-212">V následujících oddílech sestavení aplikace v některých fázích generuje chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-212">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="c1ff3-213">Pokyny určují, kdy se má aplikace sestavit.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-213">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="c1ff3-214">Entita instruktor</span><span class="sxs-lookup"><span data-stu-id="c1ff3-214">The Instructor Entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="c1ff3-216">Vytvořte *modely/Instructor. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-216">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="c1ff3-217">Více atributů může být na jednom řádku.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-217">Multiple attributes can be on one line.</span></span> <span data-ttu-id="c1ff3-218">`HireDate` Atributy mohou být zapsány následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-218">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="c1ff3-219">Navigační vlastnosti</span><span class="sxs-lookup"><span data-stu-id="c1ff3-219">Navigation properties</span></span>

<span data-ttu-id="c1ff3-220">Vlastnosti `CourseAssignments` a`OfficeAssignment` jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-220">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="c1ff3-221">Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-221">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="c1ff3-222">Instruktor může mít maximálně jednu kancelář, takže `OfficeAssignment` vlastnost obsahuje jednu `OfficeAssignment` entitu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-222">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="c1ff3-223">`OfficeAssignment`má hodnotu null, pokud není přiřazen žádný systém Office.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-223">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="c1ff3-224">Entita OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="c1ff3-224">The OfficeAssignment entity</span></span>

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="c1ff3-226">Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-226">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="c1ff3-227">Klíčový atribut</span><span class="sxs-lookup"><span data-stu-id="c1ff3-227">The Key attribute</span></span>

<span data-ttu-id="c1ff3-228">`[Key]` Atribut slouží k identifikaci vlastnosti jako primárního klíče (PK), pokud je název vlastnosti něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-228">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="c1ff3-229">Mezi `Instructor` entitami a `OfficeAssignment` je relace 1:1 nebo jedna.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-229">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="c1ff3-230">Přiřazení kanceláře existuje jenom ve vztahu k instruktorovi, ke kterému je přiřazený.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-230">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="c1ff3-231">PK je také jeho cizí klíč (FK) `Instructor` k entitě. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-231">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="c1ff3-232">EF Core nemůže automaticky rozpoznat `InstructorID` jako `OfficeAssignment` PK, protože `InstructorID` nedodržuje konvence pojmenování ID nebo classnameID.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-232">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="c1ff3-233">Proto atribut slouží k identifikaci `InstructorID` jako PK: `Key`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-233">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="c1ff3-234">Ve výchozím nastavení EF Core považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-234">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="c1ff3-235">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="c1ff3-235">The Instructor navigation property</span></span>

<span data-ttu-id="c1ff3-236">Navigační vlastnost může mít hodnotu null, protože pro daný instruktor nemusí `OfficeAssignment` existovat řádek. `Instructor.OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-236">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="c1ff3-237">Instruktor nemusí mít přiřazení kanceláře.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-237">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="c1ff3-238">Navigační vlastnost bude mít vždycky entitu instruktora, protože typ cizího klíče `int` `InstructorID` je, typ hodnoty, která není null. `OfficeAssignment.Instructor`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-238">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="c1ff3-239">Přiřazení kanceláře nemůže existovat bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-239">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="c1ff3-240">Pokud má `OfficeAssignment` entita související entitu, Každá entita má odkaz na jinou entitu v její navigační vlastnosti. `Instructor`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-240">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="c1ff3-241">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="c1ff3-241">The Course Entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="c1ff3-243">Aktualizujte *modely/Course. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-243">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="c1ff3-244">Entita má vlastnost `DepartmentID`cizího klíče (FK). `Course`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-244">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="c1ff3-245">`DepartmentID`odkazuje na související `Department` entitu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-245">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="c1ff3-246">`Course` Entita má vlastnost navigace. `Department`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-246">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="c1ff3-247">EF Core nevyžaduje vlastnost cizího klíče pro datový model, pokud model má vlastnost navigace pro související entitu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-247">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="c1ff3-248">EF Core v databázi automaticky vytvoří FKs bez ohledu na to, kde jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-248">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="c1ff3-249">EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automatické vytváření FKs.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-249">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="c1ff3-250">Explicitní a efektivnější je však, že explicitně včetně FK v datovém modelu může zjednodušit a efektivněji dělat aktualizace.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-250">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="c1ff3-251">Zvažte například model, ve kterém není obsažena vlastnost `DepartmentID` FK .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-251">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="c1ff3-252">Když se načte entita kurzu, která se upraví:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-252">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="c1ff3-253">`Department` Vlastnost má hodnotu null, pokud není explicitně načtena.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-253">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="c1ff3-254">Chcete-li aktualizovat entitu kurzu `Department` , je nutné nejprve načíst entitu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-254">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="c1ff3-255">Pokud je vlastnost `DepartmentID` FK obsažena v datovém modelu, není nutné `Department` načíst entitu před aktualizací.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-255">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="c1ff3-256">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="c1ff3-256">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="c1ff3-257">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` Atribut určuje, zda je v rámci aplikace poskytnuta PK místo vygenerovaného databází.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-257">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="c1ff3-258">Ve výchozím nastavení EF Core předpokládá, že se hodnoty PK generují v databázi.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-258">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="c1ff3-259">Vygenerovaná databáze obvykle představuje nejlepší přístup.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-259">Database-generated is generally the best approach.</span></span> <span data-ttu-id="c1ff3-260">Pro `Course` entity určuje uživatel PK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-260">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="c1ff3-261">Například číslo kurzu, jako je například série 1000 pro matematické oddělení, série 2000 pro národní oddělení.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-261">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="c1ff3-262">`DatabaseGenerated` Atribut lze také použít ke generování výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-262">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="c1ff3-263">Databáze může například automaticky vygenerovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-263">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="c1ff3-264">Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-264">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c1ff3-265">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-265">Foreign key and navigation properties</span></span>

<span data-ttu-id="c1ff3-266">Vlastnosti cizího klíče (FK) a navigační vlastnosti v `Course` entitě odráží následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-266">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="c1ff3-267">Kurz se přiřadí jednomu oddělení, takže je k dispozici `DepartmentID` FK `Department` a navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-267">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="c1ff3-268">V rámci kurzu může být zaregistrované několik studentů, takže `Enrollments` navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-268">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="c1ff3-269">Kurz může být výukou více instruktorů, takže `CourseAssignments` navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-269">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="c1ff3-270">`CourseAssignment`je vysvětleno [později](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-270">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="c1ff3-271">Entita oddělení</span><span class="sxs-lookup"><span data-stu-id="c1ff3-271">The Department entity</span></span>

![Entita oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="c1ff3-273">Vytvořte *modely/oddělení. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-273">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="c1ff3-274">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="c1ff3-274">The Column attribute</span></span>

<span data-ttu-id="c1ff3-275">Dřív se `Column` použil atribut pro změnu mapování názvu sloupce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-275">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="c1ff3-276">V kódu pro `Department` entitu `Column` se atribut používá ke změně mapování datových typů SQL.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-276">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="c1ff3-277">`Budget` Sloupec je definovaný pomocí SQL Server peněžního typu v databázi:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-277">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="c1ff3-278">Mapování sloupce není obecně vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-278">Column mapping is generally not required.</span></span> <span data-ttu-id="c1ff3-279">EF Core zvolí příslušný datový typ SQL Server na základě typu CLR pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-279">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="c1ff3-280">Typ CLR `decimal` se mapuje na typ SQL Server `decimal` .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-280">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="c1ff3-281">`Budget`je pro měnu a datový typ Money je pro měnu vhodný.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-281">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c1ff3-282">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-282">Foreign key and navigation properties</span></span>

<span data-ttu-id="c1ff3-283">Vlastnosti FK a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-283">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="c1ff3-284">Oddělení může nebo nemusí mít správce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-284">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="c1ff3-285">Správce je vždy instruktor.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-285">An administrator is always an instructor.</span></span> <span data-ttu-id="c1ff3-286">Proto je `Instructor` vlastnost obsažena jako FK pro entitu. `InstructorID`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-286">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="c1ff3-287">Navigační vlastnost má název `Administrator` , ale `Instructor` obsahuje entitu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-287">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="c1ff3-288">Otazník (?) v předchozím kódu určuje vlastnost s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-288">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="c1ff3-289">Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-289">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="c1ff3-290">Podle konvence EF Core povoluje kaskádové odstranění pro FKs, která nejsou null a pro relace m:n.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-290">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="c1ff3-291">Toto výchozí chování může způsobit cyklické kaskády odstraňování pravidel.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-291">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="c1ff3-292">Cyklická kaskádová odstranění pravidel způsobují při přidání migrace výjimku.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-292">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="c1ff3-293">Pokud je `Department.InstructorID` například vlastnost definovaná jako nepovolená, EF Core by nakonfigurovala pravidlo kaskádového odstranění.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-293">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="c1ff3-294">V takovém případě by se oddělení odstranilo, když se odstraní instruktor jako jeho správce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-294">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="c1ff3-295">V tomto scénáři by pravidlo omezení mělo smysl.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-295">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="c1ff3-296">Následující rozhraní Fluent API by nastavilo pravidlo omezení a zakáže kaskádové odstranění.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-296">The following fluent API would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="c1ff3-297">Registrace entity</span><span class="sxs-lookup"><span data-stu-id="c1ff3-297">The Enrollment entity</span></span>

<span data-ttu-id="c1ff3-298">Záznam zápisu je pro jeden kurz, který přijímá jeden student.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-298">An enrollment record is for one course taken by one student.</span></span>

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="c1ff3-300">Aktualizujte *modely/zápis. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-300">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c1ff3-301">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-301">Foreign key and navigation properties</span></span>

<span data-ttu-id="c1ff3-302">Vlastnosti CK a vlastnosti navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-302">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="c1ff3-303">Záznam zápisu je pro jeden kurz, takže existuje `CourseID` vlastnost FK `Course` a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-303">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="c1ff3-304">Záznam zápisu je určen pro jednoho studenta, takže existuje `StudentID` vlastnost FK `Student` a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-304">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="c1ff3-305">Relace m:n</span><span class="sxs-lookup"><span data-stu-id="c1ff3-305">Many-to-Many Relationships</span></span>

<span data-ttu-id="c1ff3-306">Mezi `Student` entitami a `Course` existuje vztah m:n.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-306">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="c1ff3-307">Entita funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi. `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-307">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="c1ff3-308">"S datovou částí" znamená `Enrollment` , že tabulka obsahuje další data kromě FKs pro Spojené tabulky (v tomto případě PK a `Grade`).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-308">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="c1ff3-309">Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-309">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="c1ff3-310">(Tento diagram byl vygenerován pomocí [nástrojů EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6. x.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-310">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="c1ff3-311">Vytvoření diagramu není součástí kurzu.)</span><span class="sxs-lookup"><span data-stu-id="c1ff3-311">Creating the diagram isn't part of the tutorial.)</span></span>

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

<span data-ttu-id="c1ff3-313">Každá čára relace má 1 na jednom konci a hvězdičku (\*) na druhé straně, která indikuje relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-313">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="c1ff3-314">Pokud tabulka neobsahovala informace o třídě, musí obsahovat pouze dvě FKs (`CourseID` a `StudentID`). `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-314">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="c1ff3-315">Tabulka JOIN typu m:n bez datové části se někdy označuje jako čistá spojovací tabulka (PJT).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-315">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="c1ff3-316">Entity `Instructor` a`Course` mají relaci n:n pomocí tabulky Pure JOIN.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-316">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="c1ff3-317">Poznámka: EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-317">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="c1ff3-318">Další informace najdete v tématu [relace m:n v EF Core 2,0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-318">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="c1ff3-319">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="c1ff3-319">The CourseAssignment entity</span></span>

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="c1ff3-321">Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-321">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="c1ff3-322">Relace m:n pro každého-více kurzů vyžaduje tabulku JOIN a entita pro tuto tabulku JOIN je CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-322">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="c1ff3-324">Je běžné pojmenovat entitu `EntityName1EntityName2`JOIN.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-324">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="c1ff3-325">Například tabulka pro spojení instruktora do kurzů, kterou používá tento model `CourseInstructor`, bude.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-325">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="c1ff3-326">Doporučujeme však použít název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-326">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="c1ff3-327">Modely dat začínají jednoduchým a roste.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-327">Data models start out simple and grow.</span></span> <span data-ttu-id="c1ff3-328">Spojování tabulek bez datové části (PJTs) se často vyvíjí, aby zahrnovalo datovou část.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-328">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="c1ff3-329">Když začnete s popisným názvem entity, nemusíte při změně tabulky JOIN měnit název.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-329">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="c1ff3-330">V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-330">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="c1ff3-331">Například knihy a zákazníci mohou být propojeny s entitou JOIN nazvanou hodnocení.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-331">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="c1ff3-332">Pro relaci `CourseAssignment` n:n v instruktorech na více kurzů se používá přednost před `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-332">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="c1ff3-333">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="c1ff3-333">Composite key</span></span>

<span data-ttu-id="c1ff3-334">Dva FKs v `CourseAssignment` (`InstructorID` `CourseID` a`CourseAssignment` ) společně identifikují každý řádek tabulky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-334">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="c1ff3-335">`CourseAssignment`nevyžaduje vyhrazený PK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-335">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="c1ff3-336">Vlastnosti `InstructorID` a`CourseID` fungují jako složené PK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-336">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="c1ff3-337">Jediným způsobem, jak zadat složené PKs EF Core je s rozhraním *API Fluent*.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-337">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="c1ff3-338">V další části se dozvíte, jak nakonfigurovat složený PK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-338">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="c1ff3-339">Složený klíč zajišťuje:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-339">The composite key ensures that:</span></span>

* <span data-ttu-id="c1ff3-340">Pro jeden kurz je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-340">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="c1ff3-341">Pro jednoho instruktora je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-341">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="c1ff3-342">Pro stejný instruktor a kurz nejsou povoleny vícenásobné řádky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-342">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="c1ff3-343">Entita `Enrollment` JOIN definuje vlastní PK, takže je možné duplikovat toto řazení.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-343">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="c1ff3-344">Chcete-li zabránit těmto duplicitám:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-344">To prevent such duplicates:</span></span>

* <span data-ttu-id="c1ff3-345">Přidejte do polí FK jedinečný index nebo</span><span class="sxs-lookup"><span data-stu-id="c1ff3-345">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="c1ff3-346">Nakonfigurujte `Enrollment` pomocí primárního složeného klíče `CourseAssignment`podobného.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-346">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="c1ff3-347">Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-347">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="c1ff3-348">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="c1ff3-348">Update the database context</span></span>

<span data-ttu-id="c1ff3-349">Aktualizujte *data/SchoolContext. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-349">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="c1ff3-350">Předchozí kód přidá nové entity a nakonfiguruje `CourseAssignment` neseparovaný PK entity.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-350">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="c1ff3-351">Alternativa k atributům rozhraní Fluent API</span><span class="sxs-lookup"><span data-stu-id="c1ff3-351">Fluent API alternative to attributes</span></span>

<span data-ttu-id="c1ff3-352">Metoda v předchozím kódu používá *rozhraní Fluent API* ke konfiguraci chování EF Core. `OnModelCreating`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-352">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="c1ff3-353">Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-353">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="c1ff3-354">[Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem rozhraní Fluent API:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-354">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="c1ff3-355">V tomto kurzu se rozhraní API Fluent používá jenom pro mapování databáze, které nejde s atributy dělat.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-355">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="c1ff3-356">Rozhraní API Fluent ale může určovat většinu pravidel formátování, ověřování a mapování, která se dají provádět s atributy.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-356">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="c1ff3-357">Některé atributy, `MinimumLength` jako například, se nedají použít s rozhraním API Fluent.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-357">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="c1ff3-358">`MinimumLength`nemění schéma, používá pouze ověřovací pravidlo minimální délky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-358">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="c1ff3-359">Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit".</span><span class="sxs-lookup"><span data-stu-id="c1ff3-359">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="c1ff3-360">Atributy a rozhraní API Fluent lze kombinovat.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-360">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="c1ff3-361">Existují některé konfigurace, které lze provést pouze s rozhraním API Fluent (určením složeného PK).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-361">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="c1ff3-362">Existují některé konfigurace, které lze provádět pouze s atributy (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-362">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="c1ff3-363">Doporučený postup pro použití rozhraní Fluent API nebo atributů:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-363">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="c1ff3-364">Vyberte jednu z těchto dvou přístupů.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-364">Choose one of these two approaches.</span></span>
* <span data-ttu-id="c1ff3-365">Používejte vybraný postup konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-365">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="c1ff3-366">Některé atributy používané v tomto kurzu se používají pro:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-366">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="c1ff3-367">Pouze ověření (například `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-367">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="c1ff3-368">Pouze konfigurace EF Core (například `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-368">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="c1ff3-369">Konfigurace ověřování a EF Core (například `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-369">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="c1ff3-370">Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-370">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="c1ff3-371">Diagram entit</span><span class="sxs-lookup"><span data-stu-id="c1ff3-371">Entity diagram</span></span>

<span data-ttu-id="c1ff3-372">Následující ilustrace znázorňuje diagram, který nástroje EF Power Tools vytvoří pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-372">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="c1ff3-374">Předchozí diagram znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-374">The preceding diagram shows:</span></span>

* <span data-ttu-id="c1ff3-375">Několik čar relací 1:1 (1 až \*).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-375">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="c1ff3-376">Čára relace 1:1 (1 – 0.. 1) mezi `Instructor` entitami a. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-376">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="c1ff3-377">Čára relace nula až n-many (0.. 1 až ×) mezi `Instructor` entitami a. `Department`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-377">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="c1ff3-378">Přidání dat do databáze</span><span class="sxs-lookup"><span data-stu-id="c1ff3-378">Seed the database</span></span>

<span data-ttu-id="c1ff3-379">Aktualizujte kód v *data/DbInitializer. cs*:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-379">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="c1ff3-380">Předchozí kód poskytuje počáteční data pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-380">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="c1ff3-381">Většina tohoto kódu vytváří nové objekty entit a načítá vzorová data.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-381">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="c1ff3-382">Ukázková data se používají k testování.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-382">The sample data is used for testing.</span></span> <span data-ttu-id="c1ff3-383">V `Enrollments` tématu `CourseAssignments` a najdete příklady, jak lze dosazení tabulek JOIN typu many.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-383">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="c1ff3-384">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-384">Add a migration</span></span>

<span data-ttu-id="c1ff3-385">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-385">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1ff3-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1ff3-386">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1ff3-387">V PMC spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-387">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="c1ff3-388">Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-388">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="c1ff3-389">Pokud se `database update` příkaz spustí, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-389">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="c1ff3-390">V další části se dozvíte, co dělat s touto chybou.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-390">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c1ff3-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1ff3-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1ff3-392">Pokud přidáte migraci a spustíte `database update` příkaz, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-392">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="c1ff3-393">V další části se zobrazí informace o tom, jak se vyhnout této chybě.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-393">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="c1ff3-394">Použití migrace nebo vyřazení a opětovné vytvoření</span><span class="sxs-lookup"><span data-stu-id="c1ff3-394">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="c1ff3-395">Teď, když máte existující databázi, musíte si představit, jak se na ni aplikují změny.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-395">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="c1ff3-396">V tomto kurzu se zobrazí dvě alternativy:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-396">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="c1ff3-397">[Vyřaďte a znovu vytvořte databázi](#drop).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-397">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="c1ff3-398">Tuto část vyberte, pokud používáte SQLite.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-398">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="c1ff3-399">[Použijte migraci na stávající databázi](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-399">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="c1ff3-400">Pokyny v této části fungují pouze pro SQL Server, **nikoli pro SQLite**.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-400">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="c1ff3-401">Jedna volba funguje pro SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-401">Either choice works for SQL Server.</span></span> <span data-ttu-id="c1ff3-402">I když je metoda Apply složitější a časově náročná, jedná se o preferovaný přístup pro produkční prostředí z reálného světa.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-402">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="c1ff3-403">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="c1ff3-403">Drop and re-create the database</span></span>

<span data-ttu-id="c1ff3-404">[Tuto část přeskočte](#apply-the-migration) , pokud používáte SQL Server a chcete provést postup použití migrace v následující části.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-404">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="c1ff3-405">Chcete-li vynutit EF Core vytvoření nové databáze, vyřaďte a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-405">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1ff3-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1ff3-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1ff3-407">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-407">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="c1ff3-408">Odstraňte složku *migrace* a pak spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-408">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c1ff3-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1ff3-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c1ff3-410">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-410">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="c1ff3-411">Složka projektu obsahuje soubor *ContosoUniversity. csproj* .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-411">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="c1ff3-412">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-412">Run the following command:</span></span>

  ```console
  dotnet ef database drop --force
  ```

* <span data-ttu-id="c1ff3-413">Odstraňte složku *migrace* a pak spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-413">Delete the *Migrations* folder, then run the following command:</span></span>

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="c1ff3-414">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-414">Run the app.</span></span> <span data-ttu-id="c1ff3-415">Spuštění aplikace spustí `DbInitializer.Initialize` metodu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-415">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="c1ff3-416">`DbInitializer.Initialize` Naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-416">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1ff3-417">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1ff3-417">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1ff3-418">Otevřete databázi v SSOX:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-418">Open the database in SSOX:</span></span>

* <span data-ttu-id="c1ff3-419">Pokud jste dříve otevřeli SSOX, klikněte na tlačítko **aktualizovat** .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-419">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="c1ff3-420">Rozbalte **tabulky** uzlu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-420">Expand the **Tables** node.</span></span> <span data-ttu-id="c1ff3-421">Zobrazí se vytvořené tabulky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-421">The created tables are displayed.</span></span>

  ![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="c1ff3-423">Projděte si tabulku **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="c1ff3-423">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="c1ff3-424">Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data**.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-424">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="c1ff3-425">Ověřte, že tabulka **CourseAssignment** obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-425">Verify the **CourseAssignment** table contains data.</span></span>

  ![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c1ff3-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1ff3-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1ff3-428">Použijte nástroj SQLite k prohlédnutí databáze:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-428">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="c1ff3-429">Nové tabulky a sloupce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-429">New tables and columns.</span></span>
* <span data-ttu-id="c1ff3-430">Osazená data v tabulkách, například tabulka **CourseAssignment** .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-430">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="c1ff3-431">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-431">Apply the migration</span></span>

<span data-ttu-id="c1ff3-432">Tato část je volitelná.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-432">This section is optional.</span></span> <span data-ttu-id="c1ff3-433">Tyto kroky fungují jenom pro SQL Server LocalDB a jenom v případě, že jste přeskočili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-433">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="c1ff3-434">Pokud jsou migrace spouštěny s existujícími daty, může dojít k omezením FK, která nesplňují stávající data.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-434">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="c1ff3-435">S provozními daty je potřeba provést kroky pro migraci stávajících dat.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-435">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="c1ff3-436">V této části najdete příklad opravy porušení omezení CK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-436">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="c1ff3-437">Neprovádějte změny kódu bez zálohy.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-437">Don't make these code changes without a backup.</span></span> <span data-ttu-id="c1ff3-438">Neprovádějte změny kódu, pokud jste dokončili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-438">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="c1ff3-439">Soubor *{timestamp} _ComplexDataModel. cs* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-439">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="c1ff3-440">Předchozí kód přidá `Course` do tabulky nenulovou hodnotu `DepartmentID` typu FK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-440">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="c1ff3-441">Databáze z předchozího kurzu obsahuje řádky v `Course`, aby bylo možné tabulku aktualizovat pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-441">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="c1ff3-442">Chcete-li migrovat práci s existujícími daty: `ComplexDataModel`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-442">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="c1ff3-443">Změňte kód tak, aby nový sloupec (`DepartmentID`) poskytl výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-443">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="c1ff3-444">Vytvořte falešné oddělení s názvem "Temp", které bude sloužit jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-444">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="c1ff3-445">Oprava omezení cizího klíče</span><span class="sxs-lookup"><span data-stu-id="c1ff3-445">Fix the foreign key constraints</span></span>

<span data-ttu-id="c1ff3-446">Ve třídě `Up` migrace aktualizujte metodu: `ComplexDataModel`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-446">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="c1ff3-447">Otevřete soubor *{timestamp} _ComplexDataModel. cs* .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-447">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="c1ff3-448">Odkomentujte řádek kódu, který přidá `DepartmentID` sloupec `Course` do tabulky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-448">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="c1ff3-449">Přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-449">Add the following highlighted code.</span></span> <span data-ttu-id="c1ff3-450">Nový kód přejde za `.CreateTable( name: "Department"` blok:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-450">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

<span data-ttu-id="c1ff3-451">[! Code-CSharp [] (Úvod/Samples/cu30snapshots/5-Complex/migrations/ComplexDataModel. cs? Name = snippet_CreateDefaultValue & zvýraznění = 23-31)]</span><span class="sxs-lookup"><span data-stu-id="c1ff3-451">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span></span>

<span data-ttu-id="c1ff3-452">V předchozích změnách budou existující `Course` řádky při spuštění `ComplexDataModel.Up` metody v relaci s "dočasným" oddělením.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-452">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="c1ff3-453">Pro účely tohoto kurzu je zjednodušený způsob zpracování situace, kterou tady vidíte.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-453">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="c1ff3-454">Produkční aplikace by:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-454">A production app would:</span></span>

* <span data-ttu-id="c1ff3-455">Zahrnout kód nebo skripty pro přidání `Department` řádků a souvisejících `Course` řádků do nových `Department` řádků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-455">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="c1ff3-456">Nepoužívejte oddělení "dočasné" nebo výchozí hodnotu pro `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-456">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1ff3-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1ff3-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1ff3-458">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-458">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="c1ff3-459">Vzhledem k tomu, že Metodajenavrženatak,abyfungovalapouzesprázdnoudatabází,použijteSSOXkodstraněnívšechřádkůvtabulkáchstudentaCourse.`DbInitializer.Initialize`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-459">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="c1ff3-460">(Kaskádová odstranění se postará o tabulku zápisu.)</span><span class="sxs-lookup"><span data-stu-id="c1ff3-460">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c1ff3-461">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1ff3-461">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c1ff3-462">Pokud používáte SQL Server LocalDB s Visual Studio Code, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-462">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```console
  dotnet ef database update
  ```

---

<span data-ttu-id="c1ff3-463">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-463">Run the app.</span></span> <span data-ttu-id="c1ff3-464">Spuštění aplikace spustí `DbInitializer.Initialize` metodu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-464">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="c1ff3-465">`DbInitializer.Initialize` Naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-465">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c1ff3-466">Další kroky</span><span class="sxs-lookup"><span data-stu-id="c1ff3-466">Next steps</span></span>

<span data-ttu-id="c1ff3-467">Následující dva kurzy ukazují, jak číst a aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-467">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c1ff3-468">[Předchozí kurz](xref:data/ef-rp/migrations)
> –[Další kurz](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="c1ff3-468">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c1ff3-469">Předchozí kurzy pracovaly se základním datovým modelem, který se skládá ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-469">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="c1ff3-470">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-470">In this tutorial:</span></span>

* <span data-ttu-id="c1ff3-471">Přidávají se další entity a vztahy.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-471">More entities and relationships are added.</span></span>
* <span data-ttu-id="c1ff3-472">Datový model je přizpůsoben zadáním pravidel formátování, ověřování a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-472">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="c1ff3-473">Třídy entit pro dokončený datový model jsou znázorněny na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-473">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="c1ff3-475">Pokud narazíte na problémy, které nemůžete [vyřešit,](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)Stáhněte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-475">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="c1ff3-476">Přizpůsobení datového modelu pomocí atributů</span><span class="sxs-lookup"><span data-stu-id="c1ff3-476">Customize the data model with attributes</span></span>

<span data-ttu-id="c1ff3-477">V této části je datový model přizpůsoben pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-477">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="c1ff3-478">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="c1ff3-478">The DataType attribute</span></span>

<span data-ttu-id="c1ff3-479">Na stránkách studenta se aktuálně zobrazuje čas pro datum registrace.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-479">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="c1ff3-480">Pole data obvykle zobrazují pouze datum a čas.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-480">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="c1ff3-481">Aktualizujte *modely/student. cs* následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-481">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="c1ff3-482">Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) Určuje datový typ, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-482">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="c1ff3-483">V tomto případě by se měla zobrazit pouze datum, nikoli datum a čas.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-483">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="c1ff3-484">[Výčet DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress atd. `DataType` Atribut také může aplikaci povolit automatické poskytování funkcí specifických pro typ.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-484">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="c1ff3-485">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-485">For example:</span></span>

* <span data-ttu-id="c1ff3-486">Automaticky se vytvoří `DataType.EmailAddress`odkaz pro. `mailto:`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-486">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="c1ff3-487">Selektor data je k `DataType.Date` dispozici ve většině prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-487">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="c1ff3-488">Atribut vygeneruje atributy HTML 5 `data-` (vyslovované datové přerušované), které používají prohlížeče formátu HTML 5. `DataType`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-488">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="c1ff3-489">`DataType` Atributy neposkytují ověřování.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-489">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="c1ff3-490">`DataType.Date`neurčuje formát data, které se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-490">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="c1ff3-491">Ve výchozím nastavení se pole Datum zobrazuje v závislosti na výchozích formátech na základě objektu [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serveru.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-491">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="c1ff3-492">`DisplayFormat` Atribut slouží k explicitnímu zadání formátu data:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-492">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="c1ff3-493">Toto `ApplyFormatInEditMode` nastavení určuje, že by mělo být formátování použito také na uživatelské rozhraní pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-493">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="c1ff3-494">Některá pole byste neměli `ApplyFormatInEditMode`používat.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-494">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="c1ff3-495">Například symbol měny by neměl být v textovém poli pro úpravy obvykle zobrazen.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-495">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="c1ff3-496">`DisplayFormat` Atribut může používat sám sebe.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-496">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="c1ff3-497">Obecně je vhodné použít `DataType` atribut `DisplayFormat` s atributem.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-497">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="c1ff3-498">`DataType` Atribut předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-498">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="c1ff3-499">Atribut poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`: `DataType`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-499">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="c1ff3-500">Prohlížeč může povolit funkce HTML5.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-500">The browser can enable HTML5 features.</span></span> <span data-ttu-id="c1ff3-501">Například můžete zobrazit ovládací prvek kalendáře, symbol měny odpovídající národním prostředí, e-mailové odkazy, ověřování vstupu na straně klienta atd.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-501">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="c1ff3-502">Ve výchozím nastavení prohlížeč vykresluje data pomocí správného formátu založeného na národním prostředí.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-502">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="c1ff3-503">Další informace najdete v [ \<dokumentaci k rutině Input > Tag](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-503">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="c1ff3-504">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-504">Run the app.</span></span> <span data-ttu-id="c1ff3-505">Přejděte na stránku indexu studentů.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-505">Navigate to the Students Index page.</span></span> <span data-ttu-id="c1ff3-506">Časy se už nezobrazují.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-506">Times are no longer displayed.</span></span> <span data-ttu-id="c1ff3-507">Každé zobrazení, které používá `Student` model, zobrazuje datum bez času.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-507">Every view that uses the `Student` model displays the date without time.</span></span>

![Stránka indexu studentů zobrazující data bez časů](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="c1ff3-509">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="c1ff3-509">The StringLength attribute</span></span>

<span data-ttu-id="c1ff3-510">Pravidla ověřování dat a chybové zprávy ověřování lze zadat pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-510">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="c1ff3-511">Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-511">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="c1ff3-512">`StringLength` Atribut také poskytuje ověřování na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-512">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="c1ff3-513">Minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-513">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="c1ff3-514">Aktualizujte `Student` model pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-514">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="c1ff3-515">Předchozí kód omezuje názvy na více než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-515">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="c1ff3-516">`StringLength` Atribut nebrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-516">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="c1ff3-517">Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) se používá k aplikování omezení na vstup.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-517">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="c1ff3-518">Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-518">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="c1ff3-519">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-519">Run the app:</span></span>

* <span data-ttu-id="c1ff3-520">Přejděte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-520">Navigate to the Students page.</span></span>
* <span data-ttu-id="c1ff3-521">Vyberte **vytvořit novou**a zadejte název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-521">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="c1ff3-522">Vyberte **vytvořit**, ověřování na straně klienta zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-522">Select **Create**, client-side validation shows an error message.</span></span>

![Stránka indexu studentů zobrazující chyby délky řetězce](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="c1ff3-524">V **Průzkumník objektů systému SQL Server** (SSOX) otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-524">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů v SSOX před migracemi](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="c1ff3-526">Na předchozím obrázku je znázorněno schéma pro `Student` tabulku.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-526">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="c1ff3-527">Pole název mají typ `nvarchar(MAX)` , protože migrace nejsou v databázi spuštěny.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-527">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="c1ff3-528">Po spuštění migrace později v tomto kurzu se změní `nvarchar(50)`pole název.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-528">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="c1ff3-529">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="c1ff3-529">The Column attribute</span></span>

<span data-ttu-id="c1ff3-530">Atributy mohou řídit způsob, jakým jsou třídy a vlastnosti mapovány na databázi.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-530">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="c1ff3-531">V této části `Column` se atribut používá pro mapování názvu `FirstMidName` vlastnosti na "FirstName" v databázi.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-531">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="c1ff3-532">Při vytvoření databáze se názvy vlastností v modelu používají pro názvy sloupců (kromě případu, `Column` kdy se atribut používá).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-532">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="c1ff3-533">`Student` Model používá`FirstMidName` pole pro první název, protože pole může obsahovat také prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-533">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="c1ff3-534">Aktualizujte soubor *student.cs* pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-534">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="c1ff3-535">`Student.FirstMidName` V předchozí změně se v aplikaci mapuje `FirstName` na sloupec `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-535">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="c1ff3-536">Přidání `Column` atributu změní model `SchoolContext`zálohování.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-536">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="c1ff3-537">Model, který `SchoolContext` zálohování již nevyhovuje, se neshoduje s databází.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-537">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="c1ff3-538">Pokud je aplikace spuštěná před použitím migrace, vygeneruje se tato výjimka:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-538">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="c1ff3-539">Aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-539">To update the DB:</span></span>

* <span data-ttu-id="c1ff3-540">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-540">Build the project.</span></span>
* <span data-ttu-id="c1ff3-541">Otevřete okno příkazového řádku ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-541">Open a command window in the project folder.</span></span> <span data-ttu-id="c1ff3-542">Zadáním následujících příkazů vytvořte novou migraci a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-542">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1ff3-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1ff3-543">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c1ff3-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1ff3-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="c1ff3-545">`migrations add ColumnFirstName` Příkaz vygeneruje následující varovnou zprávu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-545">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="c1ff3-546">Upozornění je vygenerováno, protože pole s názvem jsou nyní omezena na 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-546">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="c1ff3-547">Pokud má název v databázi více než 50 znaků, ztratí se 51 na poslední znak.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-547">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="c1ff3-548">Otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-548">Test the app.</span></span>

<span data-ttu-id="c1ff3-549">Otevřete tabulku student v SSOX:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-549">Open the Student table in SSOX:</span></span>

![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="c1ff3-551">Před použitím migrace byly sloupce názvu typu [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-551">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="c1ff3-552">Sloupce názvů jsou nyní `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-552">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="c1ff3-553">Změnil se název sloupce z `FirstMidName` na. `FirstName`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-553">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="c1ff3-554">V následující části sestavení aplikace v některých fázích generuje chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-554">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="c1ff3-555">Pokyny určují, kdy se má aplikace sestavit.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-555">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="c1ff3-556">Aktualizace entity studenta</span><span class="sxs-lookup"><span data-stu-id="c1ff3-556">Student entity update</span></span>

![Entita studenta](complex-data-model/_static/student-entity.png)

<span data-ttu-id="c1ff3-558">Aktualizujte *modely/student. cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-558">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="c1ff3-559">Požadovaný atribut</span><span class="sxs-lookup"><span data-stu-id="c1ff3-559">The Required attribute</span></span>

<span data-ttu-id="c1ff3-560">`Required` Atribut nastaví název vlastnosti povinná pole.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-560">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="c1ff3-561">Atribut není potřebný pro typy, které neumožňují hodnotu null, jako jsou`DateTime`typy hodnot `double`(, `int`, atd.). `Required`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-561">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="c1ff3-562">Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-562">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="c1ff3-563">Atribut může být nahrazen parametrem minimální délky `StringLength` v atributu: `Required`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-563">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="c1ff3-564">Atribut zobrazení</span><span class="sxs-lookup"><span data-stu-id="c1ff3-564">The Display attribute</span></span>

<span data-ttu-id="c1ff3-565">`Display` Atribut určuje, že titulek pro textová pole by měl být "jméno", "příjmení", "celé jméno" a "datum zápisu".</span><span class="sxs-lookup"><span data-stu-id="c1ff3-565">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="c1ff3-566">Výchozí titulky neobsahovaly místo dělení slov, například "LastName".</span><span class="sxs-lookup"><span data-stu-id="c1ff3-566">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="c1ff3-567">Vypočítaná vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="c1ff3-567">The FullName calculated property</span></span>

<span data-ttu-id="c1ff3-568">`FullName`je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-568">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="c1ff3-569">`FullName`nelze ji nastavit, má pouze přistupující objekt get.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-569">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="c1ff3-570">V `FullName` databázi není vytvořen žádný sloupec.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-570">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="c1ff3-571">Vytvořit entitu instruktora</span><span class="sxs-lookup"><span data-stu-id="c1ff3-571">Create the Instructor Entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="c1ff3-573">Vytvořte *modely/Instructor. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-573">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="c1ff3-574">Více atributů může být na jednom řádku.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-574">Multiple attributes can be on one line.</span></span> <span data-ttu-id="c1ff3-575">`HireDate` Atributy mohou být zapsány následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-575">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="c1ff3-576">Navigační vlastnosti CourseAssignments a OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="c1ff3-576">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="c1ff3-577">Vlastnosti `CourseAssignments` a`OfficeAssignment` jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-577">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="c1ff3-578">Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-578">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="c1ff3-579">Pokud navigační vlastnost obsahuje více entit:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-579">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="c1ff3-580">Musí se jednat o typ seznamu, kde je možné přidávat, odstraňovat a aktualizovat položky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-580">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="c1ff3-581">Mezi typy vlastností navigace patří:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-581">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="c1ff3-582">Pokud `ICollection<T>` je zadáno, EF Core `HashSet<T>` vytvoří kolekci ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-582">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="c1ff3-583">`CourseAssignment` Entita je vysvětlena v části u vztahů m:n.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-583">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="c1ff3-584">Firemní pravidla společnosti Contoso vysokých škol, že vyučující může mít maximálně jednu kancelář.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-584">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="c1ff3-585">Vlastnost obsahuje jednu `OfficeAssignment`entitu. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-585">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="c1ff3-586">`OfficeAssignment`má hodnotu null, pokud není přiřazen žádný systém Office.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-586">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="c1ff3-587">Vytvoření entity OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="c1ff3-587">Create the OfficeAssignment entity</span></span>

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="c1ff3-589">Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-589">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="c1ff3-590">Klíčový atribut</span><span class="sxs-lookup"><span data-stu-id="c1ff3-590">The Key attribute</span></span>

<span data-ttu-id="c1ff3-591">`[Key]` Atribut slouží k identifikaci vlastnosti jako primárního klíče (PK), pokud je název vlastnosti něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-591">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="c1ff3-592">Mezi `Instructor` entitami a `OfficeAssignment` je relace 1:1 nebo jedna.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-592">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="c1ff3-593">Přiřazení kanceláře existuje jenom ve vztahu k instruktorovi, ke kterému je přiřazený.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-593">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="c1ff3-594">PK je také jeho cizí klíč (FK) `Instructor` k entitě. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-594">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="c1ff3-595">EF Core nemůže automaticky rozpoznat `InstructorID` jako PK z těchto `OfficeAssignment` důvodů:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-595">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="c1ff3-596">`InstructorID`nedodržuje konvence pojmenování ID nebo classnameID.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-596">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="c1ff3-597">Proto atribut slouží k identifikaci `InstructorID` jako PK: `Key`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-597">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="c1ff3-598">Ve výchozím nastavení EF Core považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-598">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="c1ff3-599">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="c1ff3-599">The Instructor navigation property</span></span>

<span data-ttu-id="c1ff3-600">Vlastnost navigace pro entitu `Instructor` může mít hodnotu null, protože: `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-600">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="c1ff3-601">Typy odkazů (například třídy mohou mít hodnotu null).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-601">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="c1ff3-602">Instruktor nemusí mít přiřazení kanceláře.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-602">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="c1ff3-603">Entita `OfficeAssignment` má vlastnost navigace, která neumožňuje hodnotu null `Instructor` , protože:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-603">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="c1ff3-604">`InstructorID`hodnota nemůže být null.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-604">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="c1ff3-605">Přiřazení kanceláře nemůže existovat bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-605">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="c1ff3-606">Pokud má `OfficeAssignment` entita související entitu, Každá entita má odkaz na jinou entitu v její navigační vlastnosti. `Instructor`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-606">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="c1ff3-607">Atribut lze použít `Instructor` pro navigační vlastnost: `[Required]`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-607">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="c1ff3-608">Předchozí kód určuje, že musí existovat související instruktor.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-608">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="c1ff3-609">Předchozí kód není potřebný, protože `InstructorID` cizí klíč (což je také klíč PK) je nepovoluje hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-609">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="c1ff3-610">Úprava entity kurzu</span><span class="sxs-lookup"><span data-stu-id="c1ff3-610">Modify the Course Entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="c1ff3-612">Aktualizujte *modely/Course. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-612">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="c1ff3-613">Entita má vlastnost `DepartmentID`cizího klíče (FK). `Course`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-613">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="c1ff3-614">`DepartmentID`odkazuje na související `Department` entitu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-614">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="c1ff3-615">`Course` Entita má vlastnost navigace. `Department`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-615">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="c1ff3-616">EF Core nevyžaduje vlastnost FK pro datový model, pokud model má vlastnost navigace pro související entitu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-616">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="c1ff3-617">EF Core v databázi automaticky vytvoří FKs bez ohledu na to, kde jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-617">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="c1ff3-618">EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automatické vytváření FKs.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-618">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="c1ff3-619">Pokud se v datovém modelu nachází FK, může být aktualizace jednodušší a efektivnější.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-619">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="c1ff3-620">Zvažte například model, ve kterém není obsažena vlastnost `DepartmentID` FK .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-620">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="c1ff3-621">Když se načte entita kurzu, která se upraví:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-621">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="c1ff3-622">`Department` Entita má hodnotu null, pokud není explicitně načtena.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-622">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="c1ff3-623">Chcete-li aktualizovat entitu kurzu `Department` , je nutné nejprve načíst entitu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-623">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="c1ff3-624">Pokud je vlastnost `DepartmentID` FK obsažena v datovém modelu, není nutné `Department` načíst entitu před aktualizací.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-624">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="c1ff3-625">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="c1ff3-625">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="c1ff3-626">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` Atribut určuje, zda je v rámci aplikace poskytnuta PK místo vygenerovaného databází.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-626">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="c1ff3-627">Ve výchozím nastavení EF Core předpokládá, že jsou hodnoty PK generovány databází.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-627">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="c1ff3-628">DATABÁZE vygenerovala hodnoty PK většinou nejlepšího přístupu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-628">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="c1ff3-629">Pro `Course` entity určuje uživatel PK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-629">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="c1ff3-630">Například číslo kurzu, jako je například série 1000 pro matematické oddělení, série 2000 pro národní oddělení.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-630">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="c1ff3-631">`DatabaseGenerated` Atribut lze také použít ke generování výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-631">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="c1ff3-632">DATABÁZE může například automaticky vygenerovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-632">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="c1ff3-633">Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-633">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c1ff3-634">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-634">Foreign key and navigation properties</span></span>

<span data-ttu-id="c1ff3-635">Vlastnosti cizího klíče (FK) a navigační vlastnosti v `Course` entitě odráží následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-635">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="c1ff3-636">Kurz se přiřadí jednomu oddělení, takže je k dispozici `DepartmentID` FK `Department` a navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-636">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="c1ff3-637">V rámci kurzu může být zaregistrované několik studentů, takže `Enrollments` navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-637">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="c1ff3-638">Kurz může být výukou více instruktorů, takže `CourseAssignments` navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-638">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="c1ff3-639">`CourseAssignment`je vysvětleno [později](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-639">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="c1ff3-640">Vytvořit entitu oddělení</span><span class="sxs-lookup"><span data-stu-id="c1ff3-640">Create the Department entity</span></span>

![Entita oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="c1ff3-642">Vytvořte *modely/oddělení. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-642">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="c1ff3-643">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="c1ff3-643">The Column attribute</span></span>

<span data-ttu-id="c1ff3-644">Dřív se `Column` použil atribut pro změnu mapování názvu sloupce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-644">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="c1ff3-645">V kódu pro `Department` entitu `Column` se atribut používá ke změně mapování datových typů SQL.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-645">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="c1ff3-646">`Budget` Sloupec je definovaný pomocí SQL Server peněžního typu v databázi:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-646">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="c1ff3-647">Mapování sloupce není obecně vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-647">Column mapping is generally not required.</span></span> <span data-ttu-id="c1ff3-648">EF Core všeobecně vybere vhodný SQL Server datový typ založený na typu CLR pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-648">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="c1ff3-649">Typ CLR `decimal` se mapuje na typ SQL Server `decimal` .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-649">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="c1ff3-650">`Budget`je pro měnu a datový typ Money je pro měnu vhodný.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-650">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c1ff3-651">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-651">Foreign key and navigation properties</span></span>

<span data-ttu-id="c1ff3-652">Vlastnosti FK a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-652">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="c1ff3-653">Oddělení může nebo nemusí mít správce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-653">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="c1ff3-654">Správce je vždy instruktor.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-654">An administrator is always an instructor.</span></span> <span data-ttu-id="c1ff3-655">Proto je `Instructor` vlastnost obsažena jako FK pro entitu. `InstructorID`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-655">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="c1ff3-656">Navigační vlastnost má název `Administrator` , ale `Instructor` obsahuje entitu:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-656">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="c1ff3-657">Otazník (?) v předchozím kódu určuje vlastnost s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-657">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="c1ff3-658">Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-658">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="c1ff3-659">Poznámka: Podle konvence EF Core povoluje kaskádové odstranění pro FKs, která nejsou null a pro relace m:n.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-659">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="c1ff3-660">Kaskádové odstranění může mít za následek cyklické Kaskádové odstraňování pravidel.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-660">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="c1ff3-661">Cyklická kaskádová odstranění pravidel způsobí výjimku při přidání migrace.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-661">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="c1ff3-662">Pokud byla například `Department.InstructorID` vlastnost definována jako nepovolená hodnota null:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-662">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="c1ff3-663">EF Core konfiguruje pravidlo kaskádového odstranění, které odstraní oddělení při odstranění instruktora.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-663">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="c1ff3-664">Odstranění oddělení, když se odstraní instruktor, není zamýšleným chováním.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-664">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="c1ff3-665">Následující rozhraní Fluent API by místo kaskádového nastavilo pravidlo omezení.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-665">The following fluent API would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="c1ff3-666">Předchozí kód zakáže kaskádové odstranění na základě vztahu oddělení a instruktor.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-666">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="c1ff3-667">Aktualizace entity registrace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-667">Update the Enrollment entity</span></span>

<span data-ttu-id="c1ff3-668">Záznam zápisu je pro jeden kurz, který přijímá jeden student.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-668">An enrollment record is for one course taken by one student.</span></span>

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="c1ff3-670">Aktualizujte *modely/zápis. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-670">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c1ff3-671">Vlastnosti cizích klíčů a navigace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-671">Foreign key and navigation properties</span></span>

<span data-ttu-id="c1ff3-672">Vlastnosti CK a vlastnosti navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-672">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="c1ff3-673">Záznam zápisu je pro jeden kurz, takže existuje `CourseID` vlastnost FK `Course` a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-673">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="c1ff3-674">Záznam zápisu je určen pro jednoho studenta, takže existuje `StudentID` vlastnost FK `Student` a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-674">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="c1ff3-675">Relace m:n</span><span class="sxs-lookup"><span data-stu-id="c1ff3-675">Many-to-Many Relationships</span></span>

<span data-ttu-id="c1ff3-676">Mezi `Student` entitami a `Course` existuje vztah m:n.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-676">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="c1ff3-677">Entita funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi. `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-677">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="c1ff3-678">"S datovou částí" znamená `Enrollment` , že tabulka obsahuje další data kromě FKs pro Spojené tabulky (v tomto případě PK a `Grade`).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-678">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="c1ff3-679">Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-679">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="c1ff3-680">(Tento diagram byl vygenerován pomocí [nástrojů EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6. x.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-680">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="c1ff3-681">Vytvoření diagramu není součástí kurzu.)</span><span class="sxs-lookup"><span data-stu-id="c1ff3-681">Creating the diagram isn't part of the tutorial.)</span></span>

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

<span data-ttu-id="c1ff3-683">Každá čára relace má 1 na jednom konci a hvězdičku (\*) na druhé straně, která indikuje relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-683">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="c1ff3-684">Pokud tabulka neobsahovala informace o třídě, musí obsahovat pouze dvě FKs (`CourseID` a `StudentID`). `Enrollment`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-684">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="c1ff3-685">Tabulka JOIN typu m:n bez datové části se někdy označuje jako čistá spojovací tabulka (PJT).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-685">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="c1ff3-686">Entity `Instructor` a`Course` mají relaci n:n pomocí tabulky Pure JOIN.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-686">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="c1ff3-687">Poznámka: EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-687">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="c1ff3-688">Další informace najdete v tématu [relace m:n v EF Core 2,0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-688">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="c1ff3-689">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="c1ff3-689">The CourseAssignment entity</span></span>

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="c1ff3-691">Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-691">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="c1ff3-692">Instruktory do kurzů</span><span class="sxs-lookup"><span data-stu-id="c1ff3-692">Instructor-to-Courses</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="c1ff3-694">Vztah n:n od instruktora do více kurzů:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-694">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="c1ff3-695">Vyžaduje tabulku JOIN, která musí být reprezentována sadou entit.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-695">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="c1ff3-696">Je čistá spojovací tabulka (tabulka bez datové části).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-696">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="c1ff3-697">Je běžné pojmenovat entitu `EntityName1EntityName2`JOIN.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-697">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="c1ff3-698">Například tabulka pro spojení instruktora do kurzů, která používá tento model, je `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-698">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="c1ff3-699">Doporučujeme však použít název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-699">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="c1ff3-700">Modely dat začínají jednoduchým a roste.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-700">Data models start out simple and grow.</span></span> <span data-ttu-id="c1ff3-701">Spojení bez datové části (PJTs) se často rozvíjejí, aby zahrnovalo datovou část.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-701">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="c1ff3-702">Když začnete s popisným názvem entity, nemusíte při změně tabulky JOIN měnit název.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-702">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="c1ff3-703">V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-703">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="c1ff3-704">Například knihy a zákazníci mohou být propojeny s entitou JOIN nazvanou hodnocení.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-704">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="c1ff3-705">Pro relaci `CourseAssignment` n:n v instruktorech na více kurzů se používá přednost před `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-705">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="c1ff3-706">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="c1ff3-706">Composite key</span></span>

<span data-ttu-id="c1ff3-707">FKs nemohou mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-707">FKs are not nullable.</span></span> <span data-ttu-id="c1ff3-708">Dva FKs v `CourseAssignment` (`InstructorID` `CourseID` a`CourseAssignment` ) společně identifikují každý řádek tabulky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-708">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="c1ff3-709">`CourseAssignment`nevyžaduje vyhrazený PK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-709">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="c1ff3-710">Vlastnosti `InstructorID` a`CourseID` fungují jako složené PK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-710">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="c1ff3-711">Jediným způsobem, jak zadat složené PKs EF Core je s rozhraním *API Fluent*.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-711">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="c1ff3-712">V další části se dozvíte, jak nakonfigurovat složený PK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-712">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="c1ff3-713">Složený klíč zajišťuje:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-713">The composite key ensures:</span></span>

* <span data-ttu-id="c1ff3-714">Pro jeden kurz je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-714">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="c1ff3-715">Pro jednoho instruktora je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-715">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="c1ff3-716">Více řádků pro stejný instruktor a kurz není povoleno.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-716">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="c1ff3-717">Entita `Enrollment` JOIN definuje vlastní PK, takže je možné duplikovat toto řazení.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-717">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="c1ff3-718">Chcete-li zabránit těmto duplicitám:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-718">To prevent such duplicates:</span></span>

* <span data-ttu-id="c1ff3-719">Přidejte do polí FK jedinečný index nebo</span><span class="sxs-lookup"><span data-stu-id="c1ff3-719">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="c1ff3-720">Nakonfigurujte `Enrollment` pomocí primárního složeného klíče `CourseAssignment`podobného.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-720">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="c1ff3-721">Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-721">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="c1ff3-722">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="c1ff3-722">Update the DB context</span></span>

<span data-ttu-id="c1ff3-723">Do *data/SchoolContext. cs*přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-723">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="c1ff3-724">Předchozí kód přidá nové entity a nakonfiguruje `CourseAssignment` neseparovaný PK entity.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-724">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="c1ff3-725">Alternativa k atributům rozhraní Fluent API</span><span class="sxs-lookup"><span data-stu-id="c1ff3-725">Fluent API alternative to attributes</span></span>

<span data-ttu-id="c1ff3-726">Metoda v předchozím kódu používá *rozhraní Fluent API* ke konfiguraci chování EF Core. `OnModelCreating`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-726">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="c1ff3-727">Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-727">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="c1ff3-728">[Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem rozhraní Fluent API:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-728">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="c1ff3-729">V tomto kurzu se rozhraní API Fluent používá jenom pro mapování DB, které nejde s atributy dělat.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-729">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="c1ff3-730">Rozhraní API Fluent ale může určovat většinu pravidel formátování, ověřování a mapování, která se dají provádět s atributy.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-730">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="c1ff3-731">Některé atributy, `MinimumLength` jako například, se nedají použít s rozhraním API Fluent.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-731">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="c1ff3-732">`MinimumLength`nemění schéma, používá pouze ověřovací pravidlo minimální délky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-732">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="c1ff3-733">Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit".</span><span class="sxs-lookup"><span data-stu-id="c1ff3-733">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="c1ff3-734">Atributy a rozhraní API Fluent lze kombinovat.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-734">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="c1ff3-735">Existují některé konfigurace, které lze provést pouze s rozhraním API Fluent (určením složeného PK).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-735">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="c1ff3-736">Existují některé konfigurace, které lze provádět pouze s atributy (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-736">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="c1ff3-737">Doporučený postup pro použití rozhraní Fluent API nebo atributů:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-737">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="c1ff3-738">Vyberte jednu z těchto dvou přístupů.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-738">Choose one of these two approaches.</span></span>
* <span data-ttu-id="c1ff3-739">Používejte vybraný postup konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-739">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="c1ff3-740">Některé atributy použité v tomto kurzu se používají pro:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-740">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="c1ff3-741">Pouze ověření (například `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-741">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="c1ff3-742">Pouze konfigurace EF Core (například `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-742">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="c1ff3-743">Konfigurace ověřování a EF Core (například `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-743">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="c1ff3-744">Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-744">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="c1ff3-745">Diagram entit znázorňující vztahy</span><span class="sxs-lookup"><span data-stu-id="c1ff3-745">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="c1ff3-746">Následující ilustrace znázorňuje diagram, který nástroje EF Power Tools vytvoří pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-746">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="c1ff3-748">Předchozí diagram znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-748">The preceding diagram shows:</span></span>

* <span data-ttu-id="c1ff3-749">Několik čar relací 1:1 (1 až \*).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-749">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="c1ff3-750">Čára relace 1:1 (1 – 0.. 1) mezi `Instructor` entitami a. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-750">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="c1ff3-751">Čára relace nula až n-many (0.. 1 až ×) mezi `Instructor` entitami a. `Department`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-751">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="c1ff3-752">Osazení databáze pomocí testovacích dat</span><span class="sxs-lookup"><span data-stu-id="c1ff3-752">Seed the DB with Test Data</span></span>

<span data-ttu-id="c1ff3-753">Aktualizujte kód v *data/DbInitializer. cs*:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-753">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="c1ff3-754">Předchozí kód poskytuje počáteční data pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-754">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="c1ff3-755">Většina tohoto kódu vytváří nové objekty entit a načítá vzorová data.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-755">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="c1ff3-756">Ukázková data se používají k testování.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-756">The sample data is used for testing.</span></span> <span data-ttu-id="c1ff3-757">V `Enrollments` tématu `CourseAssignments` a najdete příklady, jak lze dosazení tabulek JOIN typu many.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-757">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="c1ff3-758">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-758">Add a migration</span></span>

<span data-ttu-id="c1ff3-759">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-759">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1ff3-760">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1ff3-760">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c1ff3-761">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1ff3-761">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="c1ff3-762">Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-762">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="c1ff3-763">Pokud se `database update` příkaz spustí, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-763">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="c1ff3-764">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="c1ff3-764">Apply the migration</span></span>

<span data-ttu-id="c1ff3-765">Teď, když máte existující databázi, musíte si představit, jak v nich použít budoucí změny.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-765">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="c1ff3-766">V tomto kurzu se dozvíte dva přístupy:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-766">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="c1ff3-767">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="c1ff3-767">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="c1ff3-768">[Použijte migraci na stávající databázi](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="c1ff3-768">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="c1ff3-769">I když je tato metoda složitější a časově náročná, jedná se o preferovaný přístup pro produkční prostředí z reálného světa.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-769">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="c1ff3-770">**Poznámka:** Toto je volitelný oddíl tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-770">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="c1ff3-771">Můžete provést kroky odkládací a znovu vytvořit a tuto část přeskočit.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-771">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="c1ff3-772">Pokud chcete postupovat podle kroků v této části, neprovádějte kroky odkládací a znovu vytvořit.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-772">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="c1ff3-773">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="c1ff3-773">Drop and re-create the database</span></span>

<span data-ttu-id="c1ff3-774">Kód v aktualizovaném `DbInitializer` přidání počátečních dat pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-774">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="c1ff3-775">Pokud chcete vynutit EF Core vytvoření nové databáze, vyřaďte a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-775">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1ff3-776">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1ff3-776">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1ff3-777">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-777">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="c1ff3-778">Pokud `Get-Help about_EntityFrameworkCore` chcete získat informace o nápovědě, spusťte z PMC.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-778">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c1ff3-779">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1ff3-779">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1ff3-780">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-780">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="c1ff3-781">Složka projektu obsahuje soubor *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-781">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="c1ff3-782">V příkazovém okně zadejte následující:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-782">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

<span data-ttu-id="c1ff3-783">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-783">Run the app.</span></span> <span data-ttu-id="c1ff3-784">Spuštění aplikace spustí `DbInitializer.Initialize` metodu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-784">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="c1ff3-785">`DbInitializer.Initialize` Naplní novou databázi.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-785">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="c1ff3-786">Otevřete databázi v SSOX:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-786">Open the DB in SSOX:</span></span>

* <span data-ttu-id="c1ff3-787">Pokud jste dříve otevřeli SSOX, klikněte na tlačítko **aktualizovat** .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-787">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="c1ff3-788">Rozbalte **tabulky** uzlu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-788">Expand the **Tables** node.</span></span> <span data-ttu-id="c1ff3-789">Zobrazí se vytvořené tabulky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-789">The created tables are displayed.</span></span>

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="c1ff3-791">Projděte si tabulku **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="c1ff3-791">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="c1ff3-792">Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data**.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-792">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="c1ff3-793">Ověřte, že tabulka **CourseAssignment** obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-793">Verify the **CourseAssignment** table contains data.</span></span>

![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="c1ff3-795">Použít migraci na existující databázi</span><span class="sxs-lookup"><span data-stu-id="c1ff3-795">Apply the migration to the existing database</span></span>

<span data-ttu-id="c1ff3-796">Tato část je volitelná.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-796">This section is optional.</span></span> <span data-ttu-id="c1ff3-797">Tyto kroky fungují pouze v případě, že jste přeskočili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-797">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="c1ff3-798">Pokud jsou migrace spouštěny s existujícími daty, může dojít k omezením FK, která nesplňují stávající data.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-798">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="c1ff3-799">S provozními daty je potřeba provést kroky pro migraci stávajících dat.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-799">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="c1ff3-800">V této části najdete příklad opravy porušení omezení CK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-800">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="c1ff3-801">Neprovádějte změny kódu bez zálohy.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-801">Don't make these code changes without a backup.</span></span> <span data-ttu-id="c1ff3-802">Neprovádějte změny kódu, pokud jste dokončili předchozí oddíl a aktualizovali databázi.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-802">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="c1ff3-803">Soubor *{timestamp} _ComplexDataModel. cs* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-803">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="c1ff3-804">Předchozí kód přidá `Course` do tabulky nenulovou hodnotu `DepartmentID` typu FK.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-804">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="c1ff3-805">Databáze z předchozího kurzu obsahuje řádky v `Course`, takže nelze aktualizovat tabulku pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-805">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="c1ff3-806">Chcete-li migrovat práci s existujícími daty: `ComplexDataModel`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-806">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="c1ff3-807">Změňte kód tak, aby nový sloupec (`DepartmentID`) poskytl výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-807">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="c1ff3-808">Vytvořte falešné oddělení s názvem "Temp", které bude sloužit jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-808">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="c1ff3-809">Oprava omezení cizího klíče</span><span class="sxs-lookup"><span data-stu-id="c1ff3-809">Fix the foreign key constraints</span></span>

<span data-ttu-id="c1ff3-810">Aktualizujte metodu `Up`třídy: `ComplexDataModel`</span><span class="sxs-lookup"><span data-stu-id="c1ff3-810">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="c1ff3-811">Otevřete soubor *{timestamp} _ComplexDataModel. cs* .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-811">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="c1ff3-812">Odkomentujte řádek kódu, který přidá `DepartmentID` sloupec `Course` do tabulky.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-812">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="c1ff3-813">Přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-813">Add the following highlighted code.</span></span> <span data-ttu-id="c1ff3-814">Nový kód přejde za `.CreateTable( name: "Department"` blok:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-814">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="c1ff3-815">V předchozích změnách budou existující `Course` řádky při spuštění `Up` metody v relaci s "dočasným" oddělením `ComplexDataModel` .</span><span class="sxs-lookup"><span data-stu-id="c1ff3-815">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="c1ff3-816">Produkční aplikace by:</span><span class="sxs-lookup"><span data-stu-id="c1ff3-816">A production app would:</span></span>

* <span data-ttu-id="c1ff3-817">Zahrnout kód nebo skripty pro přidání `Department` řádků a souvisejících `Course` řádků do nových `Department` řádků.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-817">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="c1ff3-818">Nepoužívejte oddělení "dočasné" nebo výchozí hodnotu pro `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-818">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="c1ff3-819">Další kurz se zabývá souvisejícími daty.</span><span class="sxs-lookup"><span data-stu-id="c1ff3-819">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1ff3-820">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c1ff3-820">Additional resources</span></span>

* [<span data-ttu-id="c1ff3-821">Verze tohoto kurzu pro YouTube (část 1)</span><span class="sxs-lookup"><span data-stu-id="c1ff3-821">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="c1ff3-822">Verze tohoto kurzu pro YouTube (část 2)</span><span class="sxs-lookup"><span data-stu-id="c1ff3-822">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)



> [!div class="step-by-step"]
> <span data-ttu-id="c1ff3-823">[Předchozí](xref:data/ef-rp/migrations)Další
> [](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="c1ff3-823">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end