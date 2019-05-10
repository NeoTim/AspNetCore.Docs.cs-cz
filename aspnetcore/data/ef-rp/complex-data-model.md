---
title: Stránky Razor s EF Core v ASP.NET Core – Model dat – 5 z 8
author: rick-anderson
description: V tomto kurzu přidat další entity a relace a přizpůsobte si datový model zadáním formátování, ověřování a pravidel mapování.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: a65543f805b197031bd46ef1974d4d4a5018b2d1
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516906"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a>Stránky Razor s EF Core v ASP.NET Core – Model dat – 5 z 8

Podle [Petr Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

Z předchozích kurzů ve spolupráci s základní datový model, který se skládá z tři entity. V tomto kurzu:

* Jsou přidány další entit a vztahů.
* Datový model je upravit tak, že zadáte formátování, ověřování a pravidel mapování databáze.

Tříd entit pro dokončené datový model je vidět na následujícím obrázku:

![Entity diagram](complex-data-model/_static/diagram.png)

Pokud narazíte na potíže nelze vyřešit, stáhněte si [dokončené aplikace](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="customize-the-data-model-with-attributes"></a>Přizpůsobte si datový model s atributy

V této části je datový model přizpůsobený pomocí atributů.

### <a name="the-datatype-attribute"></a>Datový typ atributu

Na stránkách student aktuálně zobrazuje čas datum registrace. Obvykle pole datum se zobrazí pouze datum a bez času.

Aktualizace *Models/Student.cs* s následující zvýrazněný kód:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

[Datový typ](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) atribut určuje datový typ, který je specifičtější než vnitřní typ databáze. V tomto případě by měl zobrazit pouze data, ne datum a čas. [Datový typ výčtu](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje pro mnoho typů dat, jako je například telefonní číslo, datum, čas, Měna, EmailAddress, atd. `DataType` Atribut můžete také povolit aplikaci automaticky poskytovat konkrétní typ funkce. Příklad:

* `mailto:` Propojení se automaticky vytvoří pro `DataType.EmailAddress`.
* Výběr data se poskytuje pro `DataType.Date` u většiny prohlížečů.

`DataType` Atribut vysílá HTML 5 `data-` (čteno data dash) atributy, které využívají prohlížeče HTML 5. `DataType` Atributy neposkytují ověření.

`DataType.Date` neurčuje formátu, který se zobrazí datum. Ve výchozím nastavení, zobrazí se pole datum podle výchozí formát založený na serveru [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).

`DisplayFormat` Atribut se používá s ohledem na formát data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode` Nastavení určuje, že formátování také bude použito pro úpravy uživatelského rozhraní. Některá pole neměli používat `ApplyFormatInEditMode`. Symbol měny například obecně nebude se zobrazovat text do textového pole.

`DisplayFormat` Atribut lze použít samostatně. Obecně je vhodné použít `DataType` atributem `DisplayFormat` atribut. `DataType` Atribut přenáší sémantiku dat na rozdíl od vykreslování na obrazovce. `DataType` Atribut poskytuje následující výhody, které nejsou k dispozici v `DisplayFormat`:

* Prohlížeči můžete povolit funkce HTML5. Například zobrazení ovládacího prvku kalendář, symbol měny odpovídající národní prostředí, odkazy na e-mailu, vstupní ověřování na straně klienta, atd.
* Ve výchozím prohlížečem data ve správném formátu podle národního prostředí.

Další informace najdete v tématu [ \<vstupní > pomocná rutina značek v dokumentaci](xref:mvc/views/working-with-forms#the-input-tag-helper).

Spusťte aplikaci. Přejděte na stránku studenty indexu. Časy se už nezobrazují. Každé zobrazení, která používá `Student` model zobrazí datum bez času.

![Studenti indexová stránka zobrazující data bez časy](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Atribut StringLength

S atributy lze zadat pravidla ověřování dat a chybové zprávy ověření. [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) atribut určuje minimální a maximální délku znaků, které jsou povoleny v datové pole. `StringLength` Atribut také poskytuje ověřování na straně klienta i stranu serveru. Minimální hodnota nemá žádný vliv na schéma databáze.

Aktualizace `Student` modelů s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Předchozí kód omezení názvů, které se více než 50 znaků. `StringLength` Atribut nemá zabránit uživateli v zadávání prázdné znaky pro název. [Regulární výraz](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) atribut se používá k aplikování omezení na vstup. Například následující kód vyžaduje prvního znaku na velká písmena a zbývající znaky, které mají být abecední znak:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Spuštění aplikace:

* Přejděte na stránku pro studenty.
* Vyberte **vytvořit nový**a zadejte název delší než 50 znaků.
* Vyberte **vytvořit**, zobrazí se chybová zpráva ověření na straně klienta.

![Studenti index stránky zobrazující chyby délky řetězce](complex-data-model/_static/string-length-errors.png)

V **Průzkumník objektů systému SQL Server** (SSOX), otevřete Návrhář tabulky Student dvojitým kliknutím **Student** tabulky.

![Tabulky Studenti v SSOX před migrací](complex-data-model/_static/ssox-before-migration.png)

Předchozí obrázek znázorňuje schéma `Student` tabulky. Název pole mají typ `nvarchar(MAX)` protože migrace nebyla spuštěna v databázi. Spuštění migrace později v tomto kurzu se název pole se stanou `nvarchar(50)`.

### <a name="the-column-attribute"></a>Atribut sloupce

Atributy můžete řídit, jak třídy a vlastnosti jsou namapovány na databázi. V této části `Column` atribut se používá k mapování názvu `FirstMidName` vlastnost na "FirstName" v databázi.

Když se vytvoří databáze, názvy vlastností na modelu se používají pro názvy sloupců (s výjimkou, kdy `Column` atribut se používá).

`Student` Model používá `FirstMidName` pro název prvního pole, protože pole může obsahovat také křestní jméno.

Aktualizace *Student.cs* soubor s následující zvýrazněný kód:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Předchozí změny `Student.FirstMidName` v aplikaci mapuje `FirstName` sloupec `Student` tabulky.

Přidání `Column` atribut změní základní model `SchoolContext`. Základní model `SchoolContext` už neodpovídá databázi. Pokud spuštění aplikace před použitím migrace je vygenerována následující výjimka:

```SQL
SqlException: Invalid column name 'FirstName'.
```

Aktualizace databáze:

* Sestavte projekt.
* Otevřete okno příkazového řádku ve složce projektu. Zadejte následující příkazy k vytvoření nové migrace a aktualizaci databáze:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

`migrations add ColumnFirstName` Příkaz vygeneruje následující upozornění:

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

Upozornění je generována, protože název pole jsou teď omezená na 50 znaků. Pokud název databáze do více než 50 znaků, by dojít ke ztrátě 51 poslední znak.

* Testování aplikace.

Otevřete tabulku studentů v SSOX:

![Tabulky Studenti v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

Před migrací se použil, název sloupce byly typu [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Název sloupce jsou nyní `nvarchar(50)`. Změnil se název sloupce z `FirstMidName` k `FirstName`.

> [!Note]
> V následující části sestavení aplikace na několik fází generuje chyby kompilátoru. Podle pokynů určit, kdy k sestavení aplikace.

## <a name="student-entity-update"></a>Aktualizovat entitu studenta

![Student entity](complex-data-model/_static/student-entity.png)

Aktualizace *Models/Student.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Požadovaný atribut

`Required` Atribut je povinná pole název vlastnosti. `Required` Atribut není potřeba pro typy neumožňující hodnotu, jako jsou typy hodnot (`DateTime`, `int`, `double`atd.). Typy, které nemůže mít hodnotu null se automaticky považují za povinná pole.

`Required` Atribut může nahrazena minimální délku parametru `StringLength` atribut:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Atribut zobrazení

`Display` Atribut určuje, že popisek pro textová pole by měl být "Jméno", "Last Name", "Jméno" a "Datum registrace." Výchozí popisky neobsahoval mezeru dělení slova, třeba "Prijmeni".

### <a name="the-fullname-calculated-property"></a>Celý název počítané vlastnosti

`FullName` je počítaná vlastnost, která vrací hodnotu, která se vytváří zřetězením dvou dalších vlastností. `FullName` Nelze nastavit, že obsahuje pouze přístupový objekt get. Ne `FullName` sloupce se vytvoří v databázi.

## <a name="create-the-instructor-entity"></a>Vytvoření Entity instruktorem

![Entita instruktorem](complex-data-model/_static/instructor-entity.png)

Vytvoření *Models/Instructor.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

Více atributů může být na jednom řádku. `HireDate` Atributů může být napsaná následujícím způsobem:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Navigační vlastnosti CourseAssignments a OfficeAssignment

`CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.

Instruktorem můžete naučit libovolný počet kurzů, takže `CourseAssignments` je definovaná jako kolekce.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Pokud vlastnost navigace obsahuje více entit:

* Musí být typu seznamu, kde položky lze přidat, odstranit a aktualizovat.

Navigační vlastnost typy patří:

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

Pokud `ICollection<T>` není zadán, vytvoří EF Core `HashSet<T>` kolekcí ve výchozím nastavení.

`CourseAssignment` Entity je vysvětleno v části u relací m: m.

Contoso University obchodní pravidla stát, že instruktorem může mít maximálně jeden office. `OfficeAssignment` Vlastnost obsahuje jediný `OfficeAssignment` entity. `OfficeAssignment` má hodnotu null, pokud není přiřazena žádná office.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>Vytvoření OfficeAssignment entity

![OfficeAssignment entity](complex-data-model/_static/officeassignment-entity.png)

Vytvoření *Models/OfficeAssignment.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Atribut Key

`[Key]` Atribut se používá k identifikaci vlastnost jako primární klíč (PK) název vlastnosti je něco jiného než classnameID nebo ID.

Existuje jedna: nula nebo 1 vztah mezi `Instructor` a `OfficeAssignment` entity. Přiřazení office existuje pouze ve vztahu k instruktorem, které je přiřazen. `OfficeAssignment` PK má také svůj cizí klíč (Cizíklíč) `Instructor` entity. EF Core nemůže automaticky rozpoznat `InstructorID` jako PK z `OfficeAssignment` protože:

* `InstructorID` není podle ID nebo classnameID konvence.

Proto `Key` atribut se používá k identifikaci `InstructorID` jako primárnímu Klíči:

```csharp
[Key]
public int InstructorID { get; set; }
```

Ve výchozím nastavení EF Core považuje za klíč bez databáze vygenerovala protože sloupec je pro identifikující relaci.

### <a name="the-instructor-navigation-property"></a>Navigační vlastnost instruktorem

`OfficeAssignment` Navigační vlastnost pro `Instructor` entita může mít hodnotu Null protože:

* Referenční typy (jako jsou třídy s možnou hodnotou Null).
* Instruktorem nemusí mít přiřazení kanceláře.

`OfficeAssignment` Entita má zakázanou `Instructor` navigační vlastnost protože:

* `InstructorID` hodnotu Null.
* Přiřazení office nemůže existovat bez instruktorem.

Když `Instructor` entita má se souvisejícím `OfficeAssignment` entit, každá entita obsahuje odkaz na druhou v jeho navigační vlastnost.

`[Required]` Atribut můžete uplatnit `Instructor` navigační vlastnost:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Předchozí kód určuje, že musí být související instruktorem. Předchozí kód není nutný, protože `InstructorID` cizí klíč (což je také primárnímu Klíči) je null.

## <a name="modify-the-course-entity"></a>Upravit Entity kurzu

![Kurz entity](complex-data-model/_static/course-entity.png)

Aktualizace *Models/Course.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

`Course` Entita má vlastnost cizí klíč (Cizíklíč) `DepartmentID`. `DepartmentID` odkazuje na související `Department` entity. `Course` Má entita `Department` navigační vlastnost.

EF Core nevyžaduje vlastnosti cizího klíče pro daný datový model, pokud model nemá vlastnost navigace u související entity.

EF Core FKs automaticky vytvoří v databázi bez ohledu na to budete potřebovat. EF Core vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automaticky vytvořené FKs. Máte cizího klíče v datovém modelu můžete provést aktualizace, jednodušší a efektivnější. Představte si třeba modelu kde vlastnost FK `DepartmentID` je *není* zahrnuté. Když kurzu entity jsou načtena upravit:

* `Department` Entity má hodnotu null, pokud nejsou explicitně načtení.
* K aktualizaci kurzu entity `Department` musíte entitu nejdřív načíst.

Při vlastnost FK `DepartmentID` je zahrnuta v datovém modelu, není nutné načíst `Department` entity před aktualizace.

### <a name="the-databasegenerated-attribute"></a>Atribut DatabaseGenerated

`[DatabaseGenerated(DatabaseGeneratedOption.None)]` Atribut určuje, že primárnímu Klíči je poskytovaný aplikací místo generován databází.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Ve výchozím nastavení EF Core předpokládá, že PK hodnoty jsou generovány pomocí databáze. DB generované PK hodnoty je obvykle nejlepší přístup. Pro `Course` entity, uživatel Určuje, PK Například kurzu číslo, například řadu 1000 pro oddělení matematické, řadu 2000 pro anglickou oddělení.

`DatabaseGenerated` Atribut lze použít také pro generování výchozích hodnot. Například databáze může automaticky generovat pole s datem a zaznamenávat data řádku byl vytvořen nebo aktualizován. Další informace najdete v tématu [vygenerovaným vlastnostem](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti cizího klíče (Cizíklíč) a navigačních vlastností v `Course` entity zahrnují následující vztahy:

Kurz je přiřazena jednoho oddělení, takže `DepartmentID` FK a `Department` navigační vlastnost.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Kurz můžete mít libovolný počet studentů zaregistrované, takže `Enrollments` navigační vlastnost je kolekce:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurz může být vedená instruktorů více, proto `CourseAssignments` navigační vlastnost je kolekce:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` je vysvětleno [později](#many-to-many-relationships).

## <a name="create-the-department-entity"></a>Vytvoření entity oddělení

![Oddělení entity](complex-data-model/_static/department-entity.png)

Vytvoření *Models/Department.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Atribut sloupce

Dříve `Column` atributu byl použit Chcete-li změnit název mapování sloupců. V kódu `Department` entity, `Column` atribut se používá ke změně mapování datového typu SQL. `Budget` Sloupec je definovaný pomocí typ money systému SQL Server v databázi:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapování sloupce není obvykle potřeba. EF Core obecně vybere odpovídající datový typ SQL serveru na základě typu CLR pro vlastnost. Modul CLR `decimal` zadejte mapuje se na serveru SQL Server `decimal` typu. `Budget` je pro měnu, a datový typ money je vhodnější pro měny.

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti cizího klíče a navigace zahrnují následující vztahy:

* Oddělení může nebo nemusí být správce.
* Správce je vždy instruktorem. Proto `InstructorID` vlastnost je zahrnutý jako FK k `Instructor` entity.

Navigační vlastnost jmenuje `Administrator` obsahuje, ale `Instructor` entity:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Otazník (?) v předchozím kódu určuje, že vlastnost může mít hodnotu Null.

Oddělení může mít mnoho kurzů, tedy navigační vlastnost kurzy:

```csharp
public ICollection<Course> Courses { get; set; }
```

Poznámka: Podle konvence EF Core umožňuje kaskádové odstranění pro Null FKs a vztahy many-to-many. Kaskádové odstranění může způsobit Cyklické kaskádové odstranění pravidla. Kruhový Kaskádové odstraňování pravidel způsobí, že při migraci se přidá výjimku.

Například pokud `Department.InstructorID` vlastnost byla definována jako Null:

* EF Core nakonfiguruje kaskádové odstranění pravidlo můžete odstranit oddělení, když se kurzů vedených odstraní.
* Odstranění oddělení při odstranění kurzů vedených není zamýšlené chování.
* Následující rozhraní API fluent by nastavit pravidlo omezit, místo na sebe.

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

Předchozí kód zakáže kaskádové odstranění relace oddělení instruktorem.

## <a name="update-the-enrollment-entity"></a>Aktualizace registrace entity

Záznam registrace je pro jeden kurz provedenou na základě jedné studentů.

![Registrace entity](complex-data-model/_static/enrollment-entity.png)

Aktualizace *Models/Enrollment.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti cizího klíče a navigačních vlastností zahrnují následující vztahy:

Záznam registrace je pro jeden kurz, tedy `CourseID` vlastnosti cizího klíče a `Course` navigační vlastnost:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Záznam registrace je pro jeden student, tedy `StudentID` vlastnosti cizího klíče a `Student` navigační vlastnost:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relace m: N

Existuje vztah n: n mezi `Student` a `Course` entity. `Enrollment` Entity funguje jako tabulka many-to-many spojení *s datovou částí* v databázi. "S datovou částí" znamená, že `Enrollment` tabulka obsahuje další data kromě FKs pro spojené tabulky (v tomto případě primárnímu Klíči a `Grade`).

Následující obrázek znázorňuje, jak tyto vztahy vypadat v diagramu entity. (Tento diagram se vygeneroval pomocí [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6.x. Vytvoření diagramu, které nejsou součástí tohoto kurzu.)

![Kurz student mnoho na mnoho vztah](complex-data-model/_static/student-course.png)

Každý řádek vztah má 1 na jednom konci a hvězdičku (*) na druhém, určující vztah jeden mnoho.

Pokud `Enrollment` tabulky nezahrnuli informace na podnikové úrovni, třeba jenom tak, aby obsahovala dva FKs (`CourseID` a `StudentID`). Tabulku spojení many-to-many bez datové části se někdy nazývá čistě spojení tabulky (PJT).

`Instructor` a `Course` entity mají vztah many-to-many pomocí tabulky čistě spojení.

Poznámka: EF 6.x podporuje implicitní spojení tabulek pro vztahy many-to-many, ale EF Core nepodporuje. Další informace najdete v tématu [Many-to-many vztahy v EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>CourseAssignment entity

![CourseAssignment entity](complex-data-model/_static/courseassignment-entity.png)

Vytvoření *Models/CourseAssignment.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Kurzů vedených kurzy

![M:M kurzů vedených kurzy](complex-data-model/_static/courseassignment.png)

Relace many-to-many kurzů vedených kurzy:

* Vyžaduje tabulku spojení, která musí být reprezentována sadu entit.
* Je čistě vazební tabulka (tabulka bez datové části).

Je běžné název entity spojení `EntityName1EntityName2`. Například tabulku spojení kurzů vedených kurzy použití tohoto modelu je `CourseInstructor`. Doporučujeme však použít název, který popisuje relace.

Datové modely začíná jednoduchou a zvýší. Žádné datové spojení (PJTs) často vyvíjet zahrnout datové části. Začněte s entity popisný název, název nemusí změnit při změně tabulku spojení. Spojení entit v ideálním případě by mít svůj vlastní přirozené název (může být jediné slovo) v obchodní domény. Například může knihy a zákazníci propojené s spojení entitu s názvem hodnocení. Pro relaci many-to-many kurzů vedených – kurzy `CourseAssignment` je upřednostňované nad `CourseInstructor`.

### <a name="composite-key"></a>Složený klíč

FKs nejsou s možnou hodnotou Null. Dvě FKs v `CourseAssignment` (`InstructorID` a `CourseID`) společně jednoznačné identifikaci jednotlivých řádků `CourseAssignment` tabulky. `CourseAssignment` nevyžaduje vyhrazený PK `InstructorID` a `CourseID` vlastnosti fungovat jako složený PK Jediný způsob, jak určit složené PKs na EF Core je *rozhraní fluent API*. V další části ukazuje, jak nakonfigurovat složené PK

Složený klíč zajistí:

* Více řádků jsou povoleny pro jeden kurz.
* Více řádků jsou povoleny pro jeden instruktorem.
* Více řádků pro stejné instruktorem a kurzu není povoleno.

`Enrollment` Entity spojení definuje vlastní PK tak, aby byly možné duplicity toto řazení. Aby se tyto duplicitní hodnoty:

* Přidat jedinečný index pro pole cizího klíče nebo
* Konfigurace `Enrollment` s primární složený klíč podobný `CourseAssignment`. Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).

## <a name="update-the-db-context"></a>Aktualizovat kontext databáze

Přidejte následující zvýrazněný kód do *Data/SchoolContext.cs*:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Předchozí kód přidá nové entity a nakonfiguruje `CourseAssignment` složené PK entity

## <a name="fluent-api-alternative-to-attributes"></a>Fluent API alternativou k atributům

`OnModelCreating` Metoda v předchozím kódu používá *rozhraní fluent API* konfigurace chování EF Core. Rozhraní API se nazývá "fluent", protože je často používána zavěšování řadu volání metody společně na jediném příkazu. [Následující kód](/ef/core/modeling/#methods-of-configuration) je příkladem rozhraní fluent API:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

V tomto kurzu se používá rozhraní fluent API pouze pro mapování databáze, které nelze provést s atributy. Rozhraní fluent API můžete však určit většinu formátování, ověřování a pravidla mapování, které lze provést s atributy.

Některé atributy, jako `MinimumLength` nelze použít s rozhraním API fluent. `MinimumLength` nedojde ke změně schématu, vztahuje se pouze minimální délka ověřovacího pravidla.

Někteří vývojáři dávají přednost používání rozhraní fluent API výhradně tak, aby se zachovat jejich tříd entit "vyčištění." Atributy a rozhraní fluent API lze kombinovat. Existují některé konfigurace, které lze provést pouze pomocí rozhraní fluent API (výběr kompozitní PK). Existují některé konfigurace, které lze provést pouze s atributy (`MinimumLength`). Doporučené postupy pro využití fluent API nebo atributy:

* Zvolte jednu z těchto dvou přístupů.
* Použijte konzistentně dosahovat zvolený způsob.

Některé atributy použité v tomto kurzu se používají pro:

* Pouze ověřování (například `MinimumLength`).
* EF Core jenom konfiguraci (například `HasKey`).
* Konfigurace ověření a EF Core (například `[StringLength(50)]`).

Další informace o atributech vs. rozhraní fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/#methods-of-configuration).

## <a name="entity-diagram-showing-relationships"></a>Diagram znázorňující entitami

Následující obrázek znázorňuje diagram, který EF Power Tools vytvořit pro dokončené model školy.

![Entity diagram](complex-data-model/_static/diagram.png)

Předchozí diagram znázorňuje:

* Několik řádků vztah jeden mnoho (1 k \*).
* Řádek jedna nula nebo 1 v relaci m (1-0..1) mezi `Instructor` a `OfficeAssignment` entity.
* Čára relace nula nebo 1 n (0.. 1 na *) mezi `Instructor` a `Department` entity.

## <a name="seed-the-db-with-test-data"></a>Počáteční hodnota databáze se testovací Data

Aktualizovat kód v *Data/DbInitializer.cs*:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

Předchozí kód poskytuje data počáteční hodnotu pro nové entity. Většina tento kód vytvoří nové objekty entity a načte ukázková data. Ukázková data se používá pro účely testování. Zobrazit `Enrollments` a `CourseAssignments` pro příklady jak many-to-many spojení tabulek můžete nasadí.

## <a name="add-a-migration"></a>Přidejte migraci

Sestavte projekt.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```console
dotnet ef migrations add ComplexDataModel
```

---

Předchozí příkaz zobrazí varování týkající se ke ztrátě.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Pokud `database update` příkaz spustit, je vytvořen následující chybu:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>Použití migrace

Teď, když máte existující databázi, musíte přemýšlet o tom, jak na ně vztahují budoucí změny. Tento kurz ukazuje dva přístupy:

* [Vyřadit a znovu vytvořit databázi](#drop)
* [Použití migrace k existující databázi](#applyexisting). Tato metoda je složité a časově náročné, je upřednostňovaný způsob pro každodenní praxe produkční prostředí. **Poznámka:** Toto je volitelné části tohoto kurzu. Můžete provést rozevírací a znovu vytvořit kroky a tuto část přeskočit. Pokud chcete postupovat podle kroků v této části, nemáte proveďte rozevírací nabídku a znovu vytvořit kroky. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>Vyřadit a znovu vytvořit databázi

Kód v aktualizovaném `DbInitializer` přidá data počáteční hodnotu pro nové entity. Pokud chcete vynutit EF Core k vytvoření nové databáze, vyřaďte a aktualizaci databáze:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V **Konzola správce balíčků** (PMC), spusťte následující příkaz:

```PMC
Drop-Database
Update-Database
```

Spustit `Get-Help about_EntityFrameworkCore` z konzole PMC zobrazíte nápovědu.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Otevřete okno příkazového řádku a přejděte do složky projektu. Obsahuje složky projektu *Startup.cs* souboru.

V příkazovém okně zadejte následující:

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

Spusťte aplikaci. Spuštění aplikace spuštěná `DbInitializer.Initialize` metody. `DbInitializer.Initialize` Naplní nová databáze.

Otevřete v SSOX databáze:

* Pokud SSOX byl dříve otevřen, klikněte na tlačítko **aktualizovat** tlačítko.
* Rozbalte **tabulky** uzlu. Vytvořené tabulky se zobrazí.

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

Zkontrolujte **CourseAssignment** tabulky:

* Klikněte pravým tlačítkem myši **CourseAssignment** tabulce a vybrat **Data zobrazení**.
* Ověřte, **CourseAssignment** tabulka obsahuje data.

![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>Použití migrace k existující databázi

Tato část je nepovinná. Tento postup funguje pouze v případě, že jste přeskočili předchozí [vyřaďte a znovu vytvořit databázi](#drop) oddílu.

Při spuštění migrace s existujícími daty, může být omezení cizího klíče, které nejsou splněné s existujícími daty. S použitím provozních dat musí být kroky potřebného k migraci existujících dat. Tato část poskytuje příklad opravuje narušení omezení cizího klíče. Neprovádějte změny kódu bez předchozího provedení zálohy. Nenastavujte tyto změny kódu, je-li dokončit předchozí části a aktualizována v databázi.

*{Timestamp}_ComplexDataModel.cs* soubor obsahuje následující kód:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Předchozí kód přidá neumožňující `DepartmentID` FK k `Course` tabulky. Databáze z předchozí kurz o službě obsahuje řádky v `Course`, takže tuto tabulku nelze aktualizovat migrace.

Chcete-li `ComplexDataModel` migraci vám nebudeme nic s existujícími daty:

* Změňte kód a zadejte nový sloupec (`DepartmentID`) výchozí hodnotu.
* Vytvořte falešnou oddělení s názvem "Temp" tak, aby fungoval jako výchozí oddělení.

#### <a name="fix-the-foreign-key-constraints"></a>Oprava omezení cizího klíče

Aktualizace `ComplexDataModel` třídy `Up` metody:

* Otevřít *{timestamp}_ComplexDataModel.cs* souboru.
* Odkomentujte řádek kódu, který se přidá `DepartmentID` sloupec, který se `Course` tabulky.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Přidejte následující zvýrazněný kód. Nový kód prochází po `.CreateTable( name: "Department"` blok:

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

S předchozím změní, stávající `Course` řádky se související s oddělení "Temp" po `ComplexDataModel` `Up` metoda spuštění.

Produkční aplikace bude:

* Zahrnout kódu nebo skriptech, chcete-li přidat `Department` řádky a související `Course` řádky k novému `Department` řádků.
* Nepoužívat oddělení "Temp" nebo výchozí hodnotu pro `Course.DepartmentID`.

Další kurz se zaměřuje na související data.

## <a name="additional-resources"></a>Další zdroje

* [YouTube verzi tohoto kurzu (část 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [YouTube verzi tohoto kurzu (část 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)



> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/migrations)
> [další](xref:data/ef-rp/read-related-data)
