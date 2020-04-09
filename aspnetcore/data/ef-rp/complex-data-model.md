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
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a>Razor Stránky s EF core v ASP.NET Core - Datový model - 5 z 8

Tom [Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Předchozí kurzy pracoval y základní datový model, který se skládal ze tří entit. V tomto kurzu:

* Jsou přidány další entity a vztahy.
* Datový model je přizpůsoben zadáním pravidel formátování, ověření a mapování databáze.

Vyplněný datový model je znázorněn na následujícím obrázku:

![Diagram entit](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a>Studentská entita

![Studentská entita](complex-data-model/_static/student-entity.png)

Nahraďte kód v *models/Student.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

Předchozí kód přidá `FullName` vlastnost a přidá následující atributy k existujícím vlastnostem:

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a>Vypočtená vlastnost FullName

`FullName`je vypočtená vlastnost, která vrací hodnotu vytvořenou zřetězením dvou dalších vlastností. `FullName`nelze nastavit, takže má pouze get přistupující ho. V `FullName` databázi není vytvořen žádný sloupec.

### <a name="the-datatype-attribute"></a>Atribut DataType

```csharp
[DataType(DataType.Date)]
```

U dat zápisu studenta se na všech stránkách aktuálně zobrazuje denní doba spolu s datem, i když je relevantní pouze datum. Pomocí atributů anotace dat můžete provést jednu změnu kódu, která opraví formát zobrazení na každé stránce, která zobrazuje data. 

Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) určuje datový typ, který je konkrétnější než vnitřní typ databáze. V tomto případě by mělo být zobrazeno pouze datum, nikoli datum a čas. [Výčet datového typu](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je například datum, čas, telefonní číslo, měna, e-mailová adresa atd. Atribut `DataType` může také povolit aplikaci automaticky poskytovat funkce specifické pro daný typ. Příklad:

* Propojení `mailto:` je automaticky `DataType.EmailAddress`vytvořeno pro aplikaci .
* Volič data je k `DataType.Date` dispozici ve většině prohlížečů.

Atribut `DataType` vyzařuje atributy `data-` HTML 5 (vyslovuje se pomlčka dat). Atributy `DataType` neposkytují ověření.

### <a name="the-displayformat-attribute"></a>Atribut DisplayFormat

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date`neurčuje formát zobrazeného data. Ve výchozím nastavení je pole data zobrazeno podle výchozích formátů založených na [serveru CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).

Atribut `DisplayFormat` se používá k explicitnímu určení formátu data. Nastavení `ApplyFormatInEditMode` určuje, že formátování by mělo být použito také pro upravit e-li. Některá pole by `ApplyFormatInEditMode`neměla používat . Symbol měny by například obecně neměl být zobrazen v textovém poli pro úpravy.

Atribut `DisplayFormat` lze použít samostatně. Obecně je vhodné použít `DataType` atribut s atributem. `DisplayFormat` Atribut `DataType` vyjadřuje sémantiku dat na rozdíl od způsobu jejich vykreslení na obrazovce. Atribut `DataType` poskytuje následující výhody, které `DisplayFormat`nejsou k dispozici v :

* Prohlížeč může povolit funkce HTML5. Můžete například zobrazit ovládací prvek kalendáře, symbol měny odpovídající národnímu prostředí, odkazy na e-maila a ověření vstupu na straně klienta.
* Ve výchozím nastavení prohlížeč vykresluje data ve správném formátu založeném na národním prostředí.

Další informace naleznete ve [ \<vstupní dokumentaci> pomocníka značky](xref:mvc/views/working-with-forms#the-input-tag-helper).

### <a name="the-stringlength-attribute"></a>Atribut StringLength

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

Pravidla pro ověření dat a chybové zprávy ověření lze zadat pomocí atributů. Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli. Zobrazený kód omezuje názvy na maximálně 50 znaků. Příklad, který nastaví minimální délku řetězce, je zobrazen [později](#the-required-attribute).

Atribut `StringLength` také poskytuje ověření na straně klienta a serveru. Minimální hodnota nemá žádný vliv na schéma databáze.

Atribut `StringLength` nezabrání uživateli v zadání prázdného místa pro název. Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) lze použít omezení pro vstup. Například následující kód vyžaduje, aby první znak byl velkými písmeny a zbývající znaky byly abecední:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V **Průzkumníku objektů SERVERU SQL Server** (SSOX) otevřete návrháře tabulek studenta poklepáním na tabulku **Student.**

![Tabulka studentů ve SSOX před migrací](complex-data-model/_static/ssox-before-migration.png)

Předchozí obrázek znázorňuje schéma `Student` tabulky. Pole názvu mají `nvarchar(MAX)`typ . Při vytvoření migrace a použít později v tomto `nvarchar(50)` kurzu, pole názvu se stanou v důsledku atributy délky řetězce.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V nástroji SQLite zkontrolujte definice sloupců pro `Student` tabulku. Pole názvu mají `Text`typ . Všimněte si, že `FirstMidName`pole křestního jména se nazývá . V další části změníte název tohoto `FirstName`sloupce na .

---

### <a name="the-column-attribute"></a>Atribut Column

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

Atributy můžete řídit, jak jsou třídy a vlastnosti mapovány do databáze. V `Student` modelu `Column` se atribut používá k mapování `FirstMidName` názvu vlastnosti na "FirstName" v databázi.

Při vytvoření databáze se názvy vlastností v modelu používají `Column` pro názvy sloupců (s výjimkou případů, kdy je atribut použit). Model `Student` používá `FirstMidName` pro pole křestního jména, protože pole může také obsahovat prostřední jméno.

S `[Column]` atributem `Student.FirstMidName` se v datovém `FirstName` modelu `Student` mapuje na sloupec tabulky. Přidání atributu `Column` změní model, `SchoolContext`který podporuje . Model, který `SchoolContext` podporuje databázi, již neodpovídá. Tento rozpor bude vyřešen přidáním migrace později v tomto kurzu.

### <a name="the-required-attribute"></a>Atribut Povinné

```csharp
[Required]
```

Atribut `Required` vytvoří pole vlastností názvu jako povinná. Atribut `Required` není potřeba pro typy s nulou, jako jsou `DateTime`typy `int`hodnot `double`(například , , a ). Typy, které nemohou být nulové, jsou automaticky považovány za povinná pole.

Atribut `Required` musí být `MinimumLength` použit `MinimumLength` s pro vynucení.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength`a `Required` umožňují prázdné znaky pro splnění ověření. Použijte `RegularExpression` atribut pro úplnou kontrolu nad řetězcem.

### <a name="the-display-attribute"></a>Atribut Zobrazit

```csharp
[Display(Name = "Last Name")]
```

Atribut `Display` určuje, že titulek pro textová pole by měl být "Jméno", "Příjmení", "Celé jméno" a "Datum zápisu". Výchozí titulky neměly mezeru rozdělující slova, například "Příjmení".

### <a name="create-a-migration"></a>Vytvoření migrace

Spusťte aplikaci a přejděte na stránku Studenti. Je vyvolána výjimka. Atribut `[Column]` způsobí, že EF očekávat `FirstName`najít sloupec s názvem , `FirstMidName`ale název sloupce v databázi je stále .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Chybová zpráva je podobná následujícímu příkladu:

```
SqlException: Invalid column name 'FirstName'.
```

* Do pmc zadejte následující příkazy pro vytvoření nové migrace a aktualizaci databáze:

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  První z těchto příkazů generuje následující upozornění:

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  Upozornění je generováno, protože pole názvu jsou nyní omezena na 50 znaků. Pokud název v databázi měl více než 50 znaků, 51 až poslední znak by být ztraceny.

* Otevřete tabulku Student ve SSOX:

  ![Tabulka studentů ve SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

  Před použitím migrace byly sloupce názvu typu [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Sloupce názvů jsou `nvarchar(50)`nyní . Název sloupce byl `FirstMidName` změněn `FirstName`z na .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Chybová zpráva je podobná následujícímu příkladu:

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* Otevřete příkazové okno ve složce projektu. Chcete-li vytvořit novou migraci a aktualizovat databázi, zadejte následující příkazy:

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  Příkaz aktualizace databáze zobrazí chybu jako v následujícím příkladu:

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

V tomto kurzu je způsob, jak se dostat přes tuto chybu, odstranit a znovu vytvořit počáteční migraci. Další informace naleznete v upozornění SQLite v horní části [kurzu migrace](xref:data/ef-rp/migrations).

* Odstraňte složku *Migrace.*
* Spuštěním následujících příkazů přetažením databáze, vytvořením nové počáteční migrace a aplikováním migrace:

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* Prohlédněte si tabulku Student v nástroji SQLite. Sloupec, který byl FirstMidName je nyní FirstName.

---

* Spusťte aplikaci a přejděte na stránku Studenti.
* Všimněte si, že časy nejsou zadány nebo zobrazeny spolu s daty.
* Vyberte **Vytvořit nový**a pokuste se zadat název delší než 50 znaků.

> [!Note]
> V následujících částech vytváření aplikace v některých fázích generuje chyby kompilátoru. Pokyny určují, kdy se má aplikace sestavit.

## <a name="the-instructor-entity"></a>Entita instruktora

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

Vytvořit *modely/Instructor.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

Více atributů může být na jednom řádku. Atributy `HireDate` mohou být zapsány takto:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>Navigační vlastnosti

`CourseAssignments` Vlastnosti `OfficeAssignment` a jsou navigační vlastnosti.

Instruktor může vyučovat `CourseAssignments` libovolný počet kurzů, takže je definován jako sbírka.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Instruktor může mít maximálně jednu `OfficeAssignment` kancelář, `OfficeAssignment` takže vlastnost má jednu entitu. `OfficeAssignment`je null, pokud není přiřazena žádná kancelář.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>Entita OfficeAssignment

![Entita OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Vytvořit *modely/OfficeAssignment.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Atribut Key

Atribut `[Key]` se používá k identifikaci vlastnosti jako primární klíč (PK), pokud název vlastnosti je něco jiného než classnameID nebo ID.

Existuje vztah jedna k nule nebo jeden mezi `Instructor` `OfficeAssignment` entitami a. Přiřazení kanceláře existuje pouze ve vztahu k instruktorovi, ke kterým je přiřazen. `OfficeAssignment` PK je také jeho cizí klíč (FK) k účetní jednotce. `Instructor`

EF Core nelze automaticky `InstructorID` rozpoznat jako `OfficeAssignment` PK, protože `InstructorID` nedodržuje ID nebo classnameID konvence pojmenování. Proto `Key` atribut slouží k `InstructorID` identifikaci jako PK:

```csharp
[Key]
public int InstructorID { get; set; }
```

Ve výchozím nastavení EF Core považuje klíč za negenerovaný jako nedatabázový, protože sloupec je určen pro identifikační vztah.

### <a name="the-instructor-navigation-property"></a>Navigační vlastnost instruktora

Vlastnost `Instructor.OfficeAssignment` navigace může být null, protože `OfficeAssignment` nemusí být řádek pro daného instruktora. Instruktor nemusí mít úkol v kanceláři.

Vlastnost `OfficeAssignment.Instructor` navigace bude mít vždy entitu `InstructorID` instruktora, protože typ cizího klíče je `int`, typ hodnoty s hodnotou, kterou lze nehodnotitelnou hodnotou. Úkol v kanceláři nemůže existovat bez instruktora.

Pokud `Instructor` má entita související `OfficeAssignment` entitu, každá entita má odkaz na druhou ve své navigační vlastnosti.

## <a name="the-course-entity"></a>Entita kurzu

![Entita kurzu](complex-data-model/_static/course-entity.png)

Aktualizovat *modely/Course.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

Entita `Course` má vlastnost `DepartmentID`cizího klíče (FK). `DepartmentID`odkazuje na `Department` související subjekt. Entita `Course` `Department` má navigační vlastnost.

EF Core nevyžaduje vlastnost cizího klíče pro datový model, když model má vlastnost navigace pro související entitu. EF Core automaticky vytvoří FKs v databázi všude tam, kde jsou potřeba. EF Core vytváří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automaticky vytvořené Soubory FK. Explicitní zahrnutí FK do datového modelu však může usnadnit a zefektivnit aktualizace. Zvažte například model, kde `DepartmentID` *není* zahrnuta vlastnost FK. Když je entita kurzu načtena k úpravám:

* Vlastnost `Department` je null, pokud není explicitně načtena.
* Chcete-li aktualizovat entitu kurzu, `Department` musí být entita nejprve načtena.

Když je vlastnost `DepartmentID` FK zahrnuta v datovém modelu, `Department` není nutné načítat entitu před aktualizací.

### <a name="the-databasegenerated-attribute"></a>Atribut DatabaseGenerated

Atribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` určuje, že PK je poskytována aplikací, nikoli generována databází.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Ve výchozím nastavení EF Core předpokládá, že hodnoty PK jsou generovány databází. Databáze generované je obecně nejlepší přístup. Pro `Course` entity uživatel určuje PK. Například číslo kurzu, například série 1000 pro matematické oddělení, série 2000 pro anglické oddělení.

Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot. Databáze může například automaticky generovat pole data pro záznam data vytvoření nebo aktualizace řádku. Další informace naleznete v tématu [Generated Properties](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti cizího klíče (FK) `Course` a navigační vlastnosti v entitě odrážejí následující vztahy:

Kurz je přiřazen k jednomu oddělení, `DepartmentID` takže je `Department` tu FK a navigační vlastnost.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Kurz může mít libovolný počet studentů zapsaných `Enrollments` v něm, takže navigační vlastnost je kolekce:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurz může být vyučován `CourseAssignments` více instruktory, takže navigační vlastnost je sbírka:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`je [vysvětleno později](#many-to-many-relationships).

## <a name="the-department-entity"></a>Subjekt oddělení

![Subjekt oddělení](complex-data-model/_static/department-entity.png)

Vytvořit *modely/Department.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a>Atribut Column

Dříve `Column` byl atribut použit ke změně mapování názvů sloupců. V kódu `Department` entity se `Column` atribut používá ke změně mapování datového typu SQL. Sloupec `Budget` je definován pomocí typu peněz serveru SQL Server v databázi:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapování sloupců se obvykle nevyžaduje. EF Core zvolí příslušný datový typ SERVERU SQL Server na základě typu CLR pro vlastnost. Typ CLR `decimal` se mapuje `decimal` na typ serveru SQL Server. `Budget`je pro měnu a datový typ peněz je vhodnější pro měnu.

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti FK a navigace odrážejí následující vztahy:

* Oddělení může nebo nemusí mít správce.
* Správce je vždy instruktor. Proto `InstructorID` je vlastnost zahrnuta jako `Instructor` FK k entitě.

Vlastnost navigace je `Administrator` pojmenována, ale obsahuje entitu: `Instructor`

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Otazník (?) v předchozím kódu určuje vlastnost je nullable.

Oddělení může mít mnoho kurzů, takže je navigační vlastnost Kurzů:

```csharp
public ICollection<Course> Courses { get; set; }
```

Podle konvence EF Core umožňuje kaskádové odstranění pro soubory FK s možnou nenulovou hodnotou a pro vztahy N:N. Toto výchozí chování může mít za následek cyklické kaskády odstranit pravidla. Kruhová pravidla kaskádového odstranění způsobí výjimku při přidání migrace.

Například pokud `Department.InstructorID` vlastnost byla definována jako nelze upustit, EF Core by nakonfigurovat pravidlo kaskádového odstranění. V takovém případě by oddělení bylo odstraněno při odstranění instruktora přiřazeného jako správce. V tomto scénáři omezení pravidlo by větší smysl. Následující [fluent API](#fluent-api-alternative-to-attributes) by nastavit pravidlo omezení a zakázat kaskádové odstranění.

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a>Entita Zápis

Záznam zápisu je pro jeden kurz přijatý jedním studentem.

![Entita zápisu](complex-data-model/_static/enrollment-entity.png)

Aktualizujte *modely/soubor Enrollment.cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti FK a navigační vlastnosti odrážejí následující vztahy:

Záznam zápisu je pro jeden kurz, `CourseID` takže je `Course` vlastnost FK a navigační vlastnost:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Záznam zápisu je pro jednoho studenta, takže `StudentID` je `Student` vlastnost FK a navigační vlastnost:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Vztahy N:N

Existuje vztah n:N mezi entitami `Student` a. `Course` Entita `Enrollment` funguje jako tabulka spojení N:N *s datovou částí* v databázi. "S datovou částí" znamená, že `Enrollment` tabulka obsahuje další data kromě FKs `Grade`pro spojené tabulky (v tomto případě PK a ).

Následující obrázek znázorňuje, jak tyto vztahy vypadají v diagramu entity. (Tento diagram byl generován pomocí [EF elektrické nástroje](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6.x. Vytvoření diagramu není součástí kurzu.)

![Student-Kurz mnoho k mnoha vztahům](complex-data-model/_static/student-course.png)

Každá čára vztahu má 1 na jednom konci a hvězdičku (*) na straně druhé, označující vztah 1:N.

Pokud `Enrollment` tabulka neobsahuje informace o hodnocení, musí obsahovat pouze dva`CourseID` `StudentID`fks ( a ). Tabulka spojení N:N bez datové části se někdy nazývá čistá tabulka spojení (PJT).

Entity `Instructor` `Course` a mají relaci N:N pomocí tabulky čistého spojení.

Poznámka: EF 6.x podporuje implicitní spojit tabulky pro relace N:N, ale EF Core není. Další informace naleznete [v tématu Relace N:N v EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>Entita CourseAssignment

![Entita CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Vytvořit *modely/CourseAssignment.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

Relace "N:N) instruktor-kurzy vyžaduje tabulku spojení a entita pro tuto tabulku spojení je CourseAssignment.

![Instruktor-kurzy m:M](complex-data-model/_static/courseassignment.png)

Je běžné pojmenovat entitu `EntityName1EntityName2`spojení . Například tabulka spojení Instruktor-kurzy pomocí tohoto `CourseInstructor`vzoru by byla . Doporučujeme však použít název, který popisuje vztah.

Datové modely začínají jednoduše a rostou. Spojit tabulky bez datové části (PJTs) často vyvíjet zahrnout datové části. Počínaje popisným názvem entity se název nemusí měnit při změně tabulky spojení. V ideálním případě by entita spojení měla svůj vlastní přirozený název (případně jedno slovo) v obchodní doméně. Knihy a zákazníci mohou být například propojeni s entitou spojení nazvanou Hodnocení. Pro vztah "Instruktor-kurzy n:N) `CourseAssignment` je `CourseInstructor`upřednostňován před .

### <a name="composite-key"></a>Složený klíč

Dva fks `CourseAssignment` v`InstructorID` `CourseID`( a ) společně jednoznačně `CourseAssignment` identifikovat každý řádek tabulky. `CourseAssignment`nevyžaduje vyhrazenou PK. `InstructorID` Vlastnosti `CourseID` a fungují jako složený PK. Jediný způsob, jak určit složené PKs na EF Core je s *fluent API*. V další části je uvedeno, jak nakonfigurovat složený PK.

Složený klíč zajišťuje, že:

* Pro jeden kurz je povoleno více řádků.
* Pro jednoho instruktora je povoleno více řádků.
* Pro stejného instruktora a kurz není povoleno více řádků.

Entita `Enrollment` spojení definuje vlastní PK, takže jsou možné duplikáty tohoto druhu. Chcete-li zabránit těmto duplikátům:

* Přidejte jedinečný index do polí FK nebo
* Konfigurace `Enrollment` s primárním složeným klíčem podobným . `CourseAssignment` Další informace naleznete v tématu [Indexy](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Aktualizace kontextu databáze

Aktualizovat *data/SchoolContext.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

Předchozí kód přidá nové entity a nakonfiguruje `CourseAssignment` složené PK entity.

## <a name="fluent-api-alternative-to-attributes"></a>Alternativa rozhraní FLUENT API k atributům

Metoda `OnModelCreating` v předchozím kódu používá *plynulé rozhraní API* ke konfiguraci chování EF Core. Rozhraní API se nazývá "plynulý", protože se často používá navlékání matné řady volání metody společně do jednoho příkazu. [Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem plynulérozhraní API:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

V tomto kurzu plynulá rozhraní API se používá pouze pro mapování databáze, které nelze provést s atributy. Rozhraní FLUENT API však může určit většinu pravidel formátování, ověřování a mapování, která lze provést pomocí atributů.

Některé atributy, `MinimumLength` jako je například nelze použít s plynulý rozhraní API. `MinimumLength`nezmění schéma, použije pouze pravidlo ověření minimální délky.

Někteří vývojáři dávají přednost použití rozhraní FLUENT API výhradně tak, aby mohli zachovat své třídy entit "čisté". Atributy a plynulé rozhraní API lze smíchat. Existují některé konfigurace, které lze provést pouze s fluent API (určení složené PK). Existují některé konfigurace, které lze provést pouze`MinimumLength`s atributy ( ). Doporučená praxe pro použití fluent API nebo atributy:

* Vyberte jeden z těchto dvou přístupů.
* Používejte zvolený přístup konzistentně co nejvíce.

Některé atributy použité v tomto kurzu se používají pro:

* Pouze ověření `MinimumLength`(například).
* Pouze konfigurace EF Core `HasKey`(například).
* Validace a konfigurace EF `[StringLength(50)]`Core (například).

Další informace o atributech vs. fluent API naleznete v [tématu Metody konfigurace](/ef/core/modeling/).

## <a name="entity-diagram"></a>Diagram entit

Následující obrázek znázorňuje diagram, který EF Power Tools vytvořit pro dokončený školní model.

![Diagram entit](complex-data-model/_static/diagram.png)

Předchozí diagram ukazuje:

* Několik linií vztahu 1:N (1 až). \*
* Řádek vztahu 1:0 nebo 1 (1 až 0..1) `Instructor` `OfficeAssignment` mezi entitami a.
* Řádek vztahu nula nebo jeden n (0..1 až *) mezi entitami `Instructor` a. `Department`

## <a name="seed-the-database"></a>Osivo databáze

Aktualizace kódu v *souboru Data/DbInitializer.cs*:

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

Předchozí kód poskytuje data osiva pro nové entity. Většina tohoto kódu vytvoří nové objekty entity a načte ukázková data. Ukázková data se používají k testování. Viz `Enrollments` `CourseAssignments` a příklady, kolik-k-n spojit tabulky mohou být nasazeny.

## <a name="add-a-migration"></a>Přidání migrace

Sestavte projekt.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V pmc spusťte následující příkaz.

```powershell
Add-Migration ComplexDataModel
```

Předchozí příkaz zobrazí upozornění na možnou ztrátu dat.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

Pokud `database update` je příkaz spuštěn, vytvoří se následující chyba:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

V další části se zobrazí, co dělat s touto chybou.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pokud přidáte migraci a `database update` spustíte příkaz, bude vytvořena následující chyba:

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

V další části uvidíte, jak se této chybě vyhnout.

---

## <a name="apply-the-migration-or-drop-and-re-create"></a>Použití migrace nebo přetažení a opětovné vytvoření

Nyní, když máte existující databázi, je třeba přemýšlet o tom, jak použít změny na ni. Tento kurz ukazuje dvě alternativy:

* [Přetažení a znovu vytvořit databázi](#drop). Tuto část zvolte, pokud používáte SQLite.
* [Použijte migraci na existující databázi](#applyexisting). Pokyny v této části pracovat pouze pro SQL Server, **nikoli pro SQLite**. 

Obě volby funguje pro SQL Server. Zatímco metoda apply-migration je složitější a časově náročná, je to upřednostňovaný přístup pro skutečná produkční prostředí. 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>Přetažení a opětovné vytvoření databáze

[Přeskočit tuto část,](#apply-the-migration) pokud používáte SQL Server a chcete provést přístup apply-migration v následující části.

Chcete-li vynutit EF Core k vytvoření nové databáze, přetáhněte a aktualizujte databázi:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **konzole Správce balíčků** (PMC) spusťte následující příkaz:

  ```powershell
  Drop-Database
  ```

* Odstraňte složku *Migrace* a spusťte následující příkaz:

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete příkazové okno a přejděte do složky projektu. Složka projektu obsahuje soubor *ContosoUniversity.csproj.*

* Spusťte následující příkaz:

  ```dotnetcli
  dotnet ef database drop --force
  ```

* Odstraňte složku *Migrace* a spusťte následující příkaz:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Spusťte aplikaci. Spuštění aplikace spustí `DbInitializer.Initialize` metodu. Naplní `DbInitializer.Initialize` novou databázi.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Otevřete databázi ve SSOX:

* Pokud byl ssox otevřen dříve, klepněte na tlačítko **Aktualizovat.**
* Rozbalte uzel **Tabulky.** Zobrazí se vytvořené tabulky.

  ![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

* Prohlédněte si tabulku **CourseAssignment:**

  * Klepněte pravým tlačítkem myši na tabulku **CourseAssignment** a vyberte **zobrazit data**.
  * Ověřte, zda tabulka **CourseAssignment** obsahuje data.

  ![CourseAssignment data v SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pomocí nástroje SQLite zkontrolujte databázi:

* Nové tabulky a sloupce.
* Nasazená data v tabulkách, například tabulka **CourseAssignment.**

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a>Použití migrace

Tato část je nepovinná. Tyto kroky fungují pouze pro SQL Server LocalDB a pouze v případě, že jste přeskočili předchozí [drop a znovu vytvořit databázový](#drop) oddíl.

Při migraci jsou spuštěny s existující mise, může být FK omezení, které nejsou spokojeni s existující data. S produkčními daty je nutné podniknout kroky k migraci existujících dat. Tato část obsahuje příklad opravy porušení omezení FK. Neprováděte tyto změny kódu bez zálohy. Neprováděte tyto změny kódu, pokud jste dokončili předchozí [drop a znovu vytvořit databázový](#drop) oddíl.

Soubor *{timestamp}_ComplexDataModel.cs* obsahuje následující kód:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

Předchozí kód přidá do `DepartmentID` `Course` tabulky soubor FK, který lze udát. Databáze z předchozího kurzu `Course`obsahuje řádky v aplikaci , takže tabulku nelze aktualizovat migrací.

Chcete-li, aby migrace fungovala `ComplexDataModel` s existujícími daty:

* Změňte kód tak, aby`DepartmentID`nový sloupec ( ) měl výchozí hodnotu.
* Vytvořte falešné oddělení s názvem "Temp", které bude fungovat jako výchozí oddělení.

#### <a name="fix-the-foreign-key-constraints"></a>Oprava omezení cizího klíče

Ve `ComplexDataModel` třídě migrace aktualizujte metodu: `Up`

* Otevřete soubor *{timestamp}_ComplexDataModel.cs.*
* Zakomentujte řádek kódu, `DepartmentID` který `Course` přidá sloupec do tabulky.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Přidejte následující zvýrazněný kód. Nový kód jde `.CreateTable( name: "Department"` za blokem:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

S předchozími změnami `Course` budou existující řádky po spuštění metody `ComplexDataModel.Up` souviset s oddělením "Temp".

Způsob zpracování zde uvedené situace je pro tento kurz zjednodušen. Produkční aplikace by:

* Zahrňte kód `Department` nebo skripty pro přidání řádků a souvisejících `Course` řádků do nových `Department` řádků.
* Nepoužívejte oddělení "Temp" nebo výchozí `Course.DepartmentID`hodnotu pro .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **konzole Správce balíčků** (PMC) spusťte následující příkaz:

  ```powershell
  Update-Database
  ```

Vzhledem `DbInitializer.Initialize` k tomu, že metoda je určena pouze pro práci s prázdnou databází, použijte SSOX k odstranění všech řádků v tabulkách studenta a kurzu. (Kaskádové odstranění se postará o tabulku Registrace.)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Pokud používáte SQL Server LocalDB s visual studio kód, spusťte následující příkaz:

  ```dotnetcli
  dotnet ef database update
  ```

---

Spusťte aplikaci. Spuštění aplikace spustí `DbInitializer.Initialize` metodu. Naplní `DbInitializer.Initialize` novou databázi.

## <a name="next-steps"></a>Další kroky

Další dva kurzy ukazují, jak číst a aktualizovat související data.

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/migrations)
> [Další kurz](xref:data/ef-rp/read-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Předchozí kurzy pracoval y základní datový model, který se skládal ze tří entit. V tomto kurzu:

* Jsou přidány další entity a vztahy.
* Datový model je přizpůsoben zadáním pravidel formátování, ověření a mapování databáze.

Třídy entit pro dokončený datový model jsou zobrazeny na následujícím obrázku:

![Diagram entit](complex-data-model/_static/diagram.png)

Pokud narazíte na problémy, které nelze vyřešit, stáhněte si [dokončenou aplikaci](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="customize-the-data-model-with-attributes"></a>Přizpůsobení datového modelu pomocí atributů

V této části je datový model přizpůsoben pomocí atributů.

### <a name="the-datatype-attribute"></a>Atribut DataType

Stránky studenta aktuálně zobrazují čas data zápisu. Pole kalendářních dat obvykle zobrazují pouze datum a nikoli čas.

Aktualizace *modelů/student.cs* s následujícím zvýrazněným kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

Atribut [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) určuje datový typ, který je konkrétnější než vnitřní typ databáze. V tomto případě by mělo být zobrazeno pouze datum, nikoli datum a čas. [Výčet datového typu](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) poskytuje mnoho datových typů, jako je například datum, čas, telefonní číslo, měna, e-mailová adresa atd. Atribut `DataType` může také povolit aplikaci automaticky poskytovat funkce specifické pro daný typ. Příklad:

* Propojení `mailto:` je automaticky `DataType.EmailAddress`vytvořeno pro aplikaci .
* Volič data je k `DataType.Date` dispozici ve většině prohlížečů.

Atribut `DataType` vyzařuje atributy `data-` HTML 5 (vyslovuje se pomlčka dat), které prohlížeče HTML 5 spotřebovávají. Atributy `DataType` neposkytují ověření.

`DataType.Date`neurčuje formát zobrazeného data. Ve výchozím nastavení je pole data zobrazeno podle výchozích formátů založených na [serveru CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).

Atribut `DisplayFormat` se používá k explicitnímu zadání formátu data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Nastavení `ApplyFormatInEditMode` určuje, že formátování by mělo být použito také pro upravit e-li. Některá pole by `ApplyFormatInEditMode`neměla používat . Symbol měny by například obecně neměl být zobrazen v textovém poli pro úpravy.

Atribut `DisplayFormat` lze použít samostatně. Obecně je vhodné použít `DataType` atribut s atributem. `DisplayFormat` Atribut `DataType` vyjadřuje sémantiku dat na rozdíl od způsobu jejich vykreslení na obrazovce. Atribut `DataType` poskytuje následující výhody, které `DisplayFormat`nejsou k dispozici v :

* Prohlížeč může povolit funkce HTML5. Můžete například zobrazit ovládací prvek kalendáře, symbol měny odpovídající národnímu prostředí, odkazy e-mailu, ověření vstupu na straně klienta atd.
* Ve výchozím nastavení prohlížeč vykresluje data ve správném formátu založeném na národním prostředí.

Další informace naleznete ve [ \<vstupní dokumentaci> pomocníka značky](xref:mvc/views/working-with-forms#the-input-tag-helper).

Spusťte aplikaci. Přejděte na stránku Studentský index. Časy se již nezobrazují. Každý pohled, `Student` který používá model, zobrazuje datum bez času.

![Studenty index stránku zobrazující data bez časů](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Atribut StringLength

Pravidla pro ověření dat a chybové zprávy ověření lze zadat pomocí atributů. Atribut [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) určuje minimální a maximální délku znaků, které jsou povoleny v datovém poli. Atribut `StringLength` také poskytuje ověření na straně klienta a serveru. Minimální hodnota nemá žádný vliv na schéma databáze.

Aktualizujte `Student` model následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Předchozí kód omezuje názvy na maximálně 50 znaků. Atribut `StringLength` nezabrání uživateli v zadání prázdného místa pro název. Atribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) se používá k použití omezení vstupu. Například následující kód vyžaduje, aby první znak byl velkými písmeny a zbývající znaky byly abecední:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Spusťte aplikaci:

* Přejděte na stránku Studenti.
* Vyberte **Vytvořit nový**a zadejte název delší než 50 znaků.
* Vyberte **Vytvořit**, ověření na straně klienta zobrazí chybovou zprávu.

![Stránka indexu studentů zobrazující chyby délky řetězce](complex-data-model/_static/string-length-errors.png)

V **Průzkumníku objektů SERVERU SQL Server** (SSOX) otevřete návrháře tabulek studenta poklepáním na tabulku **Student.**

![Tabulka studentů ve SSOX před migrací](complex-data-model/_static/ssox-before-migration.png)

Předchozí obrázek znázorňuje schéma `Student` tabulky. Pole názvů mají `nvarchar(MAX)` typ, protože migrace nebyly spuštěny v databázi. Při spuštění migrace později v tomto kurzu `nvarchar(50)`se pole názvů stanou .

### <a name="the-column-attribute"></a>Atribut Column

Atributy můžete řídit, jak jsou třídy a vlastnosti mapovány do databáze. V této části `Column` atribut slouží k mapování `FirstMidName` název vlastnosti "FirstName" v DB.

Při vytvoření DB názvy vlastností v modelu se používají `Column` pro názvy sloupců (s výjimkou při použití atributu).

Model `Student` používá `FirstMidName` pro pole křestního jména, protože pole může také obsahovat prostřední jméno.

Aktualizujte *soubor Student.cs* následujícím zvýrazněným kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

S předchozí změnou `Student.FirstMidName` se v aplikaci mapuje na `FirstName` sloupec tabulky. `Student`

Přidání atributu `Column` změní model, `SchoolContext`který podporuje . Model, který `SchoolContext` podporuje databázi, již neodpovídá. Pokud je aplikace spuštěná před použitím migrace, vygeneruje se následující výjimka:

```
SqlException: Invalid column name 'FirstName'.
```

Aktualizace databáze:

* Sestavte projekt.
* Otevřete příkazové okno ve složce projektu. Chcete-li vytvořit novou migraci a aktualizovat databázi, zadejte následující příkazy:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

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

Upozornění je generováno, protože pole názvu jsou nyní omezena na 50 znaků. Pokud název v DB měl více než 50 znaků, 51 až poslední znak by být ztraceny.

* Otestujete aplikaci.

Otevřete tabulku Student ve SSOX:

![Tabulka studentů ve SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

Před migrací byly sloupce názvů typu [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). Sloupce názvů jsou `nvarchar(50)`nyní . Název sloupce byl `FirstMidName` změněn `FirstName`z na .

> [!Note]
> V následující části vytváření aplikace v některých fázích generuje chyby kompilátoru. Pokyny určují, kdy se má aplikace sestavit.

## <a name="student-entity-update"></a>Aktualizace entity studenta

![Studentská entita](complex-data-model/_static/student-entity.png)

Aktualizovat *modely/Student.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Atribut Povinné

Atribut `Required` vytvoří pole vlastností názvu jako povinná. Atribut `Required` není potřeba pro typy s nulou, jako`DateTime` `int`jsou `double`typy hodnot ( , , , atd.). Typy, které nemohou být nulové, jsou automaticky považovány za povinná pole.

Atribut `Required` může být nahrazen parametrem minimální `StringLength` délky v atributu:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Atribut Zobrazit

Atribut `Display` určuje, že titulek pro textová pole by měl být "Jméno", "Příjmení", "Celé jméno" a "Datum zápisu". Výchozí titulky neměly mezeru rozdělující slova, například "Příjmení".

### <a name="the-fullname-calculated-property"></a>Vypočtená vlastnost FullName

`FullName`je vypočtená vlastnost, která vrací hodnotu vytvořenou zřetězením dvou dalších vlastností. `FullName`nelze nastavit, má pouze get přistupující ho. V `FullName` databázi není vytvořen žádný sloupec.

## <a name="create-the-instructor-entity"></a>Vytvořit entitu instruktora

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

Vytvořit *modely/Instructor.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

Více atributů může být na jednom řádku. Atributy `HireDate` mohou být zapsány takto:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Vlastnosti navigace CourseAssignments a OfficeAssignment

`CourseAssignments` Vlastnosti `OfficeAssignment` a jsou navigační vlastnosti.

Instruktor může vyučovat `CourseAssignments` libovolný počet kurzů, takže je definován jako sbírka.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Pokud vlastnost navigace obsahuje více entit:

* Musí se jednat o typ seznamu, ve kterém lze položky přidávat, odstraňovat a aktualizovat.

Typy vlastností navigace zahrnují:

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

Pokud `ICollection<T>` je zadán, EF `HashSet<T>` Core vytvoří kolekci ve výchozím nastavení.

Entita `CourseAssignment` je vysvětlena v části o vztazích N:N.

Obchodní pravidla Univerzity Contoso uvádějí, že instruktor může mít maximálně jednu kancelář. Vlastnost `OfficeAssignment` obsahuje jednu `OfficeAssignment` entitu. `OfficeAssignment`je null, pokud není přiřazena žádná kancelář.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>Vytvoření entity OfficeAssignment

![Entita OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Vytvořit *modely/OfficeAssignment.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Atribut Key

Atribut `[Key]` se používá k identifikaci vlastnosti jako primární klíč (PK), pokud název vlastnosti je něco jiného než classnameID nebo ID.

Existuje vztah jedna k nule nebo jeden mezi `Instructor` `OfficeAssignment` entitami a. Přiřazení kanceláře existuje pouze ve vztahu k instruktorovi, ke kterým je přiřazen. `OfficeAssignment` PK je také jeho cizí klíč (FK) k účetní jednotce. `Instructor` EF Core nemůže automaticky `InstructorID` rozpoznat jako `OfficeAssignment` PK, protože:

* `InstructorID`neřídí id nebo classnameID konvence pojmenování.

Proto `Key` atribut slouží k `InstructorID` identifikaci jako PK:

```csharp
[Key]
public int InstructorID { get; set; }
```

Ve výchozím nastavení EF Core považuje klíč za negenerovaný jako nedatabázový, protože sloupec je určen pro identifikační vztah.

### <a name="the-instructor-navigation-property"></a>Navigační vlastnost instruktora

Vlastnost `OfficeAssignment` navigace pro `Instructor` entitu je null, protože:

* Typy odkazů (například třídy jsou null).
* Instruktor nemusí mít úkol v kanceláři.

Entita `OfficeAssignment` má navigační `Instructor` vlastnost s nemožnou hodnotou null, protože:

* `InstructorID`nenulovatelný.
* Úkol v kanceláři nemůže existovat bez instruktora.

Pokud `Instructor` má entita související `OfficeAssignment` entitu, každá entita má odkaz na druhou ve své navigační vlastnosti.

Atribut `[Required]` lze použít pro `Instructor` vlastnost navigace:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Předchozí kód určuje, že musí existovat související instruktor. Předchozí kód je zbytečné, `InstructorID` protože cizí klíč (což je také PK) je nenulovatelný.

## <a name="modify-the-course-entity"></a>Upravit entitu kurzu

![Entita kurzu](complex-data-model/_static/course-entity.png)

Aktualizovat *modely/Course.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Entita `Course` má vlastnost `DepartmentID`cizího klíče (FK). `DepartmentID`odkazuje na `Department` související subjekt. Entita `Course` `Department` má navigační vlastnost.

EF Core nevyžaduje fk vlastnost pro datový model, když model má vlastnost navigace pro související entity.

EF Core automaticky vytvoří FKs v databázi všude tam, kde jsou potřeba. EF Core vytváří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro automaticky vytvořené Soubory FK. S FK v datovém modelu může aktualizace jednodušší a efektivnější. Zvažte například model, kde `DepartmentID` *není* zahrnuta vlastnost FK. Když je entita kurzu načtena k úpravám:

* Entita `Department` je null, pokud není explicitně načtena.
* Chcete-li aktualizovat entitu kurzu, `Department` musí být entita nejprve načtena.

Když je vlastnost `DepartmentID` FK zahrnuta v datovém modelu, `Department` není nutné načítat entitu před aktualizací.

### <a name="the-databasegenerated-attribute"></a>Atribut DatabaseGenerated

Atribut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` určuje, že PK je poskytována aplikací, nikoli generována databází.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Ve výchozím nastavení EF Core předpokládá, že hodnoty PK jsou generovány DB. DB generované Hodnoty PK je obecně nejlepší přístup. Pro `Course` entity uživatel určuje PK. Například číslo kurzu, například série 1000 pro matematické oddělení, série 2000 pro anglické oddělení.

Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot. Db může například automaticky generovat pole data pro záznam data, kdy byl řádek vytvořen nebo aktualizován. Další informace naleznete v tématu [Generated Properties](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti cizího klíče (FK) `Course` a navigační vlastnosti v entitě odrážejí následující vztahy:

Kurz je přiřazen k jednomu oddělení, `DepartmentID` takže je `Department` tu FK a navigační vlastnost.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Kurz může mít libovolný počet studentů zapsaných `Enrollments` v něm, takže navigační vlastnost je kolekce:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurz může být vyučován `CourseAssignments` více instruktory, takže navigační vlastnost je sbírka:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`je [vysvětleno později](#many-to-many-relationships).

## <a name="create-the-department-entity"></a>Vytvořit entitu Oddělení

![Subjekt oddělení](complex-data-model/_static/department-entity.png)

Vytvořit *modely/Department.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Atribut Column

Dříve `Column` byl atribut použit ke změně mapování názvů sloupců. V kódu `Department` entity se `Column` atribut používá ke změně mapování datového typu SQL. Sloupec `Budget` je definován pomocí typu peněz serveru SQL Server v databázi:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapování sloupců se obvykle nevyžaduje. EF Core obecně zvolí příslušný datový typ SQL Server na základě typu CLR pro vlastnost. Typ CLR `decimal` se mapuje `decimal` na typ serveru SQL Server. `Budget`je pro měnu a datový typ peněz je vhodnější pro měnu.

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti FK a navigace odrážejí následující vztahy:

* Oddělení může nebo nemusí mít správce.
* Správce je vždy instruktor. Proto `InstructorID` je vlastnost zahrnuta jako `Instructor` FK k entitě.

Vlastnost navigace je `Administrator` pojmenována, ale obsahuje entitu: `Instructor`

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Otazník (?) v předchozím kódu určuje vlastnost je nullable.

Oddělení může mít mnoho kurzů, takže je navigační vlastnost Kurzů:

```csharp
public ICollection<Course> Courses { get; set; }
```

Poznámka: Podle konvence EF Core umožňuje kaskádové odstranění pro soubory FK, které nesmožno null, a pro vztahy N:N. Kaskádové odstranění může vést k cyklickému kaskádovému odstranění pravidel. Kruhová pravidla kaskádového odstranění způsobí výjimku při přidání migrace.

Například pokud `Department.InstructorID` vlastnost byla definována jako nenulovatelné:

* EF Core konfiguruje pravidlo kaskádového odstranění k odstranění oddělení při odstranění instruktora.
* Odstranění oddělení při odstranění instruktora není zamýšlené chování.
* Následující [fluent API](#fluent-api-alternative-to-attributes) by nastavit pravidlo omezení namísto kaskády.

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

Předchozí kód zakáže kaskádové odstranění ve vztahu oddělení a instruktora.

## <a name="update-the-enrollment-entity"></a>Aktualizace entity Registrace

Záznam zápisu je pro jeden kurz přijatý jedním studentem.

![Entita zápisu](complex-data-model/_static/enrollment-entity.png)

Aktualizujte *modely/soubor Enrollment.cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti FK a navigační vlastnosti odrážejí následující vztahy:

Záznam zápisu je pro jeden kurz, `CourseID` takže je `Course` vlastnost FK a navigační vlastnost:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Záznam zápisu je pro jednoho studenta, takže `StudentID` je `Student` vlastnost FK a navigační vlastnost:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Vztahy N:N

Existuje vztah n:N mezi entitami `Student` a. `Course` Entita `Enrollment` funguje jako tabulka spojení N:N *s datovou částí* v databázi. "S datovou částí" znamená, že `Enrollment` tabulka obsahuje další data kromě FKs `Grade`pro spojené tabulky (v tomto případě PK a ).

Následující obrázek znázorňuje, jak tyto vztahy vypadají v diagramu entity. (Tento diagram byl generován pomocí [EF elektrické nástroje](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pro EF 6.x. Vytvoření diagramu není součástí kurzu.)

![Student-Kurz mnoho k mnoha vztahům](complex-data-model/_static/student-course.png)

Každá čára vztahu má 1 na jednom konci a hvězdičku (*) na straně druhé, označující vztah 1:N.

Pokud `Enrollment` tabulka neobsahuje informace o hodnocení, musí obsahovat pouze dva`CourseID` `StudentID`fks ( a ). Tabulka spojení N:N bez datové části se někdy nazývá čistá tabulka spojení (PJT).

Entity `Instructor` `Course` a mají relaci N:N pomocí tabulky čistého spojení.

Poznámka: EF 6.x podporuje implicitní spojit tabulky pro relace N:N, ale EF Core není. Další informace naleznete [v tématu Relace N:N v EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>Entita CourseAssignment

![Entita CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Vytvořit *modely/CourseAssignment.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Kurzy od instruktora

![Instruktor-kurzy m:M](complex-data-model/_static/courseassignment.png)

Vztah Instruktor-to-Kurzy n:N:

* Vyžaduje tabulku spojení, která musí být reprezentována sadou entit.
* Je čistá tabulka spojení (tabulka bez datové části).

Je běžné pojmenovat entitu `EntityName1EntityName2`spojení . Například tabulka spojení Instruktor-kurzy pomocí tohoto `CourseInstructor`vzoru je . Doporučujeme však použít název, který popisuje vztah.

Datové modely začínají jednoduše a rostou. Spojení bez datové části (PJTs) se často vyvíjejí tak, aby zahrnovala datovou část. Počínaje popisným názvem entity se název nemusí měnit při změně tabulky spojení. V ideálním případě by entita spojení měla svůj vlastní přirozený název (případně jedno slovo) v obchodní doméně. Knihy a zákazníci mohou být například propojeni s entitou spojení nazvanou Hodnocení. Pro vztah "Instruktor-kurzy n:N) `CourseAssignment` je `CourseInstructor`upřednostňován před .

### <a name="composite-key"></a>Složený klíč

FKs nelze utnout. Dva fks `CourseAssignment` v`InstructorID` `CourseID`( a ) společně jednoznačně `CourseAssignment` identifikovat každý řádek tabulky. `CourseAssignment`nevyžaduje vyhrazenou PK. `InstructorID` Vlastnosti `CourseID` a fungují jako složený PK. Jediný způsob, jak určit složené PKs na EF Core je s *fluent API*. V další části je uvedeno, jak nakonfigurovat složený PK.

Kompozitní klíč zajišťuje:

* Pro jeden kurz je povoleno více řádků.
* Pro jednoho instruktora je povoleno více řádků.
* Více řádků pro stejného instruktora a kurz není povoleno.

Entita `Enrollment` spojení definuje vlastní PK, takže jsou možné duplikáty tohoto druhu. Chcete-li zabránit těmto duplikátům:

* Přidejte jedinečný index do polí FK nebo
* Konfigurace `Enrollment` s primárním složeným klíčem podobným . `CourseAssignment` Další informace naleznete v tématu [Indexy](/ef/core/modeling/indexes).

## <a name="update-the-db-context"></a>Aktualizace kontextu DB

Přidejte následující zvýrazněný kód do *data/SchoolContext.cs*:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Předchozí kód přidá nové entity a nakonfiguruje `CourseAssignment` složené PK entity.

## <a name="fluent-api-alternative-to-attributes"></a>Alternativa rozhraní FLUENT API k atributům

Metoda `OnModelCreating` v předchozím kódu používá *plynulé rozhraní API* ke konfiguraci chování EF Core. Rozhraní API se nazývá "plynulý", protože se často používá navlékání matné řady volání metody společně do jednoho příkazu. [Následující kód](/ef/core/modeling/#use-fluent-api-to-configure-a-model) je příkladem plynulérozhraní API:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

V tomto kurzu plynulá rozhraní API se používá pouze pro mapování DB, které nelze provést s atributy. Rozhraní FLUENT API však může určit většinu pravidel formátování, ověřování a mapování, která lze provést pomocí atributů.

Některé atributy, `MinimumLength` jako je například nelze použít s plynulý rozhraní API. `MinimumLength`nezmění schéma, použije pouze pravidlo ověření minimální délky.

Někteří vývojáři dávají přednost použití rozhraní FLUENT API výhradně tak, aby mohli zachovat své třídy entit "čisté". Atributy a plynulé rozhraní API lze smíchat. Existují některé konfigurace, které lze provést pouze s fluent API (určení složené PK). Existují některé konfigurace, které lze provést pouze`MinimumLength`s atributy ( ). Doporučená praxe pro použití fluent API nebo atributy:

* Vyberte jeden z těchto dvou přístupů.
* Používejte zvolený přístup konzistentně co nejvíce.

Některé atributy použité v tomto kurzu se používají pro:

* Pouze ověření `MinimumLength`(například).
* Pouze konfigurace EF Core `HasKey`(například).
* Validace a konfigurace EF `[StringLength(50)]`Core (například).

Další informace o atributech vs. fluent API naleznete v [tématu Metody konfigurace](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagram entit znázorňující vztahy

Následující obrázek znázorňuje diagram, který EF Power Tools vytvořit pro dokončený školní model.

![Diagram entit](complex-data-model/_static/diagram.png)

Předchozí diagram ukazuje:

* Několik linií vztahu 1:N (1 až). \*
* Řádek vztahu 1:0 nebo 1 (1 až 0..1) `Instructor` `OfficeAssignment` mezi entitami a.
* Řádek vztahu nula nebo jeden n (0..1 až *) mezi entitami `Instructor` a. `Department`

## <a name="seed-the-db-with-test-data"></a>Osivo DB s testovacími daty

Aktualizace kódu v *souboru Data/DbInitializer.cs*:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

Předchozí kód poskytuje data osiva pro nové entity. Většina tohoto kódu vytvoří nové objekty entity a načte ukázková data. Ukázková data se používají k testování. Viz `Enrollments` `CourseAssignments` a příklady, kolik-k-n spojit tabulky mohou být nasazeny.

## <a name="add-a-migration"></a>Přidání migrace

Sestavte projekt.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

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

Pokud `database update` je příkaz spuštěn, vytvoří se následující chyba:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>Použití migrace

Nyní, když máte existující databázi, je třeba přemýšlet o tom, jak použít budoucí změny na ni. Tento kurz ukazuje dva přístupy:

* [Přetažení a opětovné vytvoření databáze](#drop)
* [Použijte migraci na existující databázi](#applyexisting). I když je tato metoda složitější a časově náročnější, je to upřednostňovaný přístup pro reálná produkční prostředí. **Poznámka:** Toto je volitelná část kurzu. Můžete provést přetažení a znovu vytvořit kroky a přeskočit tuto část. Pokud chcete postupovat podle kroků v této části, neprovázte a znovu nevytvářejte kroky. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>Přetažení a opětovné vytvoření databáze

Kód v aktualizované `DbInitializer` přidá data osiva pro nové entity. Chcete-li vynutit EF Core k vytvoření nové ho DB, přetáhněte a aktualizujte DB:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V **konzole Správce balíčků** (PMC) spusťte následující příkaz:

```powershell
Drop-Database
Update-Database
```

Spusťte `Get-Help about_EntityFrameworkCore` z PMC a získejte informace nápovědy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Otevřete příkazové okno a přejděte do složky projektu. Složka projektu obsahuje *soubor Startup.cs.*

Do příkazového okna zadejte následující:

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

Spusťte aplikaci. Spuštění aplikace spustí `DbInitializer.Initialize` metodu. Naplní `DbInitializer.Initialize` nové DB.

Otevřete DB ve SSOX:

* Pokud byl ssox otevřen dříve, klepněte na tlačítko **Aktualizovat.**
* Rozbalte uzel **Tabulky.** Zobrazí se vytvořené tabulky.

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

Prohlédněte si tabulku **CourseAssignment:**

* Klepněte pravým tlačítkem myši na tabulku **CourseAssignment** a vyberte **zobrazit data**.
* Ověřte, zda tabulka **CourseAssignment** obsahuje data.

![CourseAssignment data v SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>Použití migrace na existující databázi

Tato část je nepovinná. Tyto kroky fungují pouze v případě, že jste přeskočili předchozí [drop a znovu vytvořit databázový](#drop) oddíl.

Při migraci jsou spuštěny s existující mise, může být FK omezení, které nejsou spokojeni s existující data. S produkčními daty je nutné podniknout kroky k migraci existujících dat. Tato část obsahuje příklad opravy porušení omezení FK. Neprováděte tyto změny kódu bez zálohy. Neprovádějte tyto změny kódu, pokud jste dokončili předchozí část a aktualizovali databázi.

Soubor *{timestamp}_ComplexDataModel.cs* obsahuje následující kód:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Předchozí kód přidá do `DepartmentID` `Course` tabulky soubor FK, který lze udát. DB z předchozího kurzu `Course`obsahuje řádky v aplikaci , takže tabulka nemůže být aktualizována migrací.

Chcete-li, aby migrace fungovala `ComplexDataModel` s existujícími daty:

* Změňte kód tak, aby`DepartmentID`nový sloupec ( ) měl výchozí hodnotu.
* Vytvořte falešné oddělení s názvem "Temp", které bude fungovat jako výchozí oddělení.

#### <a name="fix-the-foreign-key-constraints"></a>Oprava omezení cizího klíče

Aktualizujte `ComplexDataModel` `Up` metodu tříd:

* Otevřete soubor *{timestamp}_ComplexDataModel.cs.*
* Zakomentujte řádek kódu, `DepartmentID` který `Course` přidá sloupec do tabulky.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Přidejte následující zvýrazněný kód. Nový kód jde `.CreateTable( name: "Department"` za blokem:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

S předchozími změnami `Course` budou existující řádky po spuštění metody `ComplexDataModel` `Up` souviset s oddělením "Temp".

Produkční aplikace by:

* Zahrňte kód `Department` nebo skripty pro přidání řádků a souvisejících `Course` řádků do nových `Department` řádků.
* Nepoužívejte oddělení "Temp" nebo výchozí `Course.DepartmentID`hodnotu pro .

Další kurz se zabývá souvisejícími daty.

## <a name="additional-resources"></a>Další zdroje

* [YouTube verze tohoto výukového programu (část 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [YouTube verze tohoto výukového programu (část 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/migrations)
> [další](xref:data/ef-rp/read-related-data)

::: moniker-end
