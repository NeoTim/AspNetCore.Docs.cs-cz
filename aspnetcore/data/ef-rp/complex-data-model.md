---
title: Razor Stránky s EF core v ASP.NET Core - Datový model - 5 z 8
author: rick-anderson
description: V tomto kurzu přidejte další entity a vztahy a přizpůsobte datový model zadáním pravidel formátování, ověření a mapování.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1d81a0444487c6396bb32381ed2cb26d44312c3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665716"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="a4c77-103">Razor Stránky s EF core v ASP.NET Core - Datový model - 5 z 8</span><span class="sxs-lookup"><span data-stu-id="a4c77-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="a4c77-104">Tom [Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a4c77-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a4c77-105">Předchozí kurzy pracoval y základní datový model, který se skládal ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="a4c77-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="a4c77-106">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="a4c77-106">In this tutorial:</span></span>

* <span data-ttu-id="a4c77-107">Jsou přidány další entity a vztahy.</span><span class="sxs-lookup"><span data-stu-id="a4c77-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="a4c77-108">Datový model je přizpůsoben zadáním pravidel formátování, ověření a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="a4c77-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="a4c77-109">Vyplněný datový model je znázorněn na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="a4c77-109">The completed data model is shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="a4c77-111">Studentská entita</span><span class="sxs-lookup"><span data-stu-id="a4c77-111">The Student entity</span></span>

![Studentská entita](complex-data-model/_static/student-entity.png)

<span data-ttu-id="a4c77-113">Nahraďte kód v *models/Student.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="a4c77-114">Předchozí kód přidá `FullName` vlastnost a přidá následující atributy k existujícím vlastnostem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="a4c77-115">Vypočtená vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="a4c77-115">The FullName calculated property</span></span>

<span data-ttu-id="a4c77-116">`FullName`je vypočtená vlastnost, která vrací hodnotu vytvořenou zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="a4c77-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="a4c77-117">`FullName`nelze nastavit, takže má pouze get přistupující ho.</span><span class="sxs-lookup"><span data-stu-id="a4c77-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="a4c77-118">V `FullName` databázi není vytvořen žádný sloupec.</span><span class="sxs-lookup"><span data-stu-id="a4c77-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="a4c77-119">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="a4c77-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="a4c77-120">U dat zápisu studenta se na všech stránkách aktuálně zobrazuje denní doba spolu s datem, i když je relevantní pouze datum.</span><span class="sxs-lookup"><span data-stu-id="a4c77-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="a4c77-121">Pomocí atributů anotace dat můžete provést jednu změnu kódu, která opraví formát zobrazení na každé stránce, která zobrazuje data.</span><span class="sxs-lookup"><span data-stu-id="a4c77-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="a4c77-122">Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) určuje datový typ, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="a4c77-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="a4c77-123">V tomto případě by mělo být zobrazeno pouze datum, nikoli datum a čas.</span><span class="sxs-lookup"><span data-stu-id="a4c77-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="a4c77-124">[Výčet datového typu](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je například datum, čas, telefonní číslo, měna, e-mailová adresa atd. Atribut `DataType` může také povolit aplikaci automaticky poskytovat funkce specifické pro daný typ.</span><span class="sxs-lookup"><span data-stu-id="a4c77-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="a4c77-125">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a4c77-125">For example:</span></span>

* <span data-ttu-id="a4c77-126">Propojení `mailto:` je automaticky `DataType.EmailAddress`vytvořeno pro aplikaci .</span><span class="sxs-lookup"><span data-stu-id="a4c77-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="a4c77-127">Volič data je k `DataType.Date` dispozici ve většině prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="a4c77-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="a4c77-128">Atribut `DataType` vyzařuje atributy `data-` HTML 5 (vyslovuje se pomlčka dat).</span><span class="sxs-lookup"><span data-stu-id="a4c77-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="a4c77-129">Atributy `DataType` neposkytují ověření.</span><span class="sxs-lookup"><span data-stu-id="a4c77-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="a4c77-130">Atribut DisplayFormat</span><span class="sxs-lookup"><span data-stu-id="a4c77-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="a4c77-131">`DataType.Date`neurčuje formát zobrazeného data.</span><span class="sxs-lookup"><span data-stu-id="a4c77-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="a4c77-132">Ve výchozím nastavení je pole data zobrazeno podle výchozích formátů založených na [serveru CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span><span class="sxs-lookup"><span data-stu-id="a4c77-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="a4c77-133">Atribut `DisplayFormat` se používá k explicitnímu určení formátu data.</span><span class="sxs-lookup"><span data-stu-id="a4c77-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="a4c77-134">Nastavení `ApplyFormatInEditMode` určuje, že formátování by mělo být použito také pro upravit e-li.</span><span class="sxs-lookup"><span data-stu-id="a4c77-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="a4c77-135">Některá pole by `ApplyFormatInEditMode`neměla používat .</span><span class="sxs-lookup"><span data-stu-id="a4c77-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="a4c77-136">Symbol měny by například obecně neměl být zobrazen v textovém poli pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="a4c77-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="a4c77-137">Atribut `DisplayFormat` lze použít samostatně.</span><span class="sxs-lookup"><span data-stu-id="a4c77-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="a4c77-138">Obecně je vhodné použít `DataType` atribut s atributem. `DisplayFormat`</span><span class="sxs-lookup"><span data-stu-id="a4c77-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="a4c77-139">Atribut `DataType` vyjadřuje sémantiku dat na rozdíl od způsobu jejich vykreslení na obrazovce.</span><span class="sxs-lookup"><span data-stu-id="a4c77-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="a4c77-140">Atribut `DataType` poskytuje následující výhody, které `DisplayFormat`nejsou k dispozici v :</span><span class="sxs-lookup"><span data-stu-id="a4c77-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="a4c77-141">Prohlížeč může povolit funkce HTML5.</span><span class="sxs-lookup"><span data-stu-id="a4c77-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="a4c77-142">Můžete například zobrazit ovládací prvek kalendáře, symbol měny odpovídající národnímu prostředí, odkazy na e-maila a ověření vstupu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a4c77-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="a4c77-143">Ve výchozím nastavení prohlížeč vykresluje data ve správném formátu založeném na národním prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4c77-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="a4c77-144">Další informace naleznete ve [ \<vstupní dokumentaci> pomocníka značky](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a4c77-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="a4c77-145">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="a4c77-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="a4c77-146">Pravidla pro ověření dat a chybové zprávy ověření lze zadat pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="a4c77-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="a4c77-147">Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli.</span><span class="sxs-lookup"><span data-stu-id="a4c77-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="a4c77-148">Zobrazený kód omezuje názvy na maximálně 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="a4c77-149">Příklad, který nastaví minimální délku řetězce, je zobrazen [později](#the-required-attribute).</span><span class="sxs-lookup"><span data-stu-id="a4c77-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="a4c77-150">Atribut `StringLength` také poskytuje ověření na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="a4c77-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="a4c77-151">Minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="a4c77-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="a4c77-152">Atribut `StringLength` nezabrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="a4c77-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="a4c77-153">Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) lze použít omezení pro vstup.</span><span class="sxs-lookup"><span data-stu-id="a4c77-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="a4c77-154">Například následující kód vyžaduje, aby první znak byl velkými písmeny a zbývající znaky byly abecední:</span><span class="sxs-lookup"><span data-stu-id="a4c77-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="a4c77-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4c77-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a4c77-156">V **Průzkumníku objektů SERVERU SQL Server** (SSOX) otevřete návrháře tabulek studenta poklepáním na tabulku **Student.**</span><span class="sxs-lookup"><span data-stu-id="a4c77-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů ve SSOX před migrací](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="a4c77-158">Předchozí obrázek znázorňuje schéma `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="a4c77-159">Pole názvu mají `nvarchar(MAX)`typ .</span><span class="sxs-lookup"><span data-stu-id="a4c77-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="a4c77-160">Při vytvoření migrace a použít později v tomto `nvarchar(50)` kurzu, pole názvu se stanou v důsledku atributy délky řetězce.</span><span class="sxs-lookup"><span data-stu-id="a4c77-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4c77-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4c77-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a4c77-162">V nástroji SQLite zkontrolujte definice sloupců pro `Student` tabulku.</span><span class="sxs-lookup"><span data-stu-id="a4c77-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="a4c77-163">Pole názvu mají `Text`typ .</span><span class="sxs-lookup"><span data-stu-id="a4c77-163">The name fields have type `Text`.</span></span> <span data-ttu-id="a4c77-164">Všimněte si, že `FirstMidName`pole křestního jména se nazývá .</span><span class="sxs-lookup"><span data-stu-id="a4c77-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="a4c77-165">V další části změníte název tohoto `FirstName`sloupce na .</span><span class="sxs-lookup"><span data-stu-id="a4c77-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="a4c77-166">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="a4c77-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="a4c77-167">Atributy můžete řídit, jak jsou třídy a vlastnosti mapovány do databáze.</span><span class="sxs-lookup"><span data-stu-id="a4c77-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="a4c77-168">V `Student` modelu `Column` se atribut používá k mapování `FirstMidName` názvu vlastnosti na "FirstName" v databázi.</span><span class="sxs-lookup"><span data-stu-id="a4c77-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="a4c77-169">Při vytvoření databáze se názvy vlastností v modelu používají `Column` pro názvy sloupců (s výjimkou případů, kdy je atribut použit).</span><span class="sxs-lookup"><span data-stu-id="a4c77-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="a4c77-170">Model `Student` používá `FirstMidName` pro pole křestního jména, protože pole může také obsahovat prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="a4c77-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="a4c77-171">S `[Column]` atributem `Student.FirstMidName` se v datovém `FirstName` modelu `Student` mapuje na sloupec tabulky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="a4c77-172">Přidání atributu `Column` změní model, `SchoolContext`který podporuje .</span><span class="sxs-lookup"><span data-stu-id="a4c77-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="a4c77-173">Model, který `SchoolContext` podporuje databázi, již neodpovídá.</span><span class="sxs-lookup"><span data-stu-id="a4c77-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="a4c77-174">Tento rozpor bude vyřešen přidáním migrace později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="a4c77-175">Atribut Povinné</span><span class="sxs-lookup"><span data-stu-id="a4c77-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="a4c77-176">Atribut `Required` vytvoří pole vlastností názvu jako povinná.</span><span class="sxs-lookup"><span data-stu-id="a4c77-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="a4c77-177">Atribut `Required` není potřeba pro typy s nulou, jako jsou `DateTime`typy `int`hodnot `double`(například , , a ).</span><span class="sxs-lookup"><span data-stu-id="a4c77-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="a4c77-178">Typy, které nemohou být nulové, jsou automaticky považovány za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="a4c77-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="a4c77-179">Atribut `Required` musí být `MinimumLength` použit `MinimumLength` s pro vynucení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="a4c77-180">`MinimumLength`a `Required` umožňují prázdné znaky pro splnění ověření.</span><span class="sxs-lookup"><span data-stu-id="a4c77-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="a4c77-181">Použijte `RegularExpression` atribut pro úplnou kontrolu nad řetězcem.</span><span class="sxs-lookup"><span data-stu-id="a4c77-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="a4c77-182">Atribut Zobrazit</span><span class="sxs-lookup"><span data-stu-id="a4c77-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="a4c77-183">Atribut `Display` určuje, že titulek pro textová pole by měl být "Jméno", "Příjmení", "Celé jméno" a "Datum zápisu".</span><span class="sxs-lookup"><span data-stu-id="a4c77-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="a4c77-184">Výchozí titulky neměly mezeru rozdělující slova, například "Příjmení".</span><span class="sxs-lookup"><span data-stu-id="a4c77-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="a4c77-185">Vytvoření migrace</span><span class="sxs-lookup"><span data-stu-id="a4c77-185">Create a migration</span></span>

<span data-ttu-id="a4c77-186">Spusťte aplikaci a přejděte na stránku Studenti.</span><span class="sxs-lookup"><span data-stu-id="a4c77-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="a4c77-187">Je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="a4c77-187">An exception is thrown.</span></span> <span data-ttu-id="a4c77-188">Atribut `[Column]` způsobí, že EF očekávat `FirstName`najít sloupec s názvem , `FirstMidName`ale název sloupce v databázi je stále .</span><span class="sxs-lookup"><span data-stu-id="a4c77-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4c77-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4c77-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a4c77-190">Chybová zpráva je podobná následujícímu příkladu:</span><span class="sxs-lookup"><span data-stu-id="a4c77-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="a4c77-191">Do pmc zadejte následující příkazy pro vytvoření nové migrace a aktualizaci databáze:</span><span class="sxs-lookup"><span data-stu-id="a4c77-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="a4c77-192">První z těchto příkazů generuje následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="a4c77-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="a4c77-193">Upozornění je generováno, protože pole názvu jsou nyní omezena na 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="a4c77-194">Pokud název v databázi měl více než 50 znaků, 51 až poslední znak by být ztraceny.</span><span class="sxs-lookup"><span data-stu-id="a4c77-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="a4c77-195">Otevřete tabulku Student ve SSOX:</span><span class="sxs-lookup"><span data-stu-id="a4c77-195">Open the Student table in SSOX:</span></span>

  ![Tabulka studentů ve SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="a4c77-197">Před použitím migrace byly sloupce názvu typu [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="a4c77-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="a4c77-198">Sloupce názvů jsou `nvarchar(50)`nyní .</span><span class="sxs-lookup"><span data-stu-id="a4c77-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="a4c77-199">Název sloupce byl `FirstMidName` změněn `FirstName`z na .</span><span class="sxs-lookup"><span data-stu-id="a4c77-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4c77-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4c77-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a4c77-201">Chybová zpráva je podobná následujícímu příkladu:</span><span class="sxs-lookup"><span data-stu-id="a4c77-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="a4c77-202">Otevřete příkazové okno ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-202">Open a command window in the project folder.</span></span> <span data-ttu-id="a4c77-203">Chcete-li vytvořit novou migraci a aktualizovat databázi, zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="a4c77-204">Příkaz aktualizace databáze zobrazí chybu jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a4c77-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="a4c77-205">V tomto kurzu je způsob, jak se dostat přes tuto chybu, odstranit a znovu vytvořit počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="a4c77-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="a4c77-206">Další informace naleznete v upozornění SQLite v horní části [kurzu migrace](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="a4c77-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="a4c77-207">Odstraňte složku *Migrace.*</span><span class="sxs-lookup"><span data-stu-id="a4c77-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="a4c77-208">Spuštěním následujících příkazů přetažením databáze, vytvořením nové počáteční migrace a aplikováním migrace:</span><span class="sxs-lookup"><span data-stu-id="a4c77-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="a4c77-209">Prohlédněte si tabulku Student v nástroji SQLite.</span><span class="sxs-lookup"><span data-stu-id="a4c77-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="a4c77-210">Sloupec, který byl FirstMidName je nyní FirstName.</span><span class="sxs-lookup"><span data-stu-id="a4c77-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="a4c77-211">Spusťte aplikaci a přejděte na stránku Studenti.</span><span class="sxs-lookup"><span data-stu-id="a4c77-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="a4c77-212">Všimněte si, že časy nejsou zadány nebo zobrazeny spolu s daty.</span><span class="sxs-lookup"><span data-stu-id="a4c77-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="a4c77-213">Vyberte **Vytvořit nový**a pokuste se zadat název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="a4c77-214">V následujících částech vytváření aplikace v některých fázích generuje chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="a4c77-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="a4c77-215">Pokyny určují, kdy se má aplikace sestavit.</span><span class="sxs-lookup"><span data-stu-id="a4c77-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="a4c77-216">Entita instruktora</span><span class="sxs-lookup"><span data-stu-id="a4c77-216">The Instructor Entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="a4c77-218">Vytvořit *modely/Instructor.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="a4c77-219">Více atributů může být na jednom řádku.</span><span class="sxs-lookup"><span data-stu-id="a4c77-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="a4c77-220">Atributy `HireDate` mohou být zapsány takto:</span><span class="sxs-lookup"><span data-stu-id="a4c77-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="a4c77-221">Navigační vlastnosti</span><span class="sxs-lookup"><span data-stu-id="a4c77-221">Navigation properties</span></span>

<span data-ttu-id="a4c77-222">`CourseAssignments` Vlastnosti `OfficeAssignment` a jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a4c77-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="a4c77-223">Instruktor může vyučovat `CourseAssignments` libovolný počet kurzů, takže je definován jako sbírka.</span><span class="sxs-lookup"><span data-stu-id="a4c77-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="a4c77-224">Instruktor může mít maximálně jednu `OfficeAssignment` kancelář, `OfficeAssignment` takže vlastnost má jednu entitu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="a4c77-225">`OfficeAssignment`je null, pokud není přiřazena žádná kancelář.</span><span class="sxs-lookup"><span data-stu-id="a4c77-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="a4c77-226">Entita OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="a4c77-226">The OfficeAssignment entity</span></span>

![Entita OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="a4c77-228">Vytvořit *modely/OfficeAssignment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="a4c77-229">Atribut Key</span><span class="sxs-lookup"><span data-stu-id="a4c77-229">The Key attribute</span></span>

<span data-ttu-id="a4c77-230">Atribut `[Key]` se používá k identifikaci vlastnosti jako primární klíč (PK), pokud název vlastnosti je něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="a4c77-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="a4c77-231">Existuje vztah jedna k nule nebo jeden mezi `Instructor` `OfficeAssignment` entitami a.</span><span class="sxs-lookup"><span data-stu-id="a4c77-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="a4c77-232">Přiřazení kanceláře existuje pouze ve vztahu k instruktorovi, ke kterým je přiřazen.</span><span class="sxs-lookup"><span data-stu-id="a4c77-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="a4c77-233">`OfficeAssignment` PK je také jeho cizí klíč (FK) k účetní jednotce. `Instructor`</span><span class="sxs-lookup"><span data-stu-id="a4c77-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="a4c77-234">EF Core nelze automaticky `InstructorID` rozpoznat jako `OfficeAssignment` PK, protože `InstructorID` nedodržuje ID nebo classnameID konvence pojmenování.</span><span class="sxs-lookup"><span data-stu-id="a4c77-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="a4c77-235">Proto `Key` atribut slouží k `InstructorID` identifikaci jako PK:</span><span class="sxs-lookup"><span data-stu-id="a4c77-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="a4c77-236">Ve výchozím nastavení EF Core považuje klíč za negenerovaný jako nedatabázový, protože sloupec je určen pro identifikační vztah.</span><span class="sxs-lookup"><span data-stu-id="a4c77-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="a4c77-237">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="a4c77-237">The Instructor navigation property</span></span>

<span data-ttu-id="a4c77-238">Vlastnost `Instructor.OfficeAssignment` navigace může být null, protože `OfficeAssignment` nemusí být řádek pro daného instruktora.</span><span class="sxs-lookup"><span data-stu-id="a4c77-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="a4c77-239">Instruktor nemusí mít úkol v kanceláři.</span><span class="sxs-lookup"><span data-stu-id="a4c77-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="a4c77-240">Vlastnost `OfficeAssignment.Instructor` navigace bude mít vždy entitu `InstructorID` instruktora, protože typ cizího klíče je `int`, typ hodnoty s hodnotou, kterou lze nehodnotitelnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="a4c77-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="a4c77-241">Úkol v kanceláři nemůže existovat bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="a4c77-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="a4c77-242">Pokud `Instructor` má entita související `OfficeAssignment` entitu, každá entita má odkaz na druhou ve své navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a4c77-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="a4c77-243">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="a4c77-243">The Course Entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="a4c77-245">Aktualizovat *modely/Course.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="a4c77-246">Entita `Course` má vlastnost `DepartmentID`cizího klíče (FK).</span><span class="sxs-lookup"><span data-stu-id="a4c77-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="a4c77-247">`DepartmentID`odkazuje na `Department` související subjekt.</span><span class="sxs-lookup"><span data-stu-id="a4c77-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="a4c77-248">Entita `Course` `Department` má navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a4c77-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="a4c77-249">EF Core nevyžaduje vlastnost cizího klíče pro datový model, když model má vlastnost navigace pro související entitu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="a4c77-250">EF Core automaticky vytvoří FKs v databázi všude tam, kde jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="a4c77-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="a4c77-251">EF Core vytváří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automaticky vytvořené Soubory FK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="a4c77-252">Explicitní zahrnutí FK do datového modelu však může usnadnit a zefektivnit aktualizace.</span><span class="sxs-lookup"><span data-stu-id="a4c77-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="a4c77-253">Zvažte například model, kde `DepartmentID` *není* zahrnuta vlastnost FK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="a4c77-254">Když je entita kurzu načtena k úpravám:</span><span class="sxs-lookup"><span data-stu-id="a4c77-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="a4c77-255">Vlastnost `Department` je null, pokud není explicitně načtena.</span><span class="sxs-lookup"><span data-stu-id="a4c77-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="a4c77-256">Chcete-li aktualizovat entitu kurzu, `Department` musí být entita nejprve načtena.</span><span class="sxs-lookup"><span data-stu-id="a4c77-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="a4c77-257">Když je vlastnost `DepartmentID` FK zahrnuta v datovém modelu, `Department` není nutné načítat entitu před aktualizací.</span><span class="sxs-lookup"><span data-stu-id="a4c77-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="a4c77-258">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="a4c77-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="a4c77-259">Atribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` určuje, že PK je poskytována aplikací, nikoli generována databází.</span><span class="sxs-lookup"><span data-stu-id="a4c77-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="a4c77-260">Ve výchozím nastavení EF Core předpokládá, že hodnoty PK jsou generovány databází.</span><span class="sxs-lookup"><span data-stu-id="a4c77-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="a4c77-261">Databáze generované je obecně nejlepší přístup.</span><span class="sxs-lookup"><span data-stu-id="a4c77-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="a4c77-262">Pro `Course` entity uživatel určuje PK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="a4c77-263">Například číslo kurzu, například série 1000 pro matematické oddělení, série 2000 pro anglické oddělení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="a4c77-264">Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="a4c77-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="a4c77-265">Databáze může například automaticky generovat pole data pro záznam data vytvoření nebo aktualizace řádku.</span><span class="sxs-lookup"><span data-stu-id="a4c77-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="a4c77-266">Další informace naleznete v tématu [Generated Properties](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="a4c77-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="a4c77-267">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="a4c77-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="a4c77-268">Vlastnosti cizího klíče (FK) `Course` a navigační vlastnosti v entitě odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="a4c77-269">Kurz je přiřazen k jednomu oddělení, `DepartmentID` takže je `Department` tu FK a navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a4c77-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="a4c77-270">Kurz může mít libovolný počet studentů zapsaných `Enrollments` v něm, takže navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="a4c77-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="a4c77-271">Kurz může být vyučován `CourseAssignments` více instruktory, takže navigační vlastnost je sbírka:</span><span class="sxs-lookup"><span data-stu-id="a4c77-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="a4c77-272">`CourseAssignment`je [vysvětleno později](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="a4c77-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="a4c77-273">Subjekt oddělení</span><span class="sxs-lookup"><span data-stu-id="a4c77-273">The Department entity</span></span>

![Subjekt oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="a4c77-275">Vytvořit *modely/Department.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="a4c77-276">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="a4c77-276">The Column attribute</span></span>

<span data-ttu-id="a4c77-277">Dříve `Column` byl atribut použit ke změně mapování názvů sloupců.</span><span class="sxs-lookup"><span data-stu-id="a4c77-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="a4c77-278">V kódu `Department` entity se `Column` atribut používá ke změně mapování datového typu SQL.</span><span class="sxs-lookup"><span data-stu-id="a4c77-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="a4c77-279">Sloupec `Budget` je definován pomocí typu peněz serveru SQL Server v databázi:</span><span class="sxs-lookup"><span data-stu-id="a4c77-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="a4c77-280">Mapování sloupců se obvykle nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="a4c77-280">Column mapping is generally not required.</span></span> <span data-ttu-id="a4c77-281">EF Core zvolí příslušný datový typ SERVERU SQL Server na základě typu CLR pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a4c77-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="a4c77-282">Typ CLR `decimal` se mapuje `decimal` na typ serveru SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a4c77-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="a4c77-283">`Budget`je pro měnu a datový typ peněz je vhodnější pro měnu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="a4c77-284">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="a4c77-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="a4c77-285">Vlastnosti FK a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="a4c77-286">Oddělení může nebo nemusí mít správce.</span><span class="sxs-lookup"><span data-stu-id="a4c77-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="a4c77-287">Správce je vždy instruktor.</span><span class="sxs-lookup"><span data-stu-id="a4c77-287">An administrator is always an instructor.</span></span> <span data-ttu-id="a4c77-288">Proto `InstructorID` je vlastnost zahrnuta jako `Instructor` FK k entitě.</span><span class="sxs-lookup"><span data-stu-id="a4c77-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="a4c77-289">Vlastnost navigace je `Administrator` pojmenována, ale obsahuje entitu: `Instructor`</span><span class="sxs-lookup"><span data-stu-id="a4c77-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="a4c77-290">Otazník (?) v předchozím kódu určuje vlastnost je nullable.</span><span class="sxs-lookup"><span data-stu-id="a4c77-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="a4c77-291">Oddělení může mít mnoho kurzů, takže je navigační vlastnost Kurzů:</span><span class="sxs-lookup"><span data-stu-id="a4c77-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="a4c77-292">Podle konvence EF Core umožňuje kaskádové odstranění pro soubory FK s možnou nenulovou hodnotou a pro vztahy N:N.</span><span class="sxs-lookup"><span data-stu-id="a4c77-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="a4c77-293">Toto výchozí chování může mít za následek cyklické kaskády odstranit pravidla.</span><span class="sxs-lookup"><span data-stu-id="a4c77-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="a4c77-294">Kruhová pravidla kaskádového odstranění způsobí výjimku při přidání migrace.</span><span class="sxs-lookup"><span data-stu-id="a4c77-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="a4c77-295">Například pokud `Department.InstructorID` vlastnost byla definována jako nelze upustit, EF Core by nakonfigurovat pravidlo kaskádového odstranění.</span><span class="sxs-lookup"><span data-stu-id="a4c77-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="a4c77-296">V takovém případě by oddělení bylo odstraněno při odstranění instruktora přiřazeného jako správce.</span><span class="sxs-lookup"><span data-stu-id="a4c77-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="a4c77-297">V tomto scénáři omezení pravidlo by větší smysl.</span><span class="sxs-lookup"><span data-stu-id="a4c77-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="a4c77-298">Následující [fluent API](#fluent-api-alternative-to-attributes) by nastavit pravidlo omezení a zakázat kaskádové odstranění.</span><span class="sxs-lookup"><span data-stu-id="a4c77-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="a4c77-299">Entita Zápis</span><span class="sxs-lookup"><span data-stu-id="a4c77-299">The Enrollment entity</span></span>

<span data-ttu-id="a4c77-300">Záznam zápisu je pro jeden kurz přijatý jedním studentem.</span><span class="sxs-lookup"><span data-stu-id="a4c77-300">An enrollment record is for one course taken by one student.</span></span>

![Entita zápisu](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="a4c77-302">Aktualizujte *modely/soubor Enrollment.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="a4c77-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="a4c77-303">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="a4c77-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="a4c77-304">Vlastnosti FK a navigační vlastnosti odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="a4c77-305">Záznam zápisu je pro jeden kurz, `CourseID` takže je `Course` vlastnost FK a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="a4c77-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="a4c77-306">Záznam zápisu je pro jednoho studenta, takže `StudentID` je `Student` vlastnost FK a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="a4c77-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="a4c77-307">Vztahy N:N</span><span class="sxs-lookup"><span data-stu-id="a4c77-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="a4c77-308">Existuje vztah n:N mezi entitami `Student` a. `Course`</span><span class="sxs-lookup"><span data-stu-id="a4c77-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="a4c77-309">Entita `Enrollment` funguje jako tabulka spojení N:N *s datovou částí* v databázi.</span><span class="sxs-lookup"><span data-stu-id="a4c77-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="a4c77-310">"S datovou částí" znamená, že `Enrollment` tabulka obsahuje další data kromě FKs `Grade`pro spojené tabulky (v tomto případě PK a ).</span><span class="sxs-lookup"><span data-stu-id="a4c77-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="a4c77-311">Následující obrázek znázorňuje, jak tyto vztahy vypadají v diagramu entity.</span><span class="sxs-lookup"><span data-stu-id="a4c77-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="a4c77-312">(Tento diagram byl generován pomocí [EF elektrické nástroje](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="a4c77-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="a4c77-313">Vytvoření diagramu není součástí kurzu.)</span><span class="sxs-lookup"><span data-stu-id="a4c77-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Student-Kurz mnoho k mnoha vztahům](complex-data-model/_static/student-course.png)

<span data-ttu-id="a4c77-315">Každá čára vztahu má 1 na jednom konci a hvězdičku (\*) na straně druhé, označující vztah 1:N.</span><span class="sxs-lookup"><span data-stu-id="a4c77-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="a4c77-316">Pokud `Enrollment` tabulka neobsahuje informace o hodnocení, musí obsahovat pouze dva`CourseID` `StudentID`fks ( a ).</span><span class="sxs-lookup"><span data-stu-id="a4c77-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="a4c77-317">Tabulka spojení N:N bez datové části se někdy nazývá čistá tabulka spojení (PJT).</span><span class="sxs-lookup"><span data-stu-id="a4c77-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="a4c77-318">Entity `Instructor` `Course` a mají relaci N:N pomocí tabulky čistého spojení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="a4c77-319">Poznámka: EF 6.x podporuje implicitní spojit tabulky pro relace N:N, ale EF Core není.</span><span class="sxs-lookup"><span data-stu-id="a4c77-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="a4c77-320">Další informace naleznete [v tématu Relace N:N v EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="a4c77-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="a4c77-321">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="a4c77-321">The CourseAssignment entity</span></span>

![Entita CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="a4c77-323">Vytvořit *modely/CourseAssignment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="a4c77-324">Relace "N:N) instruktor-kurzy vyžaduje tabulku spojení a entita pro tuto tabulku spojení je CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="a4c77-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Instruktor-kurzy m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="a4c77-326">Je běžné pojmenovat entitu `EntityName1EntityName2`spojení .</span><span class="sxs-lookup"><span data-stu-id="a4c77-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="a4c77-327">Například tabulka spojení Instruktor-kurzy pomocí tohoto `CourseInstructor`vzoru by byla .</span><span class="sxs-lookup"><span data-stu-id="a4c77-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="a4c77-328">Doporučujeme však použít název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="a4c77-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="a4c77-329">Datové modely začínají jednoduše a rostou.</span><span class="sxs-lookup"><span data-stu-id="a4c77-329">Data models start out simple and grow.</span></span> <span data-ttu-id="a4c77-330">Spojit tabulky bez datové části (PJTs) často vyvíjet zahrnout datové části.</span><span class="sxs-lookup"><span data-stu-id="a4c77-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="a4c77-331">Počínaje popisným názvem entity se název nemusí měnit při změně tabulky spojení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="a4c77-332">V ideálním případě by entita spojení měla svůj vlastní přirozený název (případně jedno slovo) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="a4c77-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="a4c77-333">Knihy a zákazníci mohou být například propojeni s entitou spojení nazvanou Hodnocení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="a4c77-334">Pro vztah "Instruktor-kurzy n:N) `CourseAssignment` je `CourseInstructor`upřednostňován před .</span><span class="sxs-lookup"><span data-stu-id="a4c77-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="a4c77-335">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="a4c77-335">Composite key</span></span>

<span data-ttu-id="a4c77-336">Dva fks `CourseAssignment` v`InstructorID` `CourseID`( a ) společně jednoznačně `CourseAssignment` identifikovat každý řádek tabulky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="a4c77-337">`CourseAssignment`nevyžaduje vyhrazenou PK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="a4c77-338">`InstructorID` Vlastnosti `CourseID` a fungují jako složený PK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="a4c77-339">Jediný způsob, jak určit složené PKs na EF Core je s *fluent API*.</span><span class="sxs-lookup"><span data-stu-id="a4c77-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="a4c77-340">V další části je uvedeno, jak nakonfigurovat složený PK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="a4c77-341">Složený klíč zajišťuje, že:</span><span class="sxs-lookup"><span data-stu-id="a4c77-341">The composite key ensures that:</span></span>

* <span data-ttu-id="a4c77-342">Pro jeden kurz je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="a4c77-343">Pro jednoho instruktora je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="a4c77-344">Pro stejného instruktora a kurz není povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="a4c77-345">Entita `Enrollment` spojení definuje vlastní PK, takže jsou možné duplikáty tohoto druhu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="a4c77-346">Chcete-li zabránit těmto duplikátům:</span><span class="sxs-lookup"><span data-stu-id="a4c77-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="a4c77-347">Přidejte jedinečný index do polí FK nebo</span><span class="sxs-lookup"><span data-stu-id="a4c77-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="a4c77-348">Konfigurace `Enrollment` s primárním složeným klíčem podobným . `CourseAssignment`</span><span class="sxs-lookup"><span data-stu-id="a4c77-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="a4c77-349">Další informace naleznete v tématu [Indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="a4c77-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="a4c77-350">Aktualizace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="a4c77-350">Update the database context</span></span>

<span data-ttu-id="a4c77-351">Aktualizovat *data/SchoolContext.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="a4c77-352">Předchozí kód přidá nové entity a nakonfiguruje `CourseAssignment` složené PK entity.</span><span class="sxs-lookup"><span data-stu-id="a4c77-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="a4c77-353">Alternativa rozhraní FLUENT API k atributům</span><span class="sxs-lookup"><span data-stu-id="a4c77-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="a4c77-354">Metoda `OnModelCreating` v předchozím kódu používá *plynulé rozhraní API* ke konfiguraci chování EF Core.</span><span class="sxs-lookup"><span data-stu-id="a4c77-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="a4c77-355">Rozhraní API se nazývá "plynulý", protože se často používá navlékání matné řady volání metody společně do jednoho příkazu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="a4c77-356">[Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem plynulérozhraní API:</span><span class="sxs-lookup"><span data-stu-id="a4c77-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="a4c77-357">V tomto kurzu plynulá rozhraní API se používá pouze pro mapování databáze, které nelze provést s atributy.</span><span class="sxs-lookup"><span data-stu-id="a4c77-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="a4c77-358">Rozhraní FLUENT API však může určit většinu pravidel formátování, ověřování a mapování, která lze provést pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="a4c77-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="a4c77-359">Některé atributy, `MinimumLength` jako je například nelze použít s plynulý rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a4c77-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="a4c77-360">`MinimumLength`nezmění schéma, použije pouze pravidlo ověření minimální délky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="a4c77-361">Někteří vývojáři dávají přednost použití rozhraní FLUENT API výhradně tak, aby mohli zachovat své třídy entit "čisté".</span><span class="sxs-lookup"><span data-stu-id="a4c77-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="a4c77-362">Atributy a plynulé rozhraní API lze smíchat.</span><span class="sxs-lookup"><span data-stu-id="a4c77-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="a4c77-363">Existují některé konfigurace, které lze provést pouze s fluent API (určení složené PK).</span><span class="sxs-lookup"><span data-stu-id="a4c77-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="a4c77-364">Existují některé konfigurace, které lze provést pouze`MinimumLength`s atributy ( ).</span><span class="sxs-lookup"><span data-stu-id="a4c77-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="a4c77-365">Doporučená praxe pro použití fluent API nebo atributy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="a4c77-366">Vyberte jeden z těchto dvou přístupů.</span><span class="sxs-lookup"><span data-stu-id="a4c77-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="a4c77-367">Používejte zvolený přístup konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="a4c77-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="a4c77-368">Některé atributy použité v tomto kurzu se používají pro:</span><span class="sxs-lookup"><span data-stu-id="a4c77-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="a4c77-369">Pouze ověření `MinimumLength`(například).</span><span class="sxs-lookup"><span data-stu-id="a4c77-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="a4c77-370">Pouze konfigurace EF Core `HasKey`(například).</span><span class="sxs-lookup"><span data-stu-id="a4c77-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="a4c77-371">Validace a konfigurace EF `[StringLength(50)]`Core (například).</span><span class="sxs-lookup"><span data-stu-id="a4c77-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="a4c77-372">Další informace o atributech vs. fluent API naleznete v [tématu Metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="a4c77-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="a4c77-373">Diagram entit</span><span class="sxs-lookup"><span data-stu-id="a4c77-373">Entity diagram</span></span>

<span data-ttu-id="a4c77-374">Následující obrázek znázorňuje diagram, který EF Power Tools vytvořit pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="a4c77-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="a4c77-376">Předchozí diagram ukazuje:</span><span class="sxs-lookup"><span data-stu-id="a4c77-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="a4c77-377">Několik linií vztahu 1:N (1 až). \*</span><span class="sxs-lookup"><span data-stu-id="a4c77-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="a4c77-378">Řádek vztahu 1:0 nebo 1 (1 až 0..1) `Instructor` `OfficeAssignment` mezi entitami a.</span><span class="sxs-lookup"><span data-stu-id="a4c77-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="a4c77-379">Řádek vztahu nula nebo jeden n (0..1 až \*) mezi entitami `Instructor` a. `Department`</span><span class="sxs-lookup"><span data-stu-id="a4c77-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="a4c77-380">Osivo databáze</span><span class="sxs-lookup"><span data-stu-id="a4c77-380">Seed the database</span></span>

<span data-ttu-id="a4c77-381">Aktualizace kódu v *souboru Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="a4c77-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="a4c77-382">Předchozí kód poskytuje data osiva pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="a4c77-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="a4c77-383">Většina tohoto kódu vytvoří nové objekty entity a načte ukázková data.</span><span class="sxs-lookup"><span data-stu-id="a4c77-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="a4c77-384">Ukázková data se používají k testování.</span><span class="sxs-lookup"><span data-stu-id="a4c77-384">The sample data is used for testing.</span></span> <span data-ttu-id="a4c77-385">Viz `Enrollments` `CourseAssignments` a příklady, kolik-k-n spojit tabulky mohou být nasazeny.</span><span class="sxs-lookup"><span data-stu-id="a4c77-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="a4c77-386">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="a4c77-386">Add a migration</span></span>

<span data-ttu-id="a4c77-387">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="a4c77-387">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4c77-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4c77-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a4c77-389">V pmc spusťte následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="a4c77-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="a4c77-390">Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="a4c77-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="a4c77-391">Pokud `database update` je příkaz spuštěn, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="a4c77-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="a4c77-392">V další části se zobrazí, co dělat s touto chybou.</span><span class="sxs-lookup"><span data-stu-id="a4c77-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4c77-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4c77-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a4c77-394">Pokud přidáte migraci a `database update` spustíte příkaz, bude vytvořena následující chyba:</span><span class="sxs-lookup"><span data-stu-id="a4c77-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="a4c77-395">V další části uvidíte, jak se této chybě vyhnout.</span><span class="sxs-lookup"><span data-stu-id="a4c77-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="a4c77-396">Použití migrace nebo přetažení a opětovné vytvoření</span><span class="sxs-lookup"><span data-stu-id="a4c77-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="a4c77-397">Nyní, když máte existující databázi, je třeba přemýšlet o tom, jak použít změny na ni.</span><span class="sxs-lookup"><span data-stu-id="a4c77-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="a4c77-398">Tento kurz ukazuje dvě alternativy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="a4c77-399">[Přetažení a znovu vytvořit databázi](#drop).</span><span class="sxs-lookup"><span data-stu-id="a4c77-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="a4c77-400">Tuto část zvolte, pokud používáte SQLite.</span><span class="sxs-lookup"><span data-stu-id="a4c77-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="a4c77-401">[Použijte migraci na existující databázi](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="a4c77-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="a4c77-402">Pokyny v této části pracovat pouze pro SQL Server, **nikoli pro SQLite**.</span><span class="sxs-lookup"><span data-stu-id="a4c77-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="a4c77-403">Obě volby funguje pro SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a4c77-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="a4c77-404">Zatímco metoda apply-migration je složitější a časově náročná, je to upřednostňovaný přístup pro skutečná produkční prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4c77-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="a4c77-405">Přetažení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="a4c77-405">Drop and re-create the database</span></span>

<span data-ttu-id="a4c77-406">[Přeskočit tuto část,](#apply-the-migration) pokud používáte SQL Server a chcete provést přístup apply-migration v následující části.</span><span class="sxs-lookup"><span data-stu-id="a4c77-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="a4c77-407">Chcete-li vynutit EF Core k vytvoření nové databáze, přetáhněte a aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="a4c77-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4c77-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4c77-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a4c77-409">V **konzole Správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a4c77-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="a4c77-410">Odstraňte složku *Migrace* a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a4c77-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4c77-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4c77-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a4c77-412">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="a4c77-413">Složka projektu obsahuje soubor *ContosoUniversity.csproj.*</span><span class="sxs-lookup"><span data-stu-id="a4c77-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="a4c77-414">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a4c77-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="a4c77-415">Odstraňte složku *Migrace* a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a4c77-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="a4c77-416">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a4c77-416">Run the app.</span></span> <span data-ttu-id="a4c77-417">Spuštění aplikace spustí `DbInitializer.Initialize` metodu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="a4c77-418">Naplní `DbInitializer.Initialize` novou databázi.</span><span class="sxs-lookup"><span data-stu-id="a4c77-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4c77-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4c77-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a4c77-420">Otevřete databázi ve SSOX:</span><span class="sxs-lookup"><span data-stu-id="a4c77-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="a4c77-421">Pokud byl ssox otevřen dříve, klepněte na tlačítko **Aktualizovat.**</span><span class="sxs-lookup"><span data-stu-id="a4c77-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="a4c77-422">Rozbalte uzel **Tabulky.**</span><span class="sxs-lookup"><span data-stu-id="a4c77-422">Expand the **Tables** node.</span></span> <span data-ttu-id="a4c77-423">Zobrazí se vytvořené tabulky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-423">The created tables are displayed.</span></span>

  ![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="a4c77-425">Prohlédněte si tabulku **CourseAssignment:**</span><span class="sxs-lookup"><span data-stu-id="a4c77-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="a4c77-426">Klepněte pravým tlačítkem myši na tabulku **CourseAssignment** a vyberte **zobrazit data**.</span><span class="sxs-lookup"><span data-stu-id="a4c77-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="a4c77-427">Ověřte, zda tabulka **CourseAssignment** obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="a4c77-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![CourseAssignment data v SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4c77-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4c77-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a4c77-430">Pomocí nástroje SQLite zkontrolujte databázi:</span><span class="sxs-lookup"><span data-stu-id="a4c77-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="a4c77-431">Nové tabulky a sloupce.</span><span class="sxs-lookup"><span data-stu-id="a4c77-431">New tables and columns.</span></span>
* <span data-ttu-id="a4c77-432">Nasazená data v tabulkách, například tabulka **CourseAssignment.**</span><span class="sxs-lookup"><span data-stu-id="a4c77-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="a4c77-433">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="a4c77-433">Apply the migration</span></span>

<span data-ttu-id="a4c77-434">Tato část je nepovinná.</span><span class="sxs-lookup"><span data-stu-id="a4c77-434">This section is optional.</span></span> <span data-ttu-id="a4c77-435">Tyto kroky fungují pouze pro SQL Server LocalDB a pouze v případě, že jste přeskočili předchozí [drop a znovu vytvořit databázový](#drop) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4c77-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="a4c77-436">Při migraci jsou spuštěny s existující mise, může být FK omezení, které nejsou spokojeni s existující data.</span><span class="sxs-lookup"><span data-stu-id="a4c77-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="a4c77-437">S produkčními daty je nutné podniknout kroky k migraci existujících dat.</span><span class="sxs-lookup"><span data-stu-id="a4c77-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="a4c77-438">Tato část obsahuje příklad opravy porušení omezení FK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="a4c77-439">Neprováděte tyto změny kódu bez zálohy.</span><span class="sxs-lookup"><span data-stu-id="a4c77-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="a4c77-440">Neprováděte tyto změny kódu, pokud jste dokončili předchozí [drop a znovu vytvořit databázový](#drop) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4c77-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="a4c77-441">Soubor *{timestamp}_ComplexDataModel.cs* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="a4c77-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="a4c77-442">Předchozí kód přidá do `DepartmentID` `Course` tabulky soubor FK, který lze udát.</span><span class="sxs-lookup"><span data-stu-id="a4c77-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="a4c77-443">Databáze z předchozího kurzu `Course`obsahuje řádky v aplikaci , takže tabulku nelze aktualizovat migrací.</span><span class="sxs-lookup"><span data-stu-id="a4c77-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="a4c77-444">Chcete-li, aby migrace fungovala `ComplexDataModel` s existujícími daty:</span><span class="sxs-lookup"><span data-stu-id="a4c77-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="a4c77-445">Změňte kód tak, aby`DepartmentID`nový sloupec ( ) měl výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="a4c77-446">Vytvořte falešné oddělení s názvem "Temp", které bude fungovat jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="a4c77-447">Oprava omezení cizího klíče</span><span class="sxs-lookup"><span data-stu-id="a4c77-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="a4c77-448">Ve `ComplexDataModel` třídě migrace aktualizujte metodu: `Up`</span><span class="sxs-lookup"><span data-stu-id="a4c77-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="a4c77-449">Otevřete soubor *{timestamp}_ComplexDataModel.cs.*</span><span class="sxs-lookup"><span data-stu-id="a4c77-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="a4c77-450">Zakomentujte řádek kódu, `DepartmentID` který `Course` přidá sloupec do tabulky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="a4c77-451">Přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="a4c77-451">Add the following highlighted code.</span></span> <span data-ttu-id="a4c77-452">Nový kód jde `.CreateTable( name: "Department"` za blokem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="a4c77-453">S předchozími změnami `Course` budou existující řádky po spuštění metody `ComplexDataModel.Up` souviset s oddělením "Temp".</span><span class="sxs-lookup"><span data-stu-id="a4c77-453">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="a4c77-454">Způsob zpracování zde uvedené situace je pro tento kurz zjednodušen.</span><span class="sxs-lookup"><span data-stu-id="a4c77-454">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="a4c77-455">Produkční aplikace by:</span><span class="sxs-lookup"><span data-stu-id="a4c77-455">A production app would:</span></span>

* <span data-ttu-id="a4c77-456">Zahrňte kód `Department` nebo skripty pro přidání řádků a souvisejících `Course` řádků do nových `Department` řádků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-456">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="a4c77-457">Nepoužívejte oddělení "Temp" nebo výchozí `Course.DepartmentID`hodnotu pro .</span><span class="sxs-lookup"><span data-stu-id="a4c77-457">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4c77-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4c77-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a4c77-459">V **konzole Správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a4c77-459">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="a4c77-460">Vzhledem `DbInitializer.Initialize` k tomu, že metoda je určena pouze pro práci s prázdnou databází, použijte SSOX k odstranění všech řádků v tabulkách studenta a kurzu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-460">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="a4c77-461">(Kaskádové odstranění se postará o tabulku Registrace.)</span><span class="sxs-lookup"><span data-stu-id="a4c77-461">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4c77-462">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4c77-462">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a4c77-463">Pokud používáte SQL Server LocalDB s visual studio kód, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a4c77-463">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="a4c77-464">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a4c77-464">Run the app.</span></span> <span data-ttu-id="a4c77-465">Spuštění aplikace spustí `DbInitializer.Initialize` metodu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-465">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="a4c77-466">Naplní `DbInitializer.Initialize` novou databázi.</span><span class="sxs-lookup"><span data-stu-id="a4c77-466">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a4c77-467">Další kroky</span><span class="sxs-lookup"><span data-stu-id="a4c77-467">Next steps</span></span>

<span data-ttu-id="a4c77-468">Další dva kurzy ukazují, jak číst a aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="a4c77-468">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a4c77-469">[Předchozí kurz](xref:data/ef-rp/migrations)
> [Další kurz](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="a4c77-469">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a4c77-470">Předchozí kurzy pracoval y základní datový model, který se skládal ze tří entit.</span><span class="sxs-lookup"><span data-stu-id="a4c77-470">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="a4c77-471">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="a4c77-471">In this tutorial:</span></span>

* <span data-ttu-id="a4c77-472">Jsou přidány další entity a vztahy.</span><span class="sxs-lookup"><span data-stu-id="a4c77-472">More entities and relationships are added.</span></span>
* <span data-ttu-id="a4c77-473">Datový model je přizpůsoben zadáním pravidel formátování, ověření a mapování databáze.</span><span class="sxs-lookup"><span data-stu-id="a4c77-473">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="a4c77-474">Třídy entit pro dokončený datový model jsou zobrazeny na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="a4c77-474">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="a4c77-476">Pokud narazíte na problémy, které nelze vyřešit, stáhněte si [dokončenou aplikaci](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="a4c77-476">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="a4c77-477">Přizpůsobení datového modelu pomocí atributů</span><span class="sxs-lookup"><span data-stu-id="a4c77-477">Customize the data model with attributes</span></span>

<span data-ttu-id="a4c77-478">V této části je datový model přizpůsoben pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="a4c77-478">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="a4c77-479">Atribut DataType</span><span class="sxs-lookup"><span data-stu-id="a4c77-479">The DataType attribute</span></span>

<span data-ttu-id="a4c77-480">Stránky studenta aktuálně zobrazují čas data zápisu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-480">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="a4c77-481">Pole kalendářních dat obvykle zobrazují pouze datum a nikoli čas.</span><span class="sxs-lookup"><span data-stu-id="a4c77-481">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="a4c77-482">Aktualizace *modelů/student.cs* s následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-482">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="a4c77-483">Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) určuje datový typ, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="a4c77-483">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="a4c77-484">V tomto případě by mělo být zobrazeno pouze datum, nikoli datum a čas.</span><span class="sxs-lookup"><span data-stu-id="a4c77-484">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="a4c77-485">[Výčet datového typu](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je například datum, čas, telefonní číslo, měna, e-mailová adresa atd. Atribut `DataType` může také povolit aplikaci automaticky poskytovat funkce specifické pro daný typ.</span><span class="sxs-lookup"><span data-stu-id="a4c77-485">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="a4c77-486">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a4c77-486">For example:</span></span>

* <span data-ttu-id="a4c77-487">Propojení `mailto:` je automaticky `DataType.EmailAddress`vytvořeno pro aplikaci .</span><span class="sxs-lookup"><span data-stu-id="a4c77-487">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="a4c77-488">Volič data je k `DataType.Date` dispozici ve většině prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="a4c77-488">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="a4c77-489">Atribut `DataType` vyzařuje atributy `data-` HTML 5 (vyslovuje se pomlčka dat), které prohlížeče HTML 5 spotřebovávají.</span><span class="sxs-lookup"><span data-stu-id="a4c77-489">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="a4c77-490">Atributy `DataType` neposkytují ověření.</span><span class="sxs-lookup"><span data-stu-id="a4c77-490">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="a4c77-491">`DataType.Date`neurčuje formát zobrazeného data.</span><span class="sxs-lookup"><span data-stu-id="a4c77-491">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="a4c77-492">Ve výchozím nastavení je pole data zobrazeno podle výchozích formátů založených na [serveru CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span><span class="sxs-lookup"><span data-stu-id="a4c77-492">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="a4c77-493">Atribut `DisplayFormat` se používá k explicitnímu zadání formátu data:</span><span class="sxs-lookup"><span data-stu-id="a4c77-493">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="a4c77-494">Nastavení `ApplyFormatInEditMode` určuje, že formátování by mělo být použito také pro upravit e-li.</span><span class="sxs-lookup"><span data-stu-id="a4c77-494">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="a4c77-495">Některá pole by `ApplyFormatInEditMode`neměla používat .</span><span class="sxs-lookup"><span data-stu-id="a4c77-495">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="a4c77-496">Symbol měny by například obecně neměl být zobrazen v textovém poli pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="a4c77-496">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="a4c77-497">Atribut `DisplayFormat` lze použít samostatně.</span><span class="sxs-lookup"><span data-stu-id="a4c77-497">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="a4c77-498">Obecně je vhodné použít `DataType` atribut s atributem. `DisplayFormat`</span><span class="sxs-lookup"><span data-stu-id="a4c77-498">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="a4c77-499">Atribut `DataType` vyjadřuje sémantiku dat na rozdíl od způsobu jejich vykreslení na obrazovce.</span><span class="sxs-lookup"><span data-stu-id="a4c77-499">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="a4c77-500">Atribut `DataType` poskytuje následující výhody, které `DisplayFormat`nejsou k dispozici v :</span><span class="sxs-lookup"><span data-stu-id="a4c77-500">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="a4c77-501">Prohlížeč může povolit funkce HTML5.</span><span class="sxs-lookup"><span data-stu-id="a4c77-501">The browser can enable HTML5 features.</span></span> <span data-ttu-id="a4c77-502">Můžete například zobrazit ovládací prvek kalendáře, symbol měny odpovídající národnímu prostředí, odkazy e-mailu, ověření vstupu na straně klienta atd.</span><span class="sxs-lookup"><span data-stu-id="a4c77-502">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="a4c77-503">Ve výchozím nastavení prohlížeč vykresluje data ve správném formátu založeném na národním prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4c77-503">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="a4c77-504">Další informace naleznete ve [ \<vstupní dokumentaci> pomocníka značky](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a4c77-504">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="a4c77-505">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a4c77-505">Run the app.</span></span> <span data-ttu-id="a4c77-506">Přejděte na stránku Studentský index.</span><span class="sxs-lookup"><span data-stu-id="a4c77-506">Navigate to the Students Index page.</span></span> <span data-ttu-id="a4c77-507">Časy se již nezobrazují.</span><span class="sxs-lookup"><span data-stu-id="a4c77-507">Times are no longer displayed.</span></span> <span data-ttu-id="a4c77-508">Každý pohled, `Student` který používá model, zobrazuje datum bez času.</span><span class="sxs-lookup"><span data-stu-id="a4c77-508">Every view that uses the `Student` model displays the date without time.</span></span>

![Studenty index stránku zobrazující data bez časů](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="a4c77-510">Atribut StringLength</span><span class="sxs-lookup"><span data-stu-id="a4c77-510">The StringLength attribute</span></span>

<span data-ttu-id="a4c77-511">Pravidla pro ověření dat a chybové zprávy ověření lze zadat pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="a4c77-511">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="a4c77-512">Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli.</span><span class="sxs-lookup"><span data-stu-id="a4c77-512">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="a4c77-513">Atribut `StringLength` také poskytuje ověření na straně klienta a serveru.</span><span class="sxs-lookup"><span data-stu-id="a4c77-513">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="a4c77-514">Minimální hodnota nemá žádný vliv na schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="a4c77-514">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="a4c77-515">Aktualizujte `Student` model následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-515">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="a4c77-516">Předchozí kód omezuje názvy na maximálně 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-516">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="a4c77-517">Atribut `StringLength` nezabrání uživateli v zadání prázdného místa pro název.</span><span class="sxs-lookup"><span data-stu-id="a4c77-517">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="a4c77-518">Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) se používá k použití omezení vstupu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-518">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="a4c77-519">Například následující kód vyžaduje, aby první znak byl velkými písmeny a zbývající znaky byly abecední:</span><span class="sxs-lookup"><span data-stu-id="a4c77-519">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="a4c77-520">Spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="a4c77-520">Run the app:</span></span>

* <span data-ttu-id="a4c77-521">Přejděte na stránku Studenti.</span><span class="sxs-lookup"><span data-stu-id="a4c77-521">Navigate to the Students page.</span></span>
* <span data-ttu-id="a4c77-522">Vyberte **Vytvořit nový**a zadejte název delší než 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-522">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="a4c77-523">Vyberte **Vytvořit**, ověření na straně klienta zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-523">Select **Create**, client-side validation shows an error message.</span></span>

![Stránka indexu studentů zobrazující chyby délky řetězce](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="a4c77-525">V **Průzkumníku objektů SERVERU SQL Server** (SSOX) otevřete návrháře tabulek studenta poklepáním na tabulku **Student.**</span><span class="sxs-lookup"><span data-stu-id="a4c77-525">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabulka studentů ve SSOX před migrací](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="a4c77-527">Předchozí obrázek znázorňuje schéma `Student` tabulky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-527">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="a4c77-528">Pole názvů mají `nvarchar(MAX)` typ, protože migrace nebyly spuštěny v databázi.</span><span class="sxs-lookup"><span data-stu-id="a4c77-528">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="a4c77-529">Při spuštění migrace později v tomto kurzu `nvarchar(50)`se pole názvů stanou .</span><span class="sxs-lookup"><span data-stu-id="a4c77-529">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="a4c77-530">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="a4c77-530">The Column attribute</span></span>

<span data-ttu-id="a4c77-531">Atributy můžete řídit, jak jsou třídy a vlastnosti mapovány do databáze.</span><span class="sxs-lookup"><span data-stu-id="a4c77-531">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="a4c77-532">V této části `Column` atribut slouží k mapování `FirstMidName` název vlastnosti "FirstName" v DB.</span><span class="sxs-lookup"><span data-stu-id="a4c77-532">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="a4c77-533">Při vytvoření DB názvy vlastností v modelu se používají `Column` pro názvy sloupců (s výjimkou při použití atributu).</span><span class="sxs-lookup"><span data-stu-id="a4c77-533">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="a4c77-534">Model `Student` používá `FirstMidName` pro pole křestního jména, protože pole může také obsahovat prostřední jméno.</span><span class="sxs-lookup"><span data-stu-id="a4c77-534">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="a4c77-535">Aktualizujte *soubor Student.cs* následujícím zvýrazněným kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-535">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="a4c77-536">S předchozí změnou `Student.FirstMidName` se v aplikaci mapuje na `FirstName` sloupec tabulky. `Student`</span><span class="sxs-lookup"><span data-stu-id="a4c77-536">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="a4c77-537">Přidání atributu `Column` změní model, `SchoolContext`který podporuje .</span><span class="sxs-lookup"><span data-stu-id="a4c77-537">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="a4c77-538">Model, který `SchoolContext` podporuje databázi, již neodpovídá.</span><span class="sxs-lookup"><span data-stu-id="a4c77-538">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="a4c77-539">Pokud je aplikace spuštěná před použitím migrace, vygeneruje se následující výjimka:</span><span class="sxs-lookup"><span data-stu-id="a4c77-539">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="a4c77-540">Aktualizace databáze:</span><span class="sxs-lookup"><span data-stu-id="a4c77-540">To update the DB:</span></span>

* <span data-ttu-id="a4c77-541">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="a4c77-541">Build the project.</span></span>
* <span data-ttu-id="a4c77-542">Otevřete příkazové okno ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-542">Open a command window in the project folder.</span></span> <span data-ttu-id="a4c77-543">Chcete-li vytvořit novou migraci a aktualizovat databázi, zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-543">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4c77-544">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4c77-544">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4c77-545">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4c77-545">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="a4c77-546">Příkaz `migrations add ColumnFirstName` generuje následující varovnou zprávu:</span><span class="sxs-lookup"><span data-stu-id="a4c77-546">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="a4c77-547">Upozornění je generováno, protože pole názvu jsou nyní omezena na 50 znaků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-547">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="a4c77-548">Pokud název v DB měl více než 50 znaků, 51 až poslední znak by být ztraceny.</span><span class="sxs-lookup"><span data-stu-id="a4c77-548">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="a4c77-549">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a4c77-549">Test the app.</span></span>

<span data-ttu-id="a4c77-550">Otevřete tabulku Student ve SSOX:</span><span class="sxs-lookup"><span data-stu-id="a4c77-550">Open the Student table in SSOX:</span></span>

![Tabulka studentů ve SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="a4c77-552">Před migrací byly sloupce názvů typu [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="a4c77-552">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="a4c77-553">Sloupce názvů jsou `nvarchar(50)`nyní .</span><span class="sxs-lookup"><span data-stu-id="a4c77-553">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="a4c77-554">Název sloupce byl `FirstMidName` změněn `FirstName`z na .</span><span class="sxs-lookup"><span data-stu-id="a4c77-554">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="a4c77-555">V následující části vytváření aplikace v některých fázích generuje chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="a4c77-555">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="a4c77-556">Pokyny určují, kdy se má aplikace sestavit.</span><span class="sxs-lookup"><span data-stu-id="a4c77-556">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="a4c77-557">Aktualizace entity studenta</span><span class="sxs-lookup"><span data-stu-id="a4c77-557">Student entity update</span></span>

![Studentská entita](complex-data-model/_static/student-entity.png)

<span data-ttu-id="a4c77-559">Aktualizovat *modely/Student.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-559">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="a4c77-560">Atribut Povinné</span><span class="sxs-lookup"><span data-stu-id="a4c77-560">The Required attribute</span></span>

<span data-ttu-id="a4c77-561">Atribut `Required` vytvoří pole vlastností názvu jako povinná.</span><span class="sxs-lookup"><span data-stu-id="a4c77-561">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="a4c77-562">Atribut `Required` není potřeba pro typy s nulou, jako`DateTime` `int`jsou `double`typy hodnot ( , , , atd.).</span><span class="sxs-lookup"><span data-stu-id="a4c77-562">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="a4c77-563">Typy, které nemohou být nulové, jsou automaticky považovány za povinná pole.</span><span class="sxs-lookup"><span data-stu-id="a4c77-563">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="a4c77-564">Atribut `Required` může být nahrazen parametrem minimální `StringLength` délky v atributu:</span><span class="sxs-lookup"><span data-stu-id="a4c77-564">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="a4c77-565">Atribut Zobrazit</span><span class="sxs-lookup"><span data-stu-id="a4c77-565">The Display attribute</span></span>

<span data-ttu-id="a4c77-566">Atribut `Display` určuje, že titulek pro textová pole by měl být "Jméno", "Příjmení", "Celé jméno" a "Datum zápisu".</span><span class="sxs-lookup"><span data-stu-id="a4c77-566">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="a4c77-567">Výchozí titulky neměly mezeru rozdělující slova, například "Příjmení".</span><span class="sxs-lookup"><span data-stu-id="a4c77-567">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="a4c77-568">Vypočtená vlastnost FullName</span><span class="sxs-lookup"><span data-stu-id="a4c77-568">The FullName calculated property</span></span>

<span data-ttu-id="a4c77-569">`FullName`je vypočtená vlastnost, která vrací hodnotu vytvořenou zřetězením dvou dalších vlastností.</span><span class="sxs-lookup"><span data-stu-id="a4c77-569">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="a4c77-570">`FullName`nelze nastavit, má pouze get přistupující ho.</span><span class="sxs-lookup"><span data-stu-id="a4c77-570">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="a4c77-571">V `FullName` databázi není vytvořen žádný sloupec.</span><span class="sxs-lookup"><span data-stu-id="a4c77-571">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="a4c77-572">Vytvořit entitu instruktora</span><span class="sxs-lookup"><span data-stu-id="a4c77-572">Create the Instructor Entity</span></span>

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="a4c77-574">Vytvořit *modely/Instructor.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-574">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="a4c77-575">Více atributů může být na jednom řádku.</span><span class="sxs-lookup"><span data-stu-id="a4c77-575">Multiple attributes can be on one line.</span></span> <span data-ttu-id="a4c77-576">Atributy `HireDate` mohou být zapsány takto:</span><span class="sxs-lookup"><span data-stu-id="a4c77-576">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="a4c77-577">Vlastnosti navigace CourseAssignments a OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="a4c77-577">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="a4c77-578">`CourseAssignments` Vlastnosti `OfficeAssignment` a jsou navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a4c77-578">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="a4c77-579">Instruktor může vyučovat `CourseAssignments` libovolný počet kurzů, takže je definován jako sbírka.</span><span class="sxs-lookup"><span data-stu-id="a4c77-579">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="a4c77-580">Pokud vlastnost navigace obsahuje více entit:</span><span class="sxs-lookup"><span data-stu-id="a4c77-580">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="a4c77-581">Musí se jednat o typ seznamu, ve kterém lze položky přidávat, odstraňovat a aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="a4c77-581">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="a4c77-582">Typy vlastností navigace zahrnují:</span><span class="sxs-lookup"><span data-stu-id="a4c77-582">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="a4c77-583">Pokud `ICollection<T>` je zadán, EF `HashSet<T>` Core vytvoří kolekci ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-583">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="a4c77-584">Entita `CourseAssignment` je vysvětlena v části o vztazích N:N.</span><span class="sxs-lookup"><span data-stu-id="a4c77-584">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="a4c77-585">Obchodní pravidla Univerzity Contoso uvádějí, že instruktor může mít maximálně jednu kancelář.</span><span class="sxs-lookup"><span data-stu-id="a4c77-585">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="a4c77-586">Vlastnost `OfficeAssignment` obsahuje jednu `OfficeAssignment` entitu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-586">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="a4c77-587">`OfficeAssignment`je null, pokud není přiřazena žádná kancelář.</span><span class="sxs-lookup"><span data-stu-id="a4c77-587">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="a4c77-588">Vytvoření entity OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="a4c77-588">Create the OfficeAssignment entity</span></span>

![Entita OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="a4c77-590">Vytvořit *modely/OfficeAssignment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-590">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="a4c77-591">Atribut Key</span><span class="sxs-lookup"><span data-stu-id="a4c77-591">The Key attribute</span></span>

<span data-ttu-id="a4c77-592">Atribut `[Key]` se používá k identifikaci vlastnosti jako primární klíč (PK), pokud název vlastnosti je něco jiného než classnameID nebo ID.</span><span class="sxs-lookup"><span data-stu-id="a4c77-592">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="a4c77-593">Existuje vztah jedna k nule nebo jeden mezi `Instructor` `OfficeAssignment` entitami a.</span><span class="sxs-lookup"><span data-stu-id="a4c77-593">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="a4c77-594">Přiřazení kanceláře existuje pouze ve vztahu k instruktorovi, ke kterým je přiřazen.</span><span class="sxs-lookup"><span data-stu-id="a4c77-594">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="a4c77-595">`OfficeAssignment` PK je také jeho cizí klíč (FK) k účetní jednotce. `Instructor`</span><span class="sxs-lookup"><span data-stu-id="a4c77-595">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="a4c77-596">EF Core nemůže automaticky `InstructorID` rozpoznat jako `OfficeAssignment` PK, protože:</span><span class="sxs-lookup"><span data-stu-id="a4c77-596">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="a4c77-597">`InstructorID`neřídí id nebo classnameID konvence pojmenování.</span><span class="sxs-lookup"><span data-stu-id="a4c77-597">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="a4c77-598">Proto `Key` atribut slouží k `InstructorID` identifikaci jako PK:</span><span class="sxs-lookup"><span data-stu-id="a4c77-598">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="a4c77-599">Ve výchozím nastavení EF Core považuje klíč za negenerovaný jako nedatabázový, protože sloupec je určen pro identifikační vztah.</span><span class="sxs-lookup"><span data-stu-id="a4c77-599">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="a4c77-600">Navigační vlastnost instruktora</span><span class="sxs-lookup"><span data-stu-id="a4c77-600">The Instructor navigation property</span></span>

<span data-ttu-id="a4c77-601">Vlastnost `OfficeAssignment` navigace pro `Instructor` entitu je null, protože:</span><span class="sxs-lookup"><span data-stu-id="a4c77-601">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="a4c77-602">Typy odkazů (například třídy jsou null).</span><span class="sxs-lookup"><span data-stu-id="a4c77-602">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="a4c77-603">Instruktor nemusí mít úkol v kanceláři.</span><span class="sxs-lookup"><span data-stu-id="a4c77-603">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="a4c77-604">Entita `OfficeAssignment` má navigační `Instructor` vlastnost s nemožnou hodnotou null, protože:</span><span class="sxs-lookup"><span data-stu-id="a4c77-604">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="a4c77-605">`InstructorID`nenulovatelný.</span><span class="sxs-lookup"><span data-stu-id="a4c77-605">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="a4c77-606">Úkol v kanceláři nemůže existovat bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="a4c77-606">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="a4c77-607">Pokud `Instructor` má entita související `OfficeAssignment` entitu, každá entita má odkaz na druhou ve své navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a4c77-607">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="a4c77-608">Atribut `[Required]` lze použít pro `Instructor` vlastnost navigace:</span><span class="sxs-lookup"><span data-stu-id="a4c77-608">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="a4c77-609">Předchozí kód určuje, že musí existovat související instruktor.</span><span class="sxs-lookup"><span data-stu-id="a4c77-609">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="a4c77-610">Předchozí kód je zbytečné, `InstructorID` protože cizí klíč (což je také PK) je nenulovatelný.</span><span class="sxs-lookup"><span data-stu-id="a4c77-610">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="a4c77-611">Upravit entitu kurzu</span><span class="sxs-lookup"><span data-stu-id="a4c77-611">Modify the Course Entity</span></span>

![Entita kurzu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="a4c77-613">Aktualizovat *modely/Course.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-613">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="a4c77-614">Entita `Course` má vlastnost `DepartmentID`cizího klíče (FK).</span><span class="sxs-lookup"><span data-stu-id="a4c77-614">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="a4c77-615">`DepartmentID`odkazuje na `Department` související subjekt.</span><span class="sxs-lookup"><span data-stu-id="a4c77-615">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="a4c77-616">Entita `Course` `Department` má navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a4c77-616">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="a4c77-617">EF Core nevyžaduje fk vlastnost pro datový model, když model má vlastnost navigace pro související entity.</span><span class="sxs-lookup"><span data-stu-id="a4c77-617">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="a4c77-618">EF Core automaticky vytvoří FKs v databázi všude tam, kde jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="a4c77-618">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="a4c77-619">EF Core vytváří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automaticky vytvořené Soubory FK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-619">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="a4c77-620">S FK v datovém modelu může aktualizace jednodušší a efektivnější.</span><span class="sxs-lookup"><span data-stu-id="a4c77-620">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="a4c77-621">Zvažte například model, kde `DepartmentID` *není* zahrnuta vlastnost FK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-621">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="a4c77-622">Když je entita kurzu načtena k úpravám:</span><span class="sxs-lookup"><span data-stu-id="a4c77-622">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="a4c77-623">Entita `Department` je null, pokud není explicitně načtena.</span><span class="sxs-lookup"><span data-stu-id="a4c77-623">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="a4c77-624">Chcete-li aktualizovat entitu kurzu, `Department` musí být entita nejprve načtena.</span><span class="sxs-lookup"><span data-stu-id="a4c77-624">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="a4c77-625">Když je vlastnost `DepartmentID` FK zahrnuta v datovém modelu, `Department` není nutné načítat entitu před aktualizací.</span><span class="sxs-lookup"><span data-stu-id="a4c77-625">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="a4c77-626">Atribut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="a4c77-626">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="a4c77-627">Atribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` určuje, že PK je poskytována aplikací, nikoli generována databází.</span><span class="sxs-lookup"><span data-stu-id="a4c77-627">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="a4c77-628">Ve výchozím nastavení EF Core předpokládá, že hodnoty PK jsou generovány DB.</span><span class="sxs-lookup"><span data-stu-id="a4c77-628">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="a4c77-629">DB generované Hodnoty PK je obecně nejlepší přístup.</span><span class="sxs-lookup"><span data-stu-id="a4c77-629">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="a4c77-630">Pro `Course` entity uživatel určuje PK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-630">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="a4c77-631">Například číslo kurzu, například série 1000 pro matematické oddělení, série 2000 pro anglické oddělení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-631">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="a4c77-632">Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="a4c77-632">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="a4c77-633">Db může například automaticky generovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován.</span><span class="sxs-lookup"><span data-stu-id="a4c77-633">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="a4c77-634">Další informace naleznete v tématu [Generated Properties](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="a4c77-634">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="a4c77-635">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="a4c77-635">Foreign key and navigation properties</span></span>

<span data-ttu-id="a4c77-636">Vlastnosti cizího klíče (FK) `Course` a navigační vlastnosti v entitě odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-636">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="a4c77-637">Kurz je přiřazen k jednomu oddělení, `DepartmentID` takže je `Department` tu FK a navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a4c77-637">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="a4c77-638">Kurz může mít libovolný počet studentů zapsaných `Enrollments` v něm, takže navigační vlastnost je kolekce:</span><span class="sxs-lookup"><span data-stu-id="a4c77-638">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="a4c77-639">Kurz může být vyučován `CourseAssignments` více instruktory, takže navigační vlastnost je sbírka:</span><span class="sxs-lookup"><span data-stu-id="a4c77-639">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="a4c77-640">`CourseAssignment`je [vysvětleno později](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="a4c77-640">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="a4c77-641">Vytvořit entitu Oddělení</span><span class="sxs-lookup"><span data-stu-id="a4c77-641">Create the Department entity</span></span>

![Subjekt oddělení](complex-data-model/_static/department-entity.png)

<span data-ttu-id="a4c77-643">Vytvořit *modely/Department.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-643">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="a4c77-644">Atribut Column</span><span class="sxs-lookup"><span data-stu-id="a4c77-644">The Column attribute</span></span>

<span data-ttu-id="a4c77-645">Dříve `Column` byl atribut použit ke změně mapování názvů sloupců.</span><span class="sxs-lookup"><span data-stu-id="a4c77-645">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="a4c77-646">V kódu `Department` entity se `Column` atribut používá ke změně mapování datového typu SQL.</span><span class="sxs-lookup"><span data-stu-id="a4c77-646">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="a4c77-647">Sloupec `Budget` je definován pomocí typu peněz serveru SQL Server v databázi:</span><span class="sxs-lookup"><span data-stu-id="a4c77-647">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="a4c77-648">Mapování sloupců se obvykle nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="a4c77-648">Column mapping is generally not required.</span></span> <span data-ttu-id="a4c77-649">EF Core obecně zvolí příslušný datový typ SQL Server na základě typu CLR pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a4c77-649">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="a4c77-650">Typ CLR `decimal` se mapuje `decimal` na typ serveru SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a4c77-650">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="a4c77-651">`Budget`je pro měnu a datový typ peněz je vhodnější pro měnu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-651">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="a4c77-652">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="a4c77-652">Foreign key and navigation properties</span></span>

<span data-ttu-id="a4c77-653">Vlastnosti FK a navigace odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-653">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="a4c77-654">Oddělení může nebo nemusí mít správce.</span><span class="sxs-lookup"><span data-stu-id="a4c77-654">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="a4c77-655">Správce je vždy instruktor.</span><span class="sxs-lookup"><span data-stu-id="a4c77-655">An administrator is always an instructor.</span></span> <span data-ttu-id="a4c77-656">Proto `InstructorID` je vlastnost zahrnuta jako `Instructor` FK k entitě.</span><span class="sxs-lookup"><span data-stu-id="a4c77-656">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="a4c77-657">Vlastnost navigace je `Administrator` pojmenována, ale obsahuje entitu: `Instructor`</span><span class="sxs-lookup"><span data-stu-id="a4c77-657">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="a4c77-658">Otazník (?) v předchozím kódu určuje vlastnost je nullable.</span><span class="sxs-lookup"><span data-stu-id="a4c77-658">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="a4c77-659">Oddělení může mít mnoho kurzů, takže je navigační vlastnost Kurzů:</span><span class="sxs-lookup"><span data-stu-id="a4c77-659">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="a4c77-660">Poznámka: Podle konvence EF Core umožňuje kaskádové odstranění pro soubory FK, které nesmožno null, a pro vztahy N:N.</span><span class="sxs-lookup"><span data-stu-id="a4c77-660">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="a4c77-661">Kaskádové odstranění může vést k cyklickému kaskádovému odstranění pravidel.</span><span class="sxs-lookup"><span data-stu-id="a4c77-661">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="a4c77-662">Kruhová pravidla kaskádového odstranění způsobí výjimku při přidání migrace.</span><span class="sxs-lookup"><span data-stu-id="a4c77-662">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="a4c77-663">Například pokud `Department.InstructorID` vlastnost byla definována jako nenulovatelné:</span><span class="sxs-lookup"><span data-stu-id="a4c77-663">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="a4c77-664">EF Core konfiguruje pravidlo kaskádového odstranění k odstranění oddělení při odstranění instruktora.</span><span class="sxs-lookup"><span data-stu-id="a4c77-664">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="a4c77-665">Odstranění oddělení při odstranění instruktora není zamýšlené chování.</span><span class="sxs-lookup"><span data-stu-id="a4c77-665">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="a4c77-666">Následující [fluent API](#fluent-api-alternative-to-attributes) by nastavit pravidlo omezení namísto kaskády.</span><span class="sxs-lookup"><span data-stu-id="a4c77-666">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="a4c77-667">Předchozí kód zakáže kaskádové odstranění ve vztahu oddělení a instruktora.</span><span class="sxs-lookup"><span data-stu-id="a4c77-667">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="a4c77-668">Aktualizace entity Registrace</span><span class="sxs-lookup"><span data-stu-id="a4c77-668">Update the Enrollment entity</span></span>

<span data-ttu-id="a4c77-669">Záznam zápisu je pro jeden kurz přijatý jedním studentem.</span><span class="sxs-lookup"><span data-stu-id="a4c77-669">An enrollment record is for one course taken by one student.</span></span>

![Entita zápisu](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="a4c77-671">Aktualizujte *modely/soubor Enrollment.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="a4c77-671">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="a4c77-672">Vlastnosti cizího klíče a navigace</span><span class="sxs-lookup"><span data-stu-id="a4c77-672">Foreign key and navigation properties</span></span>

<span data-ttu-id="a4c77-673">Vlastnosti FK a navigační vlastnosti odrážejí následující vztahy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-673">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="a4c77-674">Záznam zápisu je pro jeden kurz, `CourseID` takže je `Course` vlastnost FK a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="a4c77-674">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="a4c77-675">Záznam zápisu je pro jednoho studenta, takže `StudentID` je `Student` vlastnost FK a navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="a4c77-675">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="a4c77-676">Vztahy N:N</span><span class="sxs-lookup"><span data-stu-id="a4c77-676">Many-to-Many Relationships</span></span>

<span data-ttu-id="a4c77-677">Existuje vztah n:N mezi entitami `Student` a. `Course`</span><span class="sxs-lookup"><span data-stu-id="a4c77-677">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="a4c77-678">Entita `Enrollment` funguje jako tabulka spojení N:N *s datovou částí* v databázi.</span><span class="sxs-lookup"><span data-stu-id="a4c77-678">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="a4c77-679">"S datovou částí" znamená, že `Enrollment` tabulka obsahuje další data kromě FKs `Grade`pro spojené tabulky (v tomto případě PK a ).</span><span class="sxs-lookup"><span data-stu-id="a4c77-679">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="a4c77-680">Následující obrázek znázorňuje, jak tyto vztahy vypadají v diagramu entity.</span><span class="sxs-lookup"><span data-stu-id="a4c77-680">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="a4c77-681">(Tento diagram byl generován pomocí [EF elektrické nástroje](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="a4c77-681">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="a4c77-682">Vytvoření diagramu není součástí kurzu.)</span><span class="sxs-lookup"><span data-stu-id="a4c77-682">Creating the diagram isn't part of the tutorial.)</span></span>

![Student-Kurz mnoho k mnoha vztahům](complex-data-model/_static/student-course.png)

<span data-ttu-id="a4c77-684">Každá čára vztahu má 1 na jednom konci a hvězdičku (\*) na straně druhé, označující vztah 1:N.</span><span class="sxs-lookup"><span data-stu-id="a4c77-684">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="a4c77-685">Pokud `Enrollment` tabulka neobsahuje informace o hodnocení, musí obsahovat pouze dva`CourseID` `StudentID`fks ( a ).</span><span class="sxs-lookup"><span data-stu-id="a4c77-685">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="a4c77-686">Tabulka spojení N:N bez datové části se někdy nazývá čistá tabulka spojení (PJT).</span><span class="sxs-lookup"><span data-stu-id="a4c77-686">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="a4c77-687">Entity `Instructor` `Course` a mají relaci N:N pomocí tabulky čistého spojení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-687">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="a4c77-688">Poznámka: EF 6.x podporuje implicitní spojit tabulky pro relace N:N, ale EF Core není.</span><span class="sxs-lookup"><span data-stu-id="a4c77-688">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="a4c77-689">Další informace naleznete [v tématu Relace N:N v EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="a4c77-689">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="a4c77-690">Entita CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="a4c77-690">The CourseAssignment entity</span></span>

![Entita CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="a4c77-692">Vytvořit *modely/CourseAssignment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-692">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="a4c77-693">Kurzy od instruktora</span><span class="sxs-lookup"><span data-stu-id="a4c77-693">Instructor-to-Courses</span></span>

![Instruktor-kurzy m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="a4c77-695">Vztah Instruktor-to-Kurzy n:N:</span><span class="sxs-lookup"><span data-stu-id="a4c77-695">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="a4c77-696">Vyžaduje tabulku spojení, která musí být reprezentována sadou entit.</span><span class="sxs-lookup"><span data-stu-id="a4c77-696">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="a4c77-697">Je čistá tabulka spojení (tabulka bez datové části).</span><span class="sxs-lookup"><span data-stu-id="a4c77-697">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="a4c77-698">Je běžné pojmenovat entitu `EntityName1EntityName2`spojení .</span><span class="sxs-lookup"><span data-stu-id="a4c77-698">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="a4c77-699">Například tabulka spojení Instruktor-kurzy pomocí tohoto `CourseInstructor`vzoru je .</span><span class="sxs-lookup"><span data-stu-id="a4c77-699">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="a4c77-700">Doporučujeme však použít název, který popisuje vztah.</span><span class="sxs-lookup"><span data-stu-id="a4c77-700">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="a4c77-701">Datové modely začínají jednoduše a rostou.</span><span class="sxs-lookup"><span data-stu-id="a4c77-701">Data models start out simple and grow.</span></span> <span data-ttu-id="a4c77-702">Spojení bez datové části (PJTs) se často vyvíjejí tak, aby zahrnovala datovou část.</span><span class="sxs-lookup"><span data-stu-id="a4c77-702">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="a4c77-703">Počínaje popisným názvem entity se název nemusí měnit při změně tabulky spojení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-703">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="a4c77-704">V ideálním případě by entita spojení měla svůj vlastní přirozený název (případně jedno slovo) v obchodní doméně.</span><span class="sxs-lookup"><span data-stu-id="a4c77-704">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="a4c77-705">Knihy a zákazníci mohou být například propojeni s entitou spojení nazvanou Hodnocení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-705">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="a4c77-706">Pro vztah "Instruktor-kurzy n:N) `CourseAssignment` je `CourseInstructor`upřednostňován před .</span><span class="sxs-lookup"><span data-stu-id="a4c77-706">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="a4c77-707">Složený klíč</span><span class="sxs-lookup"><span data-stu-id="a4c77-707">Composite key</span></span>

<span data-ttu-id="a4c77-708">FKs nelze utnout.</span><span class="sxs-lookup"><span data-stu-id="a4c77-708">FKs are not nullable.</span></span> <span data-ttu-id="a4c77-709">Dva fks `CourseAssignment` v`InstructorID` `CourseID`( a ) společně jednoznačně `CourseAssignment` identifikovat každý řádek tabulky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-709">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="a4c77-710">`CourseAssignment`nevyžaduje vyhrazenou PK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-710">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="a4c77-711">`InstructorID` Vlastnosti `CourseID` a fungují jako složený PK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-711">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="a4c77-712">Jediný způsob, jak určit složené PKs na EF Core je s *fluent API*.</span><span class="sxs-lookup"><span data-stu-id="a4c77-712">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="a4c77-713">V další části je uvedeno, jak nakonfigurovat složený PK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-713">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="a4c77-714">Kompozitní klíč zajišťuje:</span><span class="sxs-lookup"><span data-stu-id="a4c77-714">The composite key ensures:</span></span>

* <span data-ttu-id="a4c77-715">Pro jeden kurz je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-715">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="a4c77-716">Pro jednoho instruktora je povoleno více řádků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-716">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="a4c77-717">Více řádků pro stejného instruktora a kurz není povoleno.</span><span class="sxs-lookup"><span data-stu-id="a4c77-717">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="a4c77-718">Entita `Enrollment` spojení definuje vlastní PK, takže jsou možné duplikáty tohoto druhu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-718">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="a4c77-719">Chcete-li zabránit těmto duplikátům:</span><span class="sxs-lookup"><span data-stu-id="a4c77-719">To prevent such duplicates:</span></span>

* <span data-ttu-id="a4c77-720">Přidejte jedinečný index do polí FK nebo</span><span class="sxs-lookup"><span data-stu-id="a4c77-720">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="a4c77-721">Konfigurace `Enrollment` s primárním složeným klíčem podobným . `CourseAssignment`</span><span class="sxs-lookup"><span data-stu-id="a4c77-721">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="a4c77-722">Další informace naleznete v tématu [Indexy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="a4c77-722">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="a4c77-723">Aktualizace kontextu DB</span><span class="sxs-lookup"><span data-stu-id="a4c77-723">Update the DB context</span></span>

<span data-ttu-id="a4c77-724">Přidejte následující zvýrazněný kód do *data/SchoolContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="a4c77-724">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="a4c77-725">Předchozí kód přidá nové entity a nakonfiguruje `CourseAssignment` složené PK entity.</span><span class="sxs-lookup"><span data-stu-id="a4c77-725">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="a4c77-726">Alternativa rozhraní FLUENT API k atributům</span><span class="sxs-lookup"><span data-stu-id="a4c77-726">Fluent API alternative to attributes</span></span>

<span data-ttu-id="a4c77-727">Metoda `OnModelCreating` v předchozím kódu používá *plynulé rozhraní API* ke konfiguraci chování EF Core.</span><span class="sxs-lookup"><span data-stu-id="a4c77-727">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="a4c77-728">Rozhraní API se nazývá "plynulý", protože se často používá navlékání matné řady volání metody společně do jednoho příkazu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-728">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="a4c77-729">[Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem plynulérozhraní API:</span><span class="sxs-lookup"><span data-stu-id="a4c77-729">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="a4c77-730">V tomto kurzu plynulá rozhraní API se používá pouze pro mapování DB, které nelze provést s atributy.</span><span class="sxs-lookup"><span data-stu-id="a4c77-730">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="a4c77-731">Rozhraní FLUENT API však může určit většinu pravidel formátování, ověřování a mapování, která lze provést pomocí atributů.</span><span class="sxs-lookup"><span data-stu-id="a4c77-731">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="a4c77-732">Některé atributy, `MinimumLength` jako je například nelze použít s plynulý rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a4c77-732">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="a4c77-733">`MinimumLength`nezmění schéma, použije pouze pravidlo ověření minimální délky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-733">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="a4c77-734">Někteří vývojáři dávají přednost použití rozhraní FLUENT API výhradně tak, aby mohli zachovat své třídy entit "čisté".</span><span class="sxs-lookup"><span data-stu-id="a4c77-734">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="a4c77-735">Atributy a plynulé rozhraní API lze smíchat.</span><span class="sxs-lookup"><span data-stu-id="a4c77-735">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="a4c77-736">Existují některé konfigurace, které lze provést pouze s fluent API (určení složené PK).</span><span class="sxs-lookup"><span data-stu-id="a4c77-736">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="a4c77-737">Existují některé konfigurace, které lze provést pouze`MinimumLength`s atributy ( ).</span><span class="sxs-lookup"><span data-stu-id="a4c77-737">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="a4c77-738">Doporučená praxe pro použití fluent API nebo atributy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-738">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="a4c77-739">Vyberte jeden z těchto dvou přístupů.</span><span class="sxs-lookup"><span data-stu-id="a4c77-739">Choose one of these two approaches.</span></span>
* <span data-ttu-id="a4c77-740">Používejte zvolený přístup konzistentně co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="a4c77-740">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="a4c77-741">Některé atributy použité v tomto kurzu se používají pro:</span><span class="sxs-lookup"><span data-stu-id="a4c77-741">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="a4c77-742">Pouze ověření `MinimumLength`(například).</span><span class="sxs-lookup"><span data-stu-id="a4c77-742">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="a4c77-743">Pouze konfigurace EF Core `HasKey`(například).</span><span class="sxs-lookup"><span data-stu-id="a4c77-743">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="a4c77-744">Validace a konfigurace EF `[StringLength(50)]`Core (například).</span><span class="sxs-lookup"><span data-stu-id="a4c77-744">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="a4c77-745">Další informace o atributech vs. fluent API naleznete v [tématu Metody konfigurace](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="a4c77-745">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="a4c77-746">Diagram entit znázorňující vztahy</span><span class="sxs-lookup"><span data-stu-id="a4c77-746">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="a4c77-747">Následující obrázek znázorňuje diagram, který EF Power Tools vytvořit pro dokončený školní model.</span><span class="sxs-lookup"><span data-stu-id="a4c77-747">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram entit](complex-data-model/_static/diagram.png)

<span data-ttu-id="a4c77-749">Předchozí diagram ukazuje:</span><span class="sxs-lookup"><span data-stu-id="a4c77-749">The preceding diagram shows:</span></span>

* <span data-ttu-id="a4c77-750">Několik linií vztahu 1:N (1 až). \*</span><span class="sxs-lookup"><span data-stu-id="a4c77-750">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="a4c77-751">Řádek vztahu 1:0 nebo 1 (1 až 0..1) `Instructor` `OfficeAssignment` mezi entitami a.</span><span class="sxs-lookup"><span data-stu-id="a4c77-751">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="a4c77-752">Řádek vztahu nula nebo jeden n (0..1 až \*) mezi entitami `Instructor` a. `Department`</span><span class="sxs-lookup"><span data-stu-id="a4c77-752">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="a4c77-753">Osivo DB s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="a4c77-753">Seed the DB with Test Data</span></span>

<span data-ttu-id="a4c77-754">Aktualizace kódu v *souboru Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="a4c77-754">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="a4c77-755">Předchozí kód poskytuje data osiva pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="a4c77-755">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="a4c77-756">Většina tohoto kódu vytvoří nové objekty entity a načte ukázková data.</span><span class="sxs-lookup"><span data-stu-id="a4c77-756">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="a4c77-757">Ukázková data se používají k testování.</span><span class="sxs-lookup"><span data-stu-id="a4c77-757">The sample data is used for testing.</span></span> <span data-ttu-id="a4c77-758">Viz `Enrollments` `CourseAssignments` a příklady, kolik-k-n spojit tabulky mohou být nasazeny.</span><span class="sxs-lookup"><span data-stu-id="a4c77-758">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="a4c77-759">Přidání migrace</span><span class="sxs-lookup"><span data-stu-id="a4c77-759">Add a migration</span></span>

<span data-ttu-id="a4c77-760">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="a4c77-760">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4c77-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4c77-761">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4c77-762">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4c77-762">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="a4c77-763">Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.</span><span class="sxs-lookup"><span data-stu-id="a4c77-763">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="a4c77-764">Pokud `database update` je příkaz spuštěn, vytvoří se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="a4c77-764">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="a4c77-765">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="a4c77-765">Apply the migration</span></span>

<span data-ttu-id="a4c77-766">Nyní, když máte existující databázi, je třeba přemýšlet o tom, jak použít budoucí změny na ni.</span><span class="sxs-lookup"><span data-stu-id="a4c77-766">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="a4c77-767">Tento kurz ukazuje dva přístupy:</span><span class="sxs-lookup"><span data-stu-id="a4c77-767">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="a4c77-768">Přetažení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="a4c77-768">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="a4c77-769">[Použijte migraci na existující databázi](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="a4c77-769">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="a4c77-770">I když je tato metoda složitější a časově náročnější, je to upřednostňovaný přístup pro reálná produkční prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4c77-770">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="a4c77-771">**Poznámka:** Toto je volitelná část kurzu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-771">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="a4c77-772">Můžete provést přetažení a znovu vytvořit kroky a přeskočit tuto část.</span><span class="sxs-lookup"><span data-stu-id="a4c77-772">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="a4c77-773">Pokud chcete postupovat podle kroků v této části, neprovázte a znovu nevytvářejte kroky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-773">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="a4c77-774">Přetažení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="a4c77-774">Drop and re-create the database</span></span>

<span data-ttu-id="a4c77-775">Kód v aktualizované `DbInitializer` přidá data osiva pro nové entity.</span><span class="sxs-lookup"><span data-stu-id="a4c77-775">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="a4c77-776">Chcete-li vynutit EF Core k vytvoření nové ho DB, přetáhněte a aktualizujte DB:</span><span class="sxs-lookup"><span data-stu-id="a4c77-776">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4c77-777">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4c77-777">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a4c77-778">V **konzole Správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a4c77-778">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="a4c77-779">Spusťte `Get-Help about_EntityFrameworkCore` z PMC a získejte informace nápovědy.</span><span class="sxs-lookup"><span data-stu-id="a4c77-779">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4c77-780">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4c77-780">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a4c77-781">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-781">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="a4c77-782">Složka projektu obsahuje *soubor Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="a4c77-782">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="a4c77-783">Do příkazového okna zadejte následující:</span><span class="sxs-lookup"><span data-stu-id="a4c77-783">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="a4c77-784">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a4c77-784">Run the app.</span></span> <span data-ttu-id="a4c77-785">Spuštění aplikace spustí `DbInitializer.Initialize` metodu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-785">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="a4c77-786">Naplní `DbInitializer.Initialize` nové DB.</span><span class="sxs-lookup"><span data-stu-id="a4c77-786">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="a4c77-787">Otevřete DB ve SSOX:</span><span class="sxs-lookup"><span data-stu-id="a4c77-787">Open the DB in SSOX:</span></span>

* <span data-ttu-id="a4c77-788">Pokud byl ssox otevřen dříve, klepněte na tlačítko **Aktualizovat.**</span><span class="sxs-lookup"><span data-stu-id="a4c77-788">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="a4c77-789">Rozbalte uzel **Tabulky.**</span><span class="sxs-lookup"><span data-stu-id="a4c77-789">Expand the **Tables** node.</span></span> <span data-ttu-id="a4c77-790">Zobrazí se vytvořené tabulky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-790">The created tables are displayed.</span></span>

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="a4c77-792">Prohlédněte si tabulku **CourseAssignment:**</span><span class="sxs-lookup"><span data-stu-id="a4c77-792">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="a4c77-793">Klepněte pravým tlačítkem myši na tabulku **CourseAssignment** a vyberte **zobrazit data**.</span><span class="sxs-lookup"><span data-stu-id="a4c77-793">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="a4c77-794">Ověřte, zda tabulka **CourseAssignment** obsahuje data.</span><span class="sxs-lookup"><span data-stu-id="a4c77-794">Verify the **CourseAssignment** table contains data.</span></span>

![CourseAssignment data v SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="a4c77-796">Použití migrace na existující databázi</span><span class="sxs-lookup"><span data-stu-id="a4c77-796">Apply the migration to the existing database</span></span>

<span data-ttu-id="a4c77-797">Tato část je nepovinná.</span><span class="sxs-lookup"><span data-stu-id="a4c77-797">This section is optional.</span></span> <span data-ttu-id="a4c77-798">Tyto kroky fungují pouze v případě, že jste přeskočili předchozí [drop a znovu vytvořit databázový](#drop) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4c77-798">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="a4c77-799">Při migraci jsou spuštěny s existující mise, může být FK omezení, které nejsou spokojeni s existující data.</span><span class="sxs-lookup"><span data-stu-id="a4c77-799">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="a4c77-800">S produkčními daty je nutné podniknout kroky k migraci existujících dat.</span><span class="sxs-lookup"><span data-stu-id="a4c77-800">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="a4c77-801">Tato část obsahuje příklad opravy porušení omezení FK.</span><span class="sxs-lookup"><span data-stu-id="a4c77-801">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="a4c77-802">Neprováděte tyto změny kódu bez zálohy.</span><span class="sxs-lookup"><span data-stu-id="a4c77-802">Don't make these code changes without a backup.</span></span> <span data-ttu-id="a4c77-803">Neprovádějte tyto změny kódu, pokud jste dokončili předchozí část a aktualizovali databázi.</span><span class="sxs-lookup"><span data-stu-id="a4c77-803">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="a4c77-804">Soubor *{timestamp}_ComplexDataModel.cs* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="a4c77-804">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="a4c77-805">Předchozí kód přidá do `DepartmentID` `Course` tabulky soubor FK, který lze udát.</span><span class="sxs-lookup"><span data-stu-id="a4c77-805">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="a4c77-806">DB z předchozího kurzu `Course`obsahuje řádky v aplikaci , takže tabulka nemůže být aktualizována migrací.</span><span class="sxs-lookup"><span data-stu-id="a4c77-806">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="a4c77-807">Chcete-li, aby migrace fungovala `ComplexDataModel` s existujícími daty:</span><span class="sxs-lookup"><span data-stu-id="a4c77-807">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="a4c77-808">Změňte kód tak, aby`DepartmentID`nový sloupec ( ) měl výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a4c77-808">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="a4c77-809">Vytvořte falešné oddělení s názvem "Temp", které bude fungovat jako výchozí oddělení.</span><span class="sxs-lookup"><span data-stu-id="a4c77-809">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="a4c77-810">Oprava omezení cizího klíče</span><span class="sxs-lookup"><span data-stu-id="a4c77-810">Fix the foreign key constraints</span></span>

<span data-ttu-id="a4c77-811">Aktualizujte `ComplexDataModel` `Up` metodu tříd:</span><span class="sxs-lookup"><span data-stu-id="a4c77-811">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="a4c77-812">Otevřete soubor *{timestamp}_ComplexDataModel.cs.*</span><span class="sxs-lookup"><span data-stu-id="a4c77-812">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="a4c77-813">Zakomentujte řádek kódu, `DepartmentID` který `Course` přidá sloupec do tabulky.</span><span class="sxs-lookup"><span data-stu-id="a4c77-813">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="a4c77-814">Přidejte následující zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="a4c77-814">Add the following highlighted code.</span></span> <span data-ttu-id="a4c77-815">Nový kód jde `.CreateTable( name: "Department"` za blokem:</span><span class="sxs-lookup"><span data-stu-id="a4c77-815">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="a4c77-816">S předchozími změnami `Course` budou existující řádky po spuštění metody `ComplexDataModel` `Up` souviset s oddělením "Temp".</span><span class="sxs-lookup"><span data-stu-id="a4c77-816">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="a4c77-817">Produkční aplikace by:</span><span class="sxs-lookup"><span data-stu-id="a4c77-817">A production app would:</span></span>

* <span data-ttu-id="a4c77-818">Zahrňte kód `Department` nebo skripty pro přidání řádků a souvisejících `Course` řádků do nových `Department` řádků.</span><span class="sxs-lookup"><span data-stu-id="a4c77-818">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="a4c77-819">Nepoužívejte oddělení "Temp" nebo výchozí `Course.DepartmentID`hodnotu pro .</span><span class="sxs-lookup"><span data-stu-id="a4c77-819">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="a4c77-820">Další kurz se zabývá souvisejícími daty.</span><span class="sxs-lookup"><span data-stu-id="a4c77-820">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4c77-821">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a4c77-821">Additional resources</span></span>

* [<span data-ttu-id="a4c77-822">YouTube verze tohoto výukového programu (část 1)</span><span class="sxs-lookup"><span data-stu-id="a4c77-822">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="a4c77-823">YouTube verze tohoto výukového programu (část 2)</span><span class="sxs-lookup"><span data-stu-id="a4c77-823">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="a4c77-824">[Předchozí](xref:data/ef-rp/migrations)
> [další](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="a4c77-824">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
