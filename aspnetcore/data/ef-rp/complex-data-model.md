---
title: Razor Pages s EF Core v ASP.NET Core-datový model 5 z 8
author: rick-anderson
description: V tomto kurzu přidejte další entity a vztahy a upravte datový model zadáním formátování, ověřování a pravidel mapování.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1244b2e23a842538ff2fca01a513317a690afe7c
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034035"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a>Razor Pages s EF Core v ASP.NET Core-datový model 5 z 8

Podle [Petr Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Předchozí kurzy pracovaly se základním datovým modelem, který se skládá ze tří entit. V tomto kurzu:

* Přidávají se další entity a vztahy.
* Datový model je přizpůsoben zadáním pravidel formátování, ověřování a mapování databáze.

Dokončený datový model je zobrazený na následujícím obrázku:

![Diagram entit](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a>Entita studenta

![Entita studenta](complex-data-model/_static/student-entity.png)

Nahraďte kód v *modelů/student. cs* následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

Předchozí kód přidá vlastnost `FullName` a přidá následující atributy do existujících vlastností:

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a>Vypočítaná vlastnost FullName

`FullName` je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností. `FullName` nelze nastavit, takže má pouze přistupující objekt get. V databázi není vytvořen žádný `FullName` sloupec.

### <a name="the-datatype-attribute"></a>Atribut DataType

```csharp
[DataType(DataType.Date)]
```

Pro data o registraci studenta aktuálně zobrazují všechny stránky denní dobu a datum, i když se jedná o relevantní datum. Pomocí atributů datových poznámek můžete vytvořit jednu změnu kódu, která bude opravovat formát zobrazení na každé stránce, která zobrazuje data. 

Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) Určuje datový typ, který je konkrétnější než vnitřní typ databáze. V tomto případě by se měla zobrazit pouze datum, nikoli datum a čas. [Výčet DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress atd. Atribut `DataType` může také aplikaci povolit automatické poskytování funkcí specifických pro typ. Příklad:

* Odkaz `mailto:` se automaticky vytvoří pro `DataType.EmailAddress`.
* Selektor data je k dispozici pro `DataType.Date` ve většině prohlížečů.

Atribut `DataType` emituje atributy HTML 5 `data-` (vyslovení data pomlčky). Atributy `DataType` neposkytují ověřování.

### <a name="the-displayformat-attribute"></a>Atribut DisplayFormat

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date` neurčuje formát data, které se zobrazí. Ve výchozím nastavení se pole Datum zobrazuje v závislosti na výchozích formátech na základě objektu [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serveru.

Atribut `DisplayFormat` slouží k explicitnímu zadání formátu data. Nastavení `ApplyFormatInEditMode` určuje, zda má být formátování použito také pro uživatelské rozhraní úprav. Některá pole by neměla používat `ApplyFormatInEditMode`. Například symbol měny by neměl být v textovém poli pro úpravy obvykle zobrazen.

Atribut `DisplayFormat` může používat sám sebe. Obecně je vhodné použít atribut `DataType` s atributem `DisplayFormat`. Atribut `DataType` předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce. Atribut `DataType` poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`:

* Prohlížeč může povolit funkce HTML5. Například můžete zobrazit ovládací prvek kalendáře, symbol měny odpovídající národním prostředí, e-mailové odkazy a ověření vstupu na straně klienta.
* Ve výchozím nastavení prohlížeč vykresluje data pomocí správného formátu založeného na národním prostředí.

Další informace najdete v dokumentaci k [pomocníka značek\<input >](xref:mvc/views/working-with-forms#the-input-tag-helper).

### <a name="the-stringlength-attribute"></a>Atribut StringLength

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

Pravidla ověřování dat a chybové zprávy ověřování lze zadat pomocí atributů. Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli. Zobrazený kód omezuje názvy na více než 50 znaků. Příklad, který nastaví minimální délku řetězce, se zobrazí [později](#the-required-attribute).

Atribut `StringLength` také poskytuje ověřování na straně klienta a serveru. Minimální hodnota nemá žádný vliv na schéma databáze.

Atribut `StringLength` nebrání uživateli v zadání prázdného místa pro název. Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) lze použít k uplatnění omezení na vstup. Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V **Průzkumník objektů systému SQL Server** (SSOX) otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .

![Tabulka studentů v SSOX před migracemi](complex-data-model/_static/ssox-before-migration.png)

Na předchozím obrázku je znázorněno schéma `Student` tabulky. Pole název mají typ `nvarchar(MAX)`. Když se vytvoří migrace a použije se později v tomto kurzu, pole název se stanou `nvarchar(50)` v důsledku atributů délky řetězce.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V nástroji SQLite si Projděte definice sloupců `Student` tabulce. Pole název mají typ `Text`. Všimněte si, že pole jméno se nazývá `FirstMidName`. V další části změníte název tohoto sloupce na `FirstName`.

---

### <a name="the-column-attribute"></a>Atribut Column

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

Atributy mohou řídit způsob, jakým jsou třídy a vlastnosti mapovány na databázi. V modelu `Student` se k namapování názvu vlastnosti `FirstMidName` na FirstName v databázi používá atribut `Column`.

Při vytvoření databáze se názvy vlastností v modelu používají pro názvy sloupců (kromě případu, kdy se používá atribut `Column`). `Student` model používá `FirstMidName` pro pole jméno a příjmení, protože pole může obsahovat také prostřední jméno.

Pomocí atributu `[Column]` `Student.FirstMidName` v datovém modelu mapuje na sloupec `FirstName` tabulky `Student`. Přidání atributu `Column` změní model zálohování `SchoolContext`. Model zálohovaný `SchoolContext` se už neshoduje s databází. Tato nesrovnalost bude vyřešena přidáním migrace později v tomto kurzu.

### <a name="the-required-attribute"></a>Požadovaný atribut

```csharp
[Required]
```

Atribut `Required` vytváří pole vlastnosti názvu Required. Atribut `Required` není potřebný pro typy, které nejsou null, jako jsou například typy hodnot (například `DateTime`, `int`a `double`). Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.

Atribut `Required` musí být použit s `MinimumLength` pro vymáhání `MinimumLength`.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength` a `Required` umožňují, aby bylo prázdné znaky vyhovět ověřování. Použijte atribut `RegularExpression` pro úplnou kontrolu nad řetězcem.

### <a name="the-display-attribute"></a>Atribut zobrazení

```csharp
[Display(Name = "Last Name")]
```

Atribut `Display` určuje, že titulek pro textová pole by měl být "jméno", "příjmení", "celé jméno" a "datum zápisu". Výchozí titulky neobsahovaly místo dělení slov, například "LastName".

### <a name="create-a-migration"></a>Vytvoření migrace

Spusťte aplikaci a pokračujte na stránku students. Je vyvolána výjimka. Atribut `[Column]` způsobuje, že EF očekává, že najde sloupec s názvem `FirstName`, ale název sloupce v databázi je stále `FirstMidName`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Chybová zpráva je podobná následujícímu příkladu:

```
SqlException: Invalid column name 'FirstName'.
```

* Do PMC zadejte následující příkazy, abyste vytvořili novou migraci a aktualizovali databázi:

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  První z těchto příkazů generuje následující varovnou zprávu:

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  Upozornění je vygenerováno, protože pole s názvem jsou nyní omezena na 50 znaků. Pokud má název v databázi více než 50 znaků, bude ztraceno 51 k poslednímu znaku.

* Otevřete tabulku student v SSOX:

  ![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

  Před použitím migrace byly sloupce názvu typu [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Sloupce s názvem jsou nyní `nvarchar(50)`. Název sloupce se změnil z `FirstMidName` na `FirstName`.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Chybová zpráva je podobná následujícímu příkladu:

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* Otevřete okno příkazového řádku ve složce projektu. Zadáním následujících příkazů vytvořte novou migraci a aktualizujte databázi:

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  Příkaz aktualizace databáze zobrazí chybu jako v následujícím příkladu:

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

Pro tento kurz se způsobem, jak se tato chyba zobrazí po této chybě, odstranit a znovu vytvořit počáteční migraci. Další informace najdete v poznámkovém upozornění SQLite v horní části [kurzu migrace](xref:data/ef-rp/migrations).

* Odstraňte složku *migrace* .
* Spusťte následující příkazy k vyřazení databáze, vytvořte novou počáteční migraci a použijte migraci:

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* Projděte si tabulku student v nástroji SQLite. Sloupec, který byl FirstMidName, je nyní FirstName.

---

* Spusťte aplikaci a pokračujte na stránku students.
* Všimněte si, že časy nejsou vstupní ani se nezobrazují spolu s kalendářními daty.
* Vyberte **vytvořit novou**a zkuste zadat název delší než 50 znaků.

> [!Note]
> V následujících oddílech sestavení aplikace v některých fázích generuje chyby kompilátoru. Pokyny určují, kdy se má aplikace sestavit.

## <a name="the-instructor-entity"></a>Entita instruktor

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

Vytvořte *modely/Instructor. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

Více atributů může být na jednom řádku. Atributy `HireDate` mohou být zapsány následujícím způsobem:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>Navigační vlastnosti

Vlastnosti `CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.

Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Instruktor může mít maximálně jednu kancelář, takže vlastnost `OfficeAssignment` obsahuje jedinou entitu `OfficeAssignment`. `OfficeAssignment` má hodnotu null, pokud není přiřazen žádný systém Office.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>Entita OfficeAssignment

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Klíčový atribut

Atribut `[Key]` slouží k identifikaci vlastnosti jako primárního klíče (PK), pokud je název vlastnosti něco jiného než classnameID nebo ID.

Mezi entitami `Instructor` a `OfficeAssignment` existuje vztah 1:1 nebo jedna. Přiřazení kanceláře existuje jenom ve vztahu k instruktorovi, ke kterému je přiřazený. `OfficeAssignment` PK je také jeho cizí klíč (FK) k entitě `Instructor`.

EF Core nemůže automaticky rozpoznat `InstructorID` jako PK v rámci `OfficeAssignment`, protože `InstructorID` nedodržuje zásady vytváření názvů (ID) nebo classnameID. Proto atribut `Key` slouží k identifikaci `InstructorID` jako PK:

```csharp
[Key]
public int InstructorID { get; set; }
```

Ve výchozím nastavení EF Core považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.

### <a name="the-instructor-navigation-property"></a>Navigační vlastnost instruktora

Vlastnost navigace `Instructor.OfficeAssignment` může mít hodnotu null, protože pro daného instruktora pravděpodobně není `OfficeAssignment` řádek. Instruktor nemusí mít přiřazení kanceláře.

Navigační vlastnost `OfficeAssignment.Instructor` bude mít vždycky entitu Instructor, protože typ `InstructorID` cizího klíče je `int`, typ hodnoty, která není null. Přiřazení kanceláře nemůže existovat bez instruktora.

Pokud má entita `Instructor` související entitu `OfficeAssignment`, Každá entita má odkaz na jinou entitu v její navigační vlastnosti.

## <a name="the-course-entity"></a>Entita kurzu

![Entita kurzu](complex-data-model/_static/course-entity.png)

Aktualizujte *modely/Course. cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

Entita `Course` má `DepartmentID`vlastnost cizího klíče (FK). `DepartmentID` odkazuje na související entitu `Department`. `Course` entita má vlastnost navigace `Department`.

EF Core nevyžaduje vlastnost cizího klíče pro datový model, pokud model má vlastnost navigace pro související entitu. EF Core v databázi automaticky vytvoří FKs bez ohledu na to, kde jsou potřeba. EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automatické vytváření FKs. Explicitní a efektivnější je však, že explicitně včetně FK v datovém modelu může zjednodušit a efektivněji dělat aktualizace. *Představte* si například model, ve kterém není obsažena vlastnost FK `DepartmentID`. Když se načte entita kurzu, která se upraví:

* Vlastnost `Department` má hodnotu null, pokud není explicitně načtena.
* Chcete-li aktualizovat entitu kurzu, musí být nejprve načtena `Department` entita.

Pokud je vlastnost FK `DepartmentID` obsažena v datovém modelu, není nutné načíst entitu `Department` před aktualizací.

### <a name="the-databasegenerated-attribute"></a>Atribut DatabaseGenerated

Atribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` určuje, zda je v rámci aplikace poskytnuta PK místo vygenerovaného databází.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Ve výchozím nastavení EF Core předpokládá, že se hodnoty PK generují v databázi. Vygenerovaná databáze obvykle představuje nejlepší přístup. Pro `Course` entit určuje uživatel PK. Například číslo kurzu, jako je například série 1000 pro matematické oddělení, série 2000 pro národní oddělení.

Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot. Databáze může například automaticky vygenerovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován. Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizích klíčů a navigace

Vlastnosti cizího klíče (FK) a navigační vlastnosti v entitě `Course` odrážejí následující vztahy:

Kurz se přiřadí jednomu oddělení, takže existuje `DepartmentID` FK a vlastnost `Department` navigace.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Kurz může mít zaregistrovaný libovolný počet studentů, takže navigační vlastnost `Enrollments` je kolekce:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurz může být výukou více instruktorů, takže navigační vlastnost `CourseAssignments` je kolekce:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` je vysvětleno [později](#many-to-many-relationships).

## <a name="the-department-entity"></a>Entita oddělení

![Entita oddělení](complex-data-model/_static/department-entity.png)

Vytvořte *modely/oddělení. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a>Atribut Column

Dřív se použil atribut `Column` pro změnu mapování názvu sloupce. V kódu pro entitu `Department` se atribut `Column` používá ke změně mapování datových typů SQL. `Budget` sloupec je definován pomocí SQL Server peněžního typu v databázi:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapování sloupce není obecně vyžadováno. EF Core zvolí příslušný datový typ SQL Server na základě typu CLR pro danou vlastnost. Typ CLR `decimal` namapuje na typ `decimal` SQL Server. `Budget` je pro měnu a datový typ Money je pro měnu vhodný.

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizích klíčů a navigace

Vlastnosti FK a navigace odrážejí následující vztahy:

* Oddělení může nebo nemusí mít správce.
* Správce je vždy instruktor. Proto je vlastnost `InstructorID` zahrnutá jako FK do `Instructor` entity.

Navigační vlastnost má název `Administrator`, ale obsahuje `Instructor` entitu:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Otazník (?) v předchozím kódu určuje vlastnost s možnou hodnotou null.

Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:

```csharp
public ICollection<Course> Courses { get; set; }
```

Podle konvence EF Core povoluje kaskádové odstranění pro FKs, která nejsou null a pro relace m:n. Toto výchozí chování může způsobit cyklické kaskády odstraňování pravidel. Cyklická kaskádová odstranění pravidel způsobují při přidání migrace výjimku.

Pokud je například vlastnost `Department.InstructorID` definovaná jako hodnota, která není null, EF Core by nakonfigurovala pravidlo kaskádového odstranění. V takovém případě by se oddělení odstranilo, když se odstraní instruktor jako jeho správce. V tomto scénáři by pravidlo omezení mělo smysl. Následující [rozhraní Fluent API](#fluent-api-alternative-to-attributes) by nastavilo pravidlo omezení a zakáže kaskádové odstranění.

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a>Registrace entity

Záznam zápisu je pro jeden kurz, který přijímá jeden student.

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

Aktualizujte *modely/zápis. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizích klíčů a navigace

Vlastnosti CK a vlastnosti navigace odrážejí následující vztahy:

Záznam zápisu je pro jeden kurz, takže existuje `CourseID` vlastnost FK a `Course` navigační vlastnost:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Záznam zápisu je určen pro jednoho studenta, takže existuje `StudentID` vlastnost FK a `Student` navigační vlastnost:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relace m:n

Mezi entitami `Student` a `Course` existuje vztah m:n. Entita `Enrollment` funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi. "S datovou částí" znamená, že `Enrollment` tabulka obsahuje další data kromě FKs pro Spojené tabulky (v tomto případě je to PK a `Grade`).

Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit. (Tento diagram byl vygenerován pomocí [nástrojů EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6. x. Vytvoření diagramu není součástí kurzu.)

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

Každá čára relace má 1 na jednom konci a hvězdičku (*) na druhé straně, která indikuje relaci 1: n.

Pokud tabulka `Enrollment` neobsahovala informace o třídě, musí obsahovat pouze dvě FKs (`CourseID` a `StudentID`). Tabulka JOIN typu m:n bez datové části se někdy označuje jako čistá spojovací tabulka (PJT).

Entity `Instructor` a `Course` mají relaci n:n pomocí tabulky Pure JOIN.

Poznámka: EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne. Další informace najdete v tématu [relace m:n v EF Core 2,0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>Entita CourseAssignment

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

Relace m:n pro každého-více kurzů vyžaduje tabulku JOIN a entita pro tuto tabulku JOIN je CourseAssignment.

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

Je běžné pojmenovat entitu JOIN `EntityName1EntityName2`. Například tabulka pro spojování instruktorů do kurzů, pomocí které tento model bude `CourseInstructor`. Doporučujeme však použít název, který popisuje vztah.

Modely dat začínají jednoduchým a roste. Spojování tabulek bez datové části (PJTs) se často vyvíjí, aby zahrnovalo datovou část. Když začnete s popisným názvem entity, nemusíte při změně tabulky JOIN měnit název. V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně. Například knihy a zákazníci mohou být propojeny s entitou JOIN nazvanou hodnocení. Pro relaci n:n od instruktora na více kurzů `CourseAssignment` přednost před `CourseInstructor`.

### <a name="composite-key"></a>Složený klíč

Dva FKs v `CourseAssignment` (`InstructorID` a `CourseID`) společně jednoznačně identifikují každý řádek tabulky `CourseAssignment`. `CourseAssignment` nevyžaduje vyhrazený PK. Vlastnosti `InstructorID` a `CourseID` fungují jako složený PK. Jediným způsobem, jak zadat složené PKs EF Core je s *rozhraním API Fluent*. V další části se dozvíte, jak nakonfigurovat složený PK.

Složený klíč zajišťuje:

* Pro jeden kurz je povoleno více řádků.
* Pro jednoho instruktora je povoleno více řádků.
* Pro stejný instruktor a kurz nejsou povoleny vícenásobné řádky.

Entita `Enrollment` JOIN definuje vlastní PK, takže je možné duplikovat toto řazení. Chcete-li zabránit těmto duplicitám:

* Přidejte do polí FK jedinečný index nebo
* Nakonfigurujte `Enrollment` s primárním kombinovaným klíčem podobným `CourseAssignment`. Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Aktualizace kontextu databáze

Aktualizujte *data/SchoolContext. cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

Předchozí kód přidá nové entity a nakonfiguruje neseparovaný PK entity `CourseAssignment`.

## <a name="fluent-api-alternative-to-attributes"></a>Alternativa k atributům rozhraní Fluent API

Metoda `OnModelCreating` v předchozím kódu používá ke konfiguraci chování EF Core *rozhraní Fluent API* . Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu. [Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem rozhraní Fluent API:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

V tomto kurzu se rozhraní API Fluent používá jenom pro mapování databáze, které nejde s atributy dělat. Rozhraní API Fluent ale může určovat většinu pravidel formátování, ověřování a mapování, která se dají provádět s atributy.

Některé atributy, jako je `MinimumLength`, se nedají použít s rozhraním API Fluent. `MinimumLength` nemění schéma, používá pouze ověřovací pravidlo minimální délky.

Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit". Atributy a rozhraní API Fluent lze kombinovat. Existují některé konfigurace, které lze provést pouze s rozhraním API Fluent (určením složeného PK). Existují některé konfigurace, které lze provádět pouze s atributy (`MinimumLength`). Doporučený postup pro použití rozhraní Fluent API nebo atributů:

* Vyberte jednu z těchto dvou přístupů.
* Používejte vybraný postup konzistentně co nejvíce.

Některé atributy používané v tomto kurzu se používají pro:

* Pouze ověření (například `MinimumLength`).
* Pouze konfigurace EF Core (například `HasKey`).
* Konfigurace ověřování a EF Core (například `[StringLength(50)]`).

Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).

## <a name="entity-diagram"></a>Diagram entit

Následující ilustrace znázorňuje diagram, který nástroje EF Power Tools vytvoří pro dokončený školní model.

![Diagram entit](complex-data-model/_static/diagram.png)

Předchozí diagram znázorňuje:

* Několik čar relací 1:1 (1 až \*).
* Čára relace 1:1 (1 – 0.. 1) mezi entitami `Instructor` a `OfficeAssignment`.
* Čára vztahu nula až n-many (0.. 1 až ×) mezi entitami `Instructor` a `Department`.

## <a name="seed-the-database"></a>Přidání dat do databáze

Aktualizujte kód v *data/DbInitializer. cs*:

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

Předchozí kód poskytuje počáteční data pro nové entity. Většina tohoto kódu vytváří nové objekty entit a načítá vzorová data. Ukázková data se používají k testování. Příklady toho, kolik tabulek Join lze dosazením, najdete v tématu `Enrollments` a `CourseAssignments`.

## <a name="add-a-migration"></a>Přidání migrace

Sestavte projekt.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V PMC spusťte následující příkaz.

```powershell
Add-Migration ComplexDataModel
```

Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

Pokud se spustí příkaz `database update`, vytvoří se následující chyba:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

V další části se dozvíte, co dělat s touto chybou.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pokud přidáte migraci a spustíte příkaz `database update`, vytvoří se následující chyba:

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

V další části se zobrazí informace o tom, jak se vyhnout této chybě.

---

## <a name="apply-the-migration-or-drop-and-re-create"></a>Použití migrace nebo vyřazení a opětovné vytvoření

Teď, když máte existující databázi, musíte si představit, jak se na ni aplikují změny. V tomto kurzu se zobrazí dvě alternativy:

* [Vyřaďte a znovu vytvořte databázi](#drop). Tuto část vyberte, pokud používáte SQLite.
* [Použijte migraci na stávající databázi](#applyexisting). Pokyny v této části fungují pouze pro SQL Server, **nikoli pro SQLite**. 

Jedna volba funguje pro SQL Server. I když je metoda Apply složitější a časově náročná, jedná se o preferovaný přístup pro produkční prostředí z reálného světa. 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>Vyřazení a opětovné vytvoření databáze

[Tuto část přeskočte](#apply-the-migration) , pokud používáte SQL Server a chcete provést postup použití migrace v následující části.

Chcete-li vynutit EF Core vytvoření nové databáze, vyřaďte a aktualizujte databázi:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **konzole správce balíčků** (PMC) spusťte následující příkaz:

  ```powershell
  Drop-Database
  ```

* Odstraňte složku *migrace* a pak spusťte následující příkaz:

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete příkazové okno a přejděte do složky projektu. Složka projektu obsahuje soubor *ContosoUniversity. csproj* .

* Spusťte následující příkaz:

  ```dotnetcli
  dotnet ef database drop --force
  ```

* Odstraňte složku *migrace* a pak spusťte následující příkaz:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Spusťte aplikaci. Spuštění aplikace spustí metodu `DbInitializer.Initialize`. `DbInitializer.Initialize` naplní novou databázi.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Otevřete databázi v SSOX:

* Pokud jste dříve otevřeli SSOX, klikněte na tlačítko **aktualizovat** .
* Rozbalte **tabulky** uzlu. Zobrazí se vytvořené tabulky.

  ![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

* Projděte si tabulku **CourseAssignment** :

  * Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data**.
  * Ověřte, že tabulka **CourseAssignment** obsahuje data.

  ![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Použijte nástroj SQLite k prohlédnutí databáze:

* Nové tabulky a sloupce.
* Osazená data v tabulkách, například tabulka **CourseAssignment** .

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a>Použití migrace

Tato část je volitelná. Tyto kroky fungují jenom pro SQL Server LocalDB a jenom v případě, že jste přeskočili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .

Pokud jsou migrace spouštěny s existujícími daty, může dojít k omezením FK, která nesplňují stávající data. S provozními daty je potřeba provést kroky pro migraci stávajících dat. V této části najdete příklad opravy porušení omezení CK. Neprovádějte změny kódu bez zálohy. Neprovádějte změny kódu, pokud jste dokončili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .

Soubor *{timestamp} _ComplexDataModel. cs* obsahuje následující kód:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

Předchozí kód přidá do tabulky `Course` `DepartmentID` FK, který nemůže mít hodnotu null. Databáze z předchozího kurzu obsahuje řádky v `Course`, takže se tabulka nedá aktualizovat pomocí migrací.

Postup při práci `ComplexDataModel` migrace s existujícími daty:

* Změňte kód tak, aby měl nový sloupec (`DepartmentID`) výchozí hodnotu.
* Vytvořte falešné oddělení s názvem "Temp", které bude sloužit jako výchozí oddělení.

#### <a name="fix-the-foreign-key-constraints"></a>Oprava omezení cizího klíče

V `ComplexDataModel` třídy migrace aktualizujte `Up` metodu:

* Otevřete soubor *{timestamp} _ComplexDataModel. cs* .
* Odkomentujte řádek kódu, který přidá sloupec `DepartmentID` do tabulky `Course`.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Přidejte následující zvýrazněný kód. Nový kód přejde za `.CreateTable( name: "Department"` blok:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

V předchozích změnách budou existující `Course` řádky při spuštění metody `ComplexDataModel.Up` v souvislosti s oddělením "dočasné".

Pro účely tohoto kurzu je zjednodušený způsob zpracování situace, kterou tady vidíte. Produkční aplikace by:

* Zahrnout kód nebo skripty pro přidání `Department` řádků a souvisejících `Course` řádků do nových `Department` řádků.
* Nepoužívejte oddělení "dočasné" nebo výchozí hodnotu pro `Course.DepartmentID`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **konzole správce balíčků** (PMC) spusťte následující příkaz:

  ```powershell
  Update-Database
  ```

Vzhledem k tomu, že metoda `DbInitializer.Initialize` je navržena tak, aby fungovala pouze s prázdnou databází, použijte SSOX k odstranění všech řádků v tabulkách student a Course. (Kaskádová odstranění se postará o tabulku zápisu.)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Pokud používáte SQL Server LocalDB s Visual Studio Code, spusťte následující příkaz:

  ```dotnetcli
  dotnet ef database update
  ```

---

Spusťte aplikaci. Spuštění aplikace spustí metodu `DbInitializer.Initialize`. `DbInitializer.Initialize` naplní novou databázi.

## <a name="next-steps"></a>Další kroky

Následující dva kurzy ukazují, jak číst a aktualizovat související data.

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/migrations)
> [Další kurz](xref:data/ef-rp/read-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Předchozí kurzy pracovaly se základním datovým modelem, který se skládá ze tří entit. V tomto kurzu:

* Přidávají se další entity a vztahy.
* Datový model je přizpůsoben zadáním pravidel formátování, ověřování a mapování databáze.

Třídy entit pro dokončený datový model jsou znázorněny na následujícím obrázku:

![Diagram entit](complex-data-model/_static/diagram.png)

Pokud narazíte na problémy, které nemůžete vyřešit, stáhněte [dokončenou aplikaci](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="customize-the-data-model-with-attributes"></a>Přizpůsobení datového modelu pomocí atributů

V této části je datový model přizpůsoben pomocí atributů.

### <a name="the-datatype-attribute"></a>Atribut DataType

Na stránkách studenta se aktuálně zobrazuje čas pro datum registrace. Pole data obvykle zobrazují pouze datum a čas.

Aktualizujte *modely/student. cs* následujícím zvýrazněným kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) Určuje datový typ, který je konkrétnější než vnitřní typ databáze. V tomto případě by se měla zobrazit pouze datum, nikoli datum a čas. [Výčet DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress atd. Atribut `DataType` může také aplikaci povolit automatické poskytování funkcí specifických pro typ. Příklad:

* Odkaz `mailto:` se automaticky vytvoří pro `DataType.EmailAddress`.
* Selektor data je k dispozici pro `DataType.Date` ve většině prohlížečů.

Atribut `DataType` emituje atributy HTML 5 `data-` (vyslovované datové přerušované), které používají prohlížeče formátu HTML 5. Atributy `DataType` neposkytují ověřování.

`DataType.Date` neurčuje formát data, které se zobrazí. Ve výchozím nastavení se pole Datum zobrazuje v závislosti na výchozích formátech na základě objektu [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serveru.

Atribut `DisplayFormat` slouží k explicitnímu zadání formátu data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Nastavení `ApplyFormatInEditMode` určuje, zda má být formátování použito také pro uživatelské rozhraní úprav. Některá pole by neměla používat `ApplyFormatInEditMode`. Například symbol měny by neměl být v textovém poli pro úpravy obvykle zobrazen.

Atribut `DisplayFormat` může používat sám sebe. Obecně je vhodné použít atribut `DataType` s atributem `DisplayFormat`. Atribut `DataType` předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce. Atribut `DataType` poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`:

* Prohlížeč může povolit funkce HTML5. Například můžete zobrazit ovládací prvek kalendáře, symbol měny odpovídající národním prostředí, e-mailové odkazy, ověřování vstupu na straně klienta atd.
* Ve výchozím nastavení prohlížeč vykresluje data pomocí správného formátu založeného na národním prostředí.

Další informace najdete v dokumentaci k [pomocníka značek\<input >](xref:mvc/views/working-with-forms#the-input-tag-helper).

Spusťte aplikaci. Přejděte na stránku indexu studentů. Časy se už nezobrazují. Každé zobrazení, které používá model `Student`, zobrazí datum bez času.

![Stránka indexu studentů zobrazující data bez časů](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Atribut StringLength

Pravidla ověřování dat a chybové zprávy ověřování lze zadat pomocí atributů. Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli. Atribut `StringLength` také poskytuje ověřování na straně klienta a serveru. Minimální hodnota nemá žádný vliv na schéma databáze.

Aktualizujte `Student` model pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Předchozí kód omezuje názvy na více než 50 znaků. Atribut `StringLength` nebrání uživateli v zadání prázdného místa pro název. Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) se používá k aplikování omezení na vstup. Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Spusťte aplikaci:

* Přejděte na stránku students.
* Vyberte **vytvořit novou**a zadejte název delší než 50 znaků.
* Vyberte **vytvořit**, ověřování na straně klienta zobrazí chybovou zprávu.

![Stránka indexu studentů zobrazující chyby délky řetězce](complex-data-model/_static/string-length-errors.png)

V **Průzkumník objektů systému SQL Server** (SSOX) otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .

![Tabulka studentů v SSOX před migracemi](complex-data-model/_static/ssox-before-migration.png)

Na předchozím obrázku je znázorněno schéma `Student` tabulky. Pole název mají typ `nvarchar(MAX)`, protože migrace nejsou v databázi spuštěny. Po spuštění migrace později v tomto kurzu se zobrazí pole název `nvarchar(50)`.

### <a name="the-column-attribute"></a>Atribut Column

Atributy mohou řídit způsob, jakým jsou třídy a vlastnosti mapovány na databázi. V této části se pro mapování názvu vlastnosti `FirstMidName` na FirstName v databázi používá atribut `Column`.

Při vytvoření databáze se názvy vlastností v modelu používají pro názvy sloupců (s výjimkou případů, kdy se používá atribut `Column`).

`Student` model používá `FirstMidName` pro pole jméno a příjmení, protože pole může obsahovat také prostřední jméno.

Aktualizujte soubor *student.cs* pomocí následujícího zvýrazněného kódu:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

V předchozí změně `Student.FirstMidName` v aplikaci namapovány do sloupce `FirstName` tabulky `Student`.

Přidání atributu `Column` změní model zálohování `SchoolContext`. Model zálohovaný `SchoolContext` se už neshoduje s databází. Pokud je aplikace spuštěná před použitím migrace, vygeneruje se tato výjimka:

```SQL
SqlException: Invalid column name 'FirstName'.
```

Aktualizace databáze:

* Sestavte projekt.
* Otevřete okno příkazového řádku ve složce projektu. Zadáním následujících příkazů vytvořte novou migraci a aktualizujte databázi:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

Příkaz `migrations add ColumnFirstName` generuje následující varovnou zprávu:

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

Upozornění je vygenerováno, protože pole s názvem jsou nyní omezena na 50 znaků. Pokud má název v databázi více než 50 znaků, ztratí se 51 na poslední znak.

* Otestujte aplikaci.

Otevřete tabulku student v SSOX:

![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

Před použitím migrace byly sloupce názvu typu [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Sloupce s názvem jsou nyní `nvarchar(50)`. Název sloupce se změnil z `FirstMidName` na `FirstName`.

> [!Note]
> V následující části sestavení aplikace v některých fázích generuje chyby kompilátoru. Pokyny určují, kdy se má aplikace sestavit.

## <a name="student-entity-update"></a>Aktualizace entity studenta

![Entita studenta](complex-data-model/_static/student-entity.png)

Aktualizujte *modely/student. cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Požadovaný atribut

Atribut `Required` vytváří pole vlastnosti názvu Required. Atribut `Required` není potřebný pro typy, které nejsou null, jako jsou například typy hodnot (`DateTime`, `int`, `double`atd.). Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.

Atribut `Required` mohl být nahrazen parametrem minimální délky v atributu `StringLength`:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Atribut zobrazení

Atribut `Display` určuje, že titulek pro textová pole by měl být "jméno", "příjmení", "celé jméno" a "datum zápisu". Výchozí titulky neobsahovaly místo dělení slov, například "LastName".

### <a name="the-fullname-calculated-property"></a>Vypočítaná vlastnost FullName

`FullName` je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností. `FullName` nelze nastavit, má pouze přistupující objekt get. V databázi není vytvořen žádný `FullName` sloupec.

## <a name="create-the-instructor-entity"></a>Vytvořit entitu instruktora

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

Vytvořte *modely/Instructor. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

Více atributů může být na jednom řádku. Atributy `HireDate` mohou být zapsány následujícím způsobem:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Navigační vlastnosti CourseAssignments a OfficeAssignment

Vlastnosti `CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.

Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Pokud navigační vlastnost obsahuje více entit:

* Musí se jednat o typ seznamu, kde je možné přidávat, odstraňovat a aktualizovat položky.

Mezi typy vlastností navigace patří:

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

Je-li zadán `ICollection<T>`, EF Core ve výchozím nastavení vytvoří kolekci `HashSet<T>`.

`CourseAssignment` entita je vysvětlena v části u vztahů m:n.

Firemní pravidla společnosti Contoso vysokých škol, že vyučující může mít maximálně jednu kancelář. Vlastnost `OfficeAssignment` obsahuje jednu entitu `OfficeAssignment`. `OfficeAssignment` má hodnotu null, pokud není přiřazen žádný systém Office.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>Vytvoření entity OfficeAssignment

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Klíčový atribut

Atribut `[Key]` slouží k identifikaci vlastnosti jako primárního klíče (PK), pokud je název vlastnosti něco jiného než classnameID nebo ID.

Mezi entitami `Instructor` a `OfficeAssignment` existuje vztah 1:1 nebo jedna. Přiřazení kanceláře existuje jenom ve vztahu k instruktorovi, ke kterému je přiřazený. `OfficeAssignment` PK je také jeho cizí klíč (FK) k entitě `Instructor`. EF Core nemůže automaticky rozpoznat `InstructorID` jako PK `OfficeAssignment` z těchto důvodů:

* `InstructorID` nedodržuje konvence pojmenování ID nebo classnameID.

Proto atribut `Key` slouží k identifikaci `InstructorID` jako PK:

```csharp
[Key]
public int InstructorID { get; set; }
```

Ve výchozím nastavení EF Core považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.

### <a name="the-instructor-navigation-property"></a>Navigační vlastnost instruktora

Navigační vlastnost `OfficeAssignment` pro `Instructor` entitu může mít hodnotu null, protože:

* Typy odkazů (například třídy mohou mít hodnotu null).
* Instruktor nemusí mít přiřazení kanceláře.

Entita `OfficeAssignment` má vlastnost navigace `Instructor`, která není null, protože:

* `InstructorID` nemůže mít hodnotu null.
* Přiřazení kanceláře nemůže existovat bez instruktora.

Pokud má entita `Instructor` související entitu `OfficeAssignment`, Každá entita má odkaz na jinou entitu v její navigační vlastnosti.

Atribut `[Required]` lze použít pro `Instructor` navigační vlastnost:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Předchozí kód určuje, že musí existovat související instruktor. Předchozí kód není nezbytný, protože `InstructorID` cizí klíč (což je také PK), nemůže mít hodnotu null.

## <a name="modify-the-course-entity"></a>Úprava entity kurzu

![Entita kurzu](complex-data-model/_static/course-entity.png)

Aktualizujte *modely/Course. cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Entita `Course` má `DepartmentID`vlastnost cizího klíče (FK). `DepartmentID` odkazuje na související entitu `Department`. `Course` entita má vlastnost navigace `Department`.

EF Core nevyžaduje vlastnost FK pro datový model, pokud model má vlastnost navigace pro související entitu.

EF Core v databázi automaticky vytvoří FKs bez ohledu na to, kde jsou potřeba. EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automatické vytváření FKs. Pokud se v datovém modelu nachází FK, může být aktualizace jednodušší a efektivnější. *Představte* si například model, ve kterém není obsažena vlastnost FK `DepartmentID`. Když se načte entita kurzu, která se upraví:

* Entita `Department` má hodnotu null, pokud není explicitně načtena.
* Chcete-li aktualizovat entitu kurzu, musí být nejprve načtena `Department` entita.

Pokud je vlastnost FK `DepartmentID` obsažena v datovém modelu, není nutné načíst entitu `Department` před aktualizací.

### <a name="the-databasegenerated-attribute"></a>Atribut DatabaseGenerated

Atribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` určuje, zda je v rámci aplikace poskytnuta PK místo vygenerovaného databází.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Ve výchozím nastavení EF Core předpokládá, že jsou hodnoty PK generovány databází. DATABÁZE vygenerovala hodnoty PK většinou nejlepšího přístupu. Pro `Course` entit určuje uživatel PK. Například číslo kurzu, jako je například série 1000 pro matematické oddělení, série 2000 pro národní oddělení.

Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot. DATABÁZE může například automaticky vygenerovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován. Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizích klíčů a navigace

Vlastnosti cizího klíče (FK) a navigační vlastnosti v entitě `Course` odrážejí následující vztahy:

Kurz se přiřadí jednomu oddělení, takže existuje `DepartmentID` FK a vlastnost `Department` navigace.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Kurz může mít zaregistrovaný libovolný počet studentů, takže navigační vlastnost `Enrollments` je kolekce:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurz může být výukou více instruktorů, takže navigační vlastnost `CourseAssignments` je kolekce:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` je vysvětleno [později](#many-to-many-relationships).

## <a name="create-the-department-entity"></a>Vytvořit entitu oddělení

![Entita oddělení](complex-data-model/_static/department-entity.png)

Vytvořte *modely/oddělení. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Atribut Column

Dřív se použil atribut `Column` pro změnu mapování názvu sloupce. V kódu pro entitu `Department` se atribut `Column` používá ke změně mapování datových typů SQL. `Budget` sloupec je definován pomocí SQL Server peněžního typu v databázi:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapování sloupce není obecně vyžadováno. EF Core všeobecně vybere vhodný SQL Server datový typ založený na typu CLR pro danou vlastnost. Typ CLR `decimal` namapuje na typ `decimal` SQL Server. `Budget` je pro měnu a datový typ Money je pro měnu vhodný.

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizích klíčů a navigace

Vlastnosti FK a navigace odrážejí následující vztahy:

* Oddělení může nebo nemusí mít správce.
* Správce je vždy instruktor. Proto je vlastnost `InstructorID` zahrnutá jako FK do `Instructor` entity.

Navigační vlastnost má název `Administrator`, ale obsahuje `Instructor` entitu:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Otazník (?) v předchozím kódu určuje vlastnost s možnou hodnotou null.

Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:

```csharp
public ICollection<Course> Courses { get; set; }
```

Poznámka: podle konvence EF Core povoluje kaskádové odstranění pro FKs, která nejsou null a pro relace m:n. Kaskádové odstranění může mít za následek cyklické Kaskádové odstraňování pravidel. Cyklická kaskádová odstranění pravidel způsobí výjimku při přidání migrace.

Pokud je například vlastnost `Department.InstructorID` definovaná jako hodnota, která není null:

* EF Core konfiguruje pravidlo kaskádového odstranění, které odstraní oddělení při odstranění instruktora.
* Odstranění oddělení, když se odstraní instruktor, není zamýšleným chováním.
* Následující [rozhraní Fluent API](#fluent-api-alternative-to-attributes) by místo kaskádového nastavilo pravidlo omezení.

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

Předchozí kód zakáže kaskádové odstranění na základě vztahu oddělení a instruktor.

## <a name="update-the-enrollment-entity"></a>Aktualizace entity registrace

Záznam zápisu je pro jeden kurz, který přijímá jeden student.

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

Aktualizujte *modely/zápis. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizích klíčů a navigace

Vlastnosti CK a vlastnosti navigace odrážejí následující vztahy:

Záznam zápisu je pro jeden kurz, takže existuje `CourseID` vlastnost FK a `Course` navigační vlastnost:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Záznam zápisu je určen pro jednoho studenta, takže existuje `StudentID` vlastnost FK a `Student` navigační vlastnost:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relace m:n

Mezi entitami `Student` a `Course` existuje vztah m:n. Entita `Enrollment` funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi. "S datovou částí" znamená, že `Enrollment` tabulka obsahuje další data kromě FKs pro Spojené tabulky (v tomto případě je to PK a `Grade`).

Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit. (Tento diagram byl vygenerován pomocí [nástrojů EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6. x. Vytvoření diagramu není součástí kurzu.)

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

Každá čára relace má 1 na jednom konci a hvězdičku (*) na druhé straně, která indikuje relaci 1: n.

Pokud tabulka `Enrollment` neobsahovala informace o třídě, musí obsahovat pouze dvě FKs (`CourseID` a `StudentID`). Tabulka JOIN typu m:n bez datové části se někdy označuje jako čistá spojovací tabulka (PJT).

Entity `Instructor` a `Course` mají relaci n:n pomocí tabulky Pure JOIN.

Poznámka: EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne. Další informace najdete v tématu [relace m:n v EF Core 2,0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>Entita CourseAssignment

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Instruktory do kurzů

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

Vztah n:n od instruktora do více kurzů:

* Vyžaduje tabulku JOIN, která musí být reprezentována sadou entit.
* Je čistá spojovací tabulka (tabulka bez datové části).

Je běžné pojmenovat entitu JOIN `EntityName1EntityName2`. Například tabulka pro spojení instruktora do kurzů, která používá tento model, je `CourseInstructor`. Doporučujeme však použít název, který popisuje vztah.

Modely dat začínají jednoduchým a roste. Spojení bez datové části (PJTs) se často rozvíjejí, aby zahrnovalo datovou část. Když začnete s popisným názvem entity, nemusíte při změně tabulky JOIN měnit název. V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně. Například knihy a zákazníci mohou být propojeny s entitou JOIN nazvanou hodnocení. Pro relaci n:n od instruktora na více kurzů `CourseAssignment` přednost před `CourseInstructor`.

### <a name="composite-key"></a>Složený klíč

FKs nemohou mít hodnotu null. Dva FKs v `CourseAssignment` (`InstructorID` a `CourseID`) společně jednoznačně identifikují každý řádek tabulky `CourseAssignment`. `CourseAssignment` nevyžaduje vyhrazený PK. Vlastnosti `InstructorID` a `CourseID` fungují jako složený PK. Jediným způsobem, jak zadat složené PKs EF Core je s *rozhraním API Fluent*. V další části se dozvíte, jak nakonfigurovat složený PK.

Složený klíč zajišťuje:

* Pro jeden kurz je povoleno více řádků.
* Pro jednoho instruktora je povoleno více řádků.
* Více řádků pro stejný instruktor a kurz není povoleno.

Entita `Enrollment` JOIN definuje vlastní PK, takže je možné duplikovat toto řazení. Chcete-li zabránit těmto duplicitám:

* Přidejte do polí FK jedinečný index nebo
* Nakonfigurujte `Enrollment` s primárním kombinovaným klíčem podobným `CourseAssignment`. Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).

## <a name="update-the-db-context"></a>Aktualizace kontextu databáze

Do *data/SchoolContext. cs*přidejte následující zvýrazněný kód:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Předchozí kód přidá nové entity a nakonfiguruje neseparovaný PK entity `CourseAssignment`.

## <a name="fluent-api-alternative-to-attributes"></a>Alternativa k atributům rozhraní Fluent API

Metoda `OnModelCreating` v předchozím kódu používá ke konfiguraci chování EF Core *rozhraní Fluent API* . Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu. [Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem rozhraní Fluent API:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

V tomto kurzu se rozhraní API Fluent používá jenom pro mapování DB, které nejde s atributy dělat. Rozhraní API Fluent ale může určovat většinu pravidel formátování, ověřování a mapování, která se dají provádět s atributy.

Některé atributy, jako je `MinimumLength`, se nedají použít s rozhraním API Fluent. `MinimumLength` nemění schéma, používá pouze ověřovací pravidlo minimální délky.

Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit". Atributy a rozhraní API Fluent lze kombinovat. Existují některé konfigurace, které lze provést pouze s rozhraním API Fluent (určením složeného PK). Existují některé konfigurace, které lze provádět pouze s atributy (`MinimumLength`). Doporučený postup pro použití rozhraní Fluent API nebo atributů:

* Vyberte jednu z těchto dvou přístupů.
* Používejte vybraný postup konzistentně co nejvíce.

Některé atributy použité v tomto kurzu se používají pro:

* Pouze ověření (například `MinimumLength`).
* Pouze konfigurace EF Core (například `HasKey`).
* Konfigurace ověřování a EF Core (například `[StringLength(50)]`).

Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagram entit znázorňující vztahy

Následující ilustrace znázorňuje diagram, který nástroje EF Power Tools vytvoří pro dokončený školní model.

![Diagram entit](complex-data-model/_static/diagram.png)

Předchozí diagram znázorňuje:

* Několik čar relací 1:1 (1 až \*).
* Čára relace 1:1 (1 – 0.. 1) mezi entitami `Instructor` a `OfficeAssignment`.
* Čára vztahu nula až n-many (0.. 1 až ×) mezi entitami `Instructor` a `Department`.

## <a name="seed-the-db-with-test-data"></a>Osazení databáze pomocí testovacích dat

Aktualizujte kód v *data/DbInitializer. cs*:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

Předchozí kód poskytuje počáteční data pro nové entity. Většina tohoto kódu vytváří nové objekty entit a načítá vzorová data. Ukázková data se používají k testování. Příklady toho, kolik tabulek Join lze dosazením, najdete v tématu `Enrollments` a `CourseAssignments`.

## <a name="add-a-migration"></a>Přidání migrace

Sestavte projekt.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Pokud se spustí příkaz `database update`, vytvoří se následující chyba:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>Použití migrace

Teď, když máte existující databázi, musíte si představit, jak v nich použít budoucí změny. V tomto kurzu se dozvíte dva přístupy:

* [Vyřazení a opětovné vytvoření databáze](#drop)
* [Použijte migraci na stávající databázi](#applyexisting). I když je tato metoda složitější a časově náročná, jedná se o preferovaný přístup pro produkční prostředí z reálného světa. **Poznámka**: Toto je volitelný oddíl tohoto kurzu. Můžete provést kroky odkládací a znovu vytvořit a tuto část přeskočit. Pokud chcete postupovat podle kroků v této části, neprovádějte kroky odkládací a znovu vytvořit. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>Vyřazení a opětovné vytvoření databáze

Kód v aktualizované `DbInitializer` přidá počáteční data pro nové entity. Pokud chcete vynutit EF Core vytvoření nové databáze, vyřaďte a aktualizujte databázi:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V **konzole správce balíčků** (PMC) spusťte následující příkaz:

```PMC
Drop-Database
Update-Database
```

Pokud chcete získat informace o nápovědě, spusťte `Get-Help about_EntityFrameworkCore` z PMC.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Otevřete příkazové okno a přejděte do složky projektu. Složka projektu obsahuje soubor *Startup.cs* .

V příkazovém okně zadejte následující:

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

Spusťte aplikaci. Spuštění aplikace spustí metodu `DbInitializer.Initialize`. `DbInitializer.Initialize` naplní novou databázi.

Otevřete databázi v SSOX:

* Pokud jste dříve otevřeli SSOX, klikněte na tlačítko **aktualizovat** .
* Rozbalte **tabulky** uzlu. Zobrazí se vytvořené tabulky.

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

Projděte si tabulku **CourseAssignment** :

* Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data**.
* Ověřte, že tabulka **CourseAssignment** obsahuje data.

![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>Použít migraci na existující databázi

Tato část je volitelná. Tyto kroky fungují pouze v případě, že jste přeskočili předchozí oddíl [drop a znovu vytvořit databázi](#drop) .

Pokud jsou migrace spouštěny s existujícími daty, může dojít k omezením FK, která nesplňují stávající data. S provozními daty je potřeba provést kroky pro migraci stávajících dat. V této části najdete příklad opravy porušení omezení CK. Neprovádějte změny kódu bez zálohy. Neprovádějte změny kódu, pokud jste dokončili předchozí oddíl a aktualizovali databázi.

Soubor *{timestamp} _ComplexDataModel. cs* obsahuje následující kód:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Předchozí kód přidá do tabulky `Course` `DepartmentID` FK, který nemůže mít hodnotu null. DATABÁZE z předchozího kurzu obsahuje řádky v `Course`, takže se tabulka nedá aktualizovat pomocí migrací.

Postup při práci `ComplexDataModel` migrace s existujícími daty:

* Změňte kód tak, aby měl nový sloupec (`DepartmentID`) výchozí hodnotu.
* Vytvořte falešné oddělení s názvem "Temp", které bude sloužit jako výchozí oddělení.

#### <a name="fix-the-foreign-key-constraints"></a>Oprava omezení cizího klíče

Aktualizujte `ComplexDataModel` třídy `Up` metodu:

* Otevřete soubor *{timestamp} _ComplexDataModel. cs* .
* Odkomentujte řádek kódu, který přidá sloupec `DepartmentID` do tabulky `Course`.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Přidejte následující zvýrazněný kód. Nový kód přejde za `.CreateTable( name: "Department"` blok:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

V předchozích změnách budou existující `Course` řádky při spuštění metody `ComplexDataModel` `Up` v souvislosti s oddělením "dočasného".

Produkční aplikace by:

* Zahrnout kód nebo skripty pro přidání `Department` řádků a souvisejících `Course` řádků do nových `Department` řádků.
* Nepoužívejte oddělení "dočasné" nebo výchozí hodnotu pro `Course.DepartmentID`.

Další kurz se zabývá souvisejícími daty.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Verze tohoto kurzu pro YouTube (část 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [Verze tohoto kurzu pro YouTube (část 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/migrations)
> [Další](xref:data/ef-rp/read-related-data)

::: moniker-end
