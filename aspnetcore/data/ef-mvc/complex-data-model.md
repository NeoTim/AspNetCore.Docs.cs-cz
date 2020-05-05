---
title: 'Kurz: vytvoření složitého datového modelu – ASP.NET MVC pomocí EF Core'
description: V tomto kurzu přidejte další entity a vztahy a upravte datový model zadáním formátování, ověřování a pravidel mapování.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 5e617a201cbd133e695bdadc08dc6c797f97b6be
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773624"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>Kurz: vytvoření složitého datového modelu – ASP.NET MVC pomocí EF Core

V předchozích kurzech jste pracovali s jednoduchým datovým modelem, který se skládá ze tří entit. V tomto kurzu přidáte další entity a vztahy a budete přizpůsobovat datový model zadáním pravidel formátování, ověřování a mapování databáze.

Až budete hotovi, třídy entit vytvoří dokončený datový model, který je znázorněn na následujícím obrázku:

![Diagram entit](complex-data-model/_static/diagram.png)

V tomto kurzu jste:

> [!div class="checklist"]
> * Přizpůsobení datového modelu
> * Provedení změn v entitě studenta
> * Vytvořit entitu instruktora
> * Vytvořit entitu OfficeAssignment
> * Upravit entitu kurzu
> * Vytvořit entitu oddělení
> * Upravit entitu registrace
> * Aktualizace kontextu databáze
> * Počáteční databáze s testovacími daty
> * Přidání migrace
> * Změna připojovacího řetězce
> * Aktualizace databáze

## <a name="prerequisites"></a>Požadavky

* [Použití migrace EF Core](migrations.md)

## <a name="customize-the-data-model"></a>Přizpůsobení datového modelu

V této části se dozvíte, jak přizpůsobit datový model pomocí atributů, které určují pravidla formátování, ověřování a mapování databáze. Potom v několika následujících částech vytvoříte kompletní model školních dat přidáním atributů do tříd, které jste už vytvořili, a vytvořením nových tříd pro zbývající typy entit v modelu.

### <a name="the-datatype-attribute"></a>Atribut DataType

Pro data o registraci studenta se aktuálně zobrazují všechny webové stránky, které jsou aktuálně současně s datem, i když jsou pro toto pole k disstará data. Pomocí atributů datových poznámek můžete vytvořit jednu změnu kódu, která bude opravovat formát zobrazení v každém zobrazení, které zobrazuje data. Chcete-li zobrazit příklad toho, jak to provést, přidáte atribut do `EnrollmentDate` vlastnosti `Student` třídy.

V *modelech/student. cs* `using` přidejte příkaz pro `System.ComponentModel.DataAnnotations` obor názvů a přidejte `DataType` `DisplayFormat` atributy do `EnrollmentDate` vlastnosti, jak je znázorněno v následujícím příkladu:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

`DataType` Atribut slouží k určení datového typu, který je konkrétnější než vnitřní typ databáze. V tomto případě chceme sledovat pouze datum, nikoli datum a čas. `DataType` Výčet poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress a další. `DataType` Atribut může také povolit aplikaci automatické poskytování funkcí specifických pro typ. Například `mailto:` odkaz lze vytvořit pro `DataType.EmailAddress`a `DataType.Date` v prohlížečích, které podporují HTML5, lze zadat selektor data. `DataType` Atribut emituje atributy HTML 5 `data-` (s vyslovnou datovou pomlčkou), které mohou prohlížeče HTML 5 pochopit. `DataType` Atributy neposkytují žádné ověření.

`DataType.Date`neurčuje formát data, které se zobrazí. Ve výchozím nastavení se datové pole zobrazuje v závislosti na výchozích formátech na základě objektu CultureInfo serveru.

`DisplayFormat` Atribut slouží k explicitnímu zadání formátu data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode` Nastavení určuje, že formátování by mělo být použito i v případě, že se hodnota zobrazí v textovém poli pro úpravy. (Možná nebudete chtít, aby se pro některá pole, například pro hodnoty měny, nemuseli v textovém poli pro úpravy chtít symbol měny.)

Můžete použít `DisplayFormat` atribut sám o sobě, ale obecně je vhodné použít `DataType` atribut také. `DataType` Atribut vyjadřuje sémantiku dat na rozdíl od způsobu vykreslování na obrazovce a poskytuje následující výhody, které se `DisplayFormat`nedají získat:

* Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolu měny odpovídající národním prostředí, e-mailových odkazů, některých ověření vstupu na straně klienta atd.).

* Ve výchozím nastavení bude prohlížeč data vykreslovat pomocí správného formátu na základě vašeho národního prostředí.

Další informace najdete v [ \<dokumentaci k rutině Input> tag](../../mvc/views/working-with-forms.md#the-input-tag-helper).

Spusťte aplikaci, klikněte na stránku indexu studentů a Všimněte si, že časy se už nezobrazuje pro data registrace. Totéž platí pro všechna zobrazení, která používají model studenta.

![Stránka indexu studentů zobrazující data bez časů](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Atribut StringLength

Můžete také zadat pravidla ověřování dat a chybové zprávy ověřování pomocí atributů. `StringLength` Atribut nastavuje maximální délku v databázi a poskytuje ověřování na straně klienta a na straně serveru pro ASP.NET Core MVC. V tomto atributu můžete také zadat minimální délku řetězce, ale minimální hodnota nemá žádný vliv na schéma databáze.

Předpokládejme, že chcete zajistit, aby uživatelé nezadali více než 50 znaků pro název. Chcete-li přidat toto omezení `StringLength` , přidejte atributy `LastName` do `FirstMidName` vlastností a, jak je znázorněno v následujícím příkladu:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

`StringLength` Atribut nezabrání uživateli v zadání prázdného místa pro název. Můžete použít `RegularExpression` atribut pro použití omezení na vstup. Například následující kód vyžaduje, aby první znak byl velkými písmeny a aby zbývající znaky byly abecedně:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

`MaxLength` Atribut poskytuje funkce podobné `StringLength` atributu, ale neposkytuje ověřování na straně klienta.

Model databáze se teď změnil způsobem, který vyžaduje změnu ve schématu databáze. Pomocí migrace aktualizujete schéma bez ztráty dat, která jste mohli přidat do databáze pomocí uživatelského rozhraní aplikace.

Uložte změny a sestavte projekt. Pak otevřete příkazové okno ve složce projektu a zadejte následující příkazy:

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

`migrations add` Příkaz upozorní na to, že může dojít ke ztrátě dat, protože změna má pro dva sloupce minimální délku.  Migrace vytvoří soubor s názvem * \<timestamp>_MaxLengthOnNames. cs*. Tento soubor obsahuje kód v `Up` metodě, která bude aktualizovat databázi tak, aby odpovídala aktuálnímu datovému modelu. `database update` Příkaz spustil tento kód.

Časové razítko s předponou názvu souboru migrace se používá Entity Framework k seřazení migrace. Před spuštěním příkazu Update-Database můžete vytvořit více migrací a všechny migrace pak budou aplikovány v pořadí, ve kterém byly vytvořeny.

Spusťte aplikaci, vyberte kartu **Students** , klikněte na **vytvořit novou**a zkuste zadat název delší než 50 znaků. Aplikace by vám měla zabránit v tom. 

### <a name="the-column-attribute"></a>Atribut Column

Můžete také použít atributy pro řízení způsobu, jakým jsou třídy a vlastnosti namapovány na databázi. Předpokládejme, že jste použili název `FirstMidName` pole jméno a příjmení, protože pole může obsahovat také prostřední jméno. Ale chcete, aby byl sloupec databáze pojmenován `FirstName`, protože uživatelé, kteří budou psát ad hoc dotazy na databázi, jsou zvyklí na tento název. Chcete-li toto mapování provést, můžete použít `Column` atribut.

`Column` Atribut určuje, že při vytvoření databáze bude název `Student` `FirstMidName` `FirstName`sloupce tabulky, která je namapována na vlastnost. Jinými slovy, pokud kód odkazuje na `Student.FirstMidName`, data budou pocházet z nebo aktualizována ve `FirstName` sloupci `Student` tabulky. Pokud neurčíte názvy sloupců, budou mít stejný název jako název vlastnosti.

V souboru *student.cs* přidejte `using` příkaz pro `System.ComponentModel.DataAnnotations.Schema` a přidejte do `FirstMidName` vlastnosti atribut název sloupce, jak je znázorněno v následujícím zvýrazněném kódu:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Přidání `Column` atributu změní model `SchoolContext`, který ho zálohuje, takže se neshoduje s databází.

Uložte změny a sestavte projekt. Pak otevřete příkazové okno ve složce projektu a zadáním následujících příkazů vytvořte další migraci:

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

V **Průzkumník objektů systému SQL Server**otevřete Návrhář tabulky student dvojitým kliknutím na tabulku **student** .

![Tabulka studentů v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

Před použitím prvních dvou migrací byly sloupce názvu typu nvarchar (MAX). Teď jsou nvarchar (50) a název sloupce se změnil z FirstMidName na FirstName.

> [!Note]
> Pokud se pokusíte kompilovat před dokončením vytváření všech tříd entit v následujících oddílech, může dojít k chybám kompilátoru.

## <a name="changes-to-student-entity"></a>Změny entity studenta

![Entita studenta](complex-data-model/_static/student-entity.png)

V *modelu/student. cs*nahraďte kód, který jste přidali dříve, následujícím kódem. Změny jsou zvýrazněny.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Požadovaný atribut

`Required` Atribut nastaví název vlastnosti povinná pole. `Required` Atribut není potřebný pro typy, které nejsou null, jako jsou typy hodnot (DateTime, int, Double, float atd.). Typy, které nemůžou mít hodnotu null, se automaticky považují za povinná pole.

`Required` Atribut musí být použit s `MinimumLength` pro `MinimumLength` vymáhání.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Atribut zobrazení

`Display` Atribut určuje, že titulek pro textová pole by měl být "jméno", "příjmení", "celé jméno" a "datum zápisu" místo názvu vlastnosti v každé instanci (což nemá mezeru dělená slova).

### <a name="the-fullname-calculated-property"></a>Vypočítaná vlastnost FullName

`FullName`je vypočtená vlastnost, která vrací hodnotu, která je vytvořena zřetězením dvou dalších vlastností. Proto má pouze přistupující objekt get a v databázi nebude `FullName` vygenerován žádný sloupec.

## <a name="create-instructor-entity"></a>Vytvořit entitu instruktora

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

Vytvořte *modely/Instructor. cs*a nahraďte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Všimněte si, že několik vlastností je stejné v entitách student a instruktor. V kurzu [Implementace dědičnosti](inheritance.md) níže v této sérii můžete tento kód Refaktorovat, aby se vyloučila redundance.

Na jeden řádek můžete umístit více atributů, takže můžete také napsat `HireDate` atributy následujícím způsobem:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Navigační vlastnosti CourseAssignments a OfficeAssignment

Vlastnosti `CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.

Instruktor může naučit libovolný počet kurzů, takže `CourseAssignments` je definován jako kolekce.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Pokud navigační vlastnost může obsahovat více entit, musí být její typ seznam, ve kterém je možné přidávat, odstraňovat a aktualizovat položky.  Můžete zadat `ICollection<T>` nebo typ jako `List<T>` nebo. `HashSet<T>` Pokud zadáte `ICollection<T>`, EF vytvoří ve `HashSet<T>` výchozím nastavení kolekci.

Důvod, proč se jedná `CourseAssignment` o entity, jsou vysvětleny níže v části o relacích n:n.

Obchodní pravidla společnosti Contoso University mají stav, že instruktor může mít jenom jednu kancelář, takže `OfficeAssignment` vlastnost obsahuje jednu entitu OfficeAssignment (která může být null, pokud není přiřazená žádná sada Office).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>Vytvořit entitu OfficeAssignment

![OfficeAssignment – entita](complex-data-model/_static/officeassignment-entity.png)

Vytvořte *modely/OfficeAssignment. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Klíčový atribut

Mezi instruktorem a entitami OfficeAssignment existuje vztah 1:1 nebo jedna. Přiřazení kanceláře existuje pouze ve vztahu k instruktorovi, kterému je přiřazeno, a proto jeho primární klíč je také cizí klíč k entitě instruktor. Entity Framework ale nemůžou automaticky rozpoznat InstructorID jako primární klíč této entity, protože jeho název nedodržuje zásady vytváření názvů classnameID. Proto `Key` atribut slouží k identifikaci jako klíč:

```csharp
[Key]
public int InstructorID { get; set; }
```

Atribut můžete použít také v `Key` případě, že má entita svůj vlastní primární klíč, ale chcete pojmenovat vlastnost jinou než CLASSNAMEID nebo ID.

Ve výchozím nastavení EF považuje klíč za generovaný nedatabází, protože sloupec je určen pro identifikaci vztahu.

### <a name="the-instructor-navigation-property"></a>Navigační vlastnost instruktora

Entita Instructor má vlastnost navigace s `OfficeAssignment` možnou hodnotou null (protože instruktor nemusí mít přiřazený Office) a entita OfficeAssignment má vlastnost navigace, která nemůže mít `Instructor` hodnotu null (protože přiřazení kanceláře nemůže existovat bez instruktora-- `InstructorID` nepovoluje hodnotu null). Pokud má entita instruktora související entitu OfficeAssignment, bude mít Každá entita odkaz na jinou entitu v její navigační vlastnosti.

Můžete umístit `[Required]` atribut do navigační vlastnosti instruktora a určit tak, že se musí jednat o související instruktor, ale nemusíte to dělat, protože `InstructorID` cizí klíč (což je také klíč k této tabulce) nemůže mít hodnotu null.

## <a name="modify-course-entity"></a>Upravit entitu kurzu

![Entita kurzu](complex-data-model/_static/course-entity.png)

V *modelu/Course. cs*nahraďte kód, který jste přidali dříve, následujícím kódem. Změny jsou zvýrazněny.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Entita kurzu má vlastnost `DepartmentID` cizího klíče, která odkazuje na související entitu oddělení a má vlastnost `Department` navigace.

Entity Framework nevyžaduje, abyste do datového modelu přidali vlastnost cizího klíče, když máte vlastnost navigace pro související entitu.  EF v databázi automaticky vytvoří cizí klíče bez ohledu na to, kde jsou potřeba, a vytvoří pro ně [vlastnosti stínu](/ef/core/modeling/shadow-properties) . Ale při používání cizího klíče v datovém modelu může být aktualizace jednodušší a efektivnější. Když například načtete entitu kurzu, která se má upravit, entita oddělení má hodnotu null, pokud ji nenačtete, takže když aktualizujete entitu kurzu, budete muset nejdřív načíst entitu oddělení. Pokud je vlastnost `DepartmentID` cizí klíč obsažena v datovém modelu, není nutné před aktualizací načíst entitu oddělení.

### <a name="the-databasegenerated-attribute"></a>Atribut DatabaseGenerated

`DatabaseGenerated` Atribut s `None` parametrem `CourseID` vlastnosti určuje, že uživatel zadá hodnoty primárního klíče místo vygenerovaného databází.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Ve výchozím nastavení Entity Framework předpokládá, že databáze generuje hodnoty primárního klíče. To je to, co chcete ve většině scénářů. U entit kurzu ale použijete uživatelem zadané číslo kurzu, jako je například řada 1000, pro jedno oddělení, 2000 Series pro jiné oddělení a tak dále.

`DatabaseGenerated` Atribut lze také použít ke generování výchozích hodnot, jako v případě databázových sloupců použitých k záznamu data vytvoření nebo aktualizace řádku.  Další informace najdete v tématu [vygenerované vlastnosti](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizích klíčů a navigace

Vlastnosti cizího klíče a navigační vlastnosti v entitě kurzu odrážejí následující vztahy:

Kurz se přiřadí jednomu oddělení, takže existuje `DepartmentID` cizí klíč a vlastnost `Department` navigace z výše uvedených důvodů.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

V rámci kurzu může být zaregistrované několik studentů, takže `Enrollments` navigační vlastnost je kolekce:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurz může být výukou více instruktorů, takže `CourseAssignments` navigační vlastnost je kolekce (Tento typ `CourseAssignment` je vysvětlen [později](#many-to-many-relationships)):

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Vytvořit entitu oddělení

![Entita oddělení](complex-data-model/_static/department-entity.png)

Vytvořte *modely/oddělení. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Atribut Column

Dříve jste použili `Column` atribut pro změnu mapování názvu sloupce. V kódu pro entitu oddělení se `Column` atribut používá ke změně mapování datových typů SQL tak, aby byl sloupec definovaný pomocí SQL Server peněžního typu v databázi:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapování sloupce není obecně vyžadováno, protože Entity Framework zvolí vhodný SQL Server datový typ založený na typu CLR, který definujete pro vlastnost. Typ CLR `decimal` se mapuje na typ SQL Server `decimal` . Ale v tomto případě víte, že se ve sloupci budou držet peněžní částky, a datový typ Money je pro to vhodnější.

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizích klíčů a navigace

Vlastnosti cizího klíče a navigace odrážejí následující vztahy:

Oddělení může nebo nemusí mít správce a správce je vždy instruktorem. Proto je `InstructorID` vlastnost zahrnutá jako cizí klíč k entitě instruktor a po označení `int` typu k označení vlastnosti jako Nullable se přidá otazník. Navigační vlastnost má název `Administrator` , ale obsahuje entitu instruktora:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Oddělení může mít spoustu kurzů, takže máme navigační vlastnost kurzů:

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Podle konvence Entity Framework umožňuje odstranit Kaskádové odstraňování cizích klíčů, které neumožňují hodnotu null, a pro relace m:n. Výsledkem může být cyklická kaskádová odstranění pravidel, která způsobí výjimku při pokusu o přidání migrace. Pokud jste například nedefinovali vlastnost oddělení. InstructorID jako Nullable, EF by nakonfigurovala pravidlo kaskádového odstranění, které odstraní oddělení, když odstraníte instruktora, což nevede k tomu, co chcete mít. Pokud vaše obchodní pravidla vyžadují `InstructorID` vlastnost, která není null, budete muset použít následující příkaz rozhraní Fluent API, který v relaci zakáže kaskádové odstranění:
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Upravit entitu registrace

![Entita registrace](complex-data-model/_static/enrollment-entity.png)

V *modelů/zápisu. cs*nahraďte kód, který jste přidali dříve, pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizích klíčů a navigace

Vlastnosti cizího klíče a vlastnosti navigace odrážejí následující vztahy:

Záznam zápisu je pro jeden kurz, takže existuje vlastnost `CourseID` cizího klíče a `Course` navigační vlastnost:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Záznam zápisu je určen pro jednoho studenta, takže existuje vlastnost `StudentID` cizího klíče a `Student` navigační vlastnost:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relace m:n

Mezi entitami student a Course existuje vztah n:n a entita registrace funguje jako tabulka JOIN typu m:n *s datovou částí* v databázi. "S datovou částí" znamená, že tabulka zápisu obsahuje další data kromě cizích klíčů pro Spojené tabulky (v tomto případě primární klíč a vlastnost třídy).

Následující ilustrace znázorňuje, co tyto vztahy vypadají jako v diagramu entit. (Tento diagram byl vygenerován pomocí Entity Framework nástrojů Power Tools pro EF 6. x; vytvoření diagramu není součástí kurzu, stačí ho použít jako ilustraci.)

![Mezi studenty hodně a mnoha](complex-data-model/_static/student-course.png)

Každá čára relace má 1 na jednom konci a hvězdičku (*) na druhé straně, která indikuje relaci 1: n.

Pokud tabulka zápisu neobsahuje informace o třídě, musí obsahovat pouze dva cizí klíče CourseID a StudentID. V takovém případě by to byla tabulka JOIN typu m:n (celá řada) bez datové části (nebo čistá spojovací tabulka) v databázi. Entity instruktor a Course mají tento druh relace m:n a vaším dalším krokem je vytvoření třídy entity, která bude fungovat jako spojovací tabulka bez datové části.

(EF 6. x podporuje implicitní spojení tabulek pro relace m:n, ale EF Core ne. Další informace najdete [v diskuzi v úložišti GitHub EF Core](https://github.com/aspnet/EntityFramework/issues/1368).)

## <a name="the-courseassignment-entity"></a>Entita CourseAssignment

![CourseAssignment – entita](complex-data-model/_static/courseassignment-entity.png)

Vytvořte *modely/CourseAssignment. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Spojování názvů entit

V databázi je vyžadována tabulka JOIN pro relaci n:n typu n:1 a musí být reprezentovaná sadou entit... Je běžné pojmenovat entitu `EntityName1EntityName2`JOIN, která by v tomto případě byla. `CourseInstructor` Doporučujeme však zvolit název, který popisuje vztah. Modely dat začínají jednoduchým a roste a často se nepoužívají spojení bez datové části, které později načítá datovou část. Pokud začnete s popisným názvem entity, nebudete muset později změnit název. V ideálním případě by entita JOIN měla vlastní přirozený název (případně jeden Word) v obchodní doméně. Například knihy a zákazníci mohou být propojeni pomocí hodnocení. Pro tento vztah `CourseAssignment` je lepší volbou než `CourseInstructor`.

### <a name="composite-key"></a>Složený klíč

Vzhledem k tomu, že cizí klíče neumožňují hodnotu null a společně jednoznačně identifikují každý řádek tabulky, není nutné používat samostatný primární klíč. Vlastnosti *InstructorID* a *CourseID* by měly fungovat jako složený primární klíč. Jediným způsobem, jak identifikovat složené primární klíče k EF, je použití *rozhraní Fluent API* (nemůže být provedeno pomocí atributů). V další části se dozvíte, jak nakonfigurovat složený primární klíč.

Složený klíč zajišťuje, že i když můžete mít více řádků pro jeden kurz a více řádků pro jednoho instruktora, nemůžete mít pro stejný instruktor a kurz více řádků. Entita `Enrollment` JOIN definuje svůj vlastní primární klíč, takže je možné duplicity tohoto řazení provést. Aby tyto duplicity nedocházelo, mohli byste do polí cizího klíče přidat jedinečný index nebo nakonfigurovat `Enrollment` primární složený klíč podobný tomuto `CourseAssignment`:. Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Aktualizace kontextu databáze

Do souboru *data/SchoolContext. cs* přidejte následující zvýrazněný kód:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Tento kód přidá nové entity a nakonfiguruje složený primární klíč entity CourseAssignment.

## <a name="about-a-fluent-api-alternative"></a>O alternativní rozhraní API Fluent

Kód v `OnModelCreating` metodě `DbContext` třídy používá *rozhraní Fluent API* ke konfiguraci chování EF. Rozhraní API se nazývá "Fluent", protože se často používá k zřetězení řady volání metody do jednoho příkazu, jako v tomto příkladu v [dokumentaci EF Core](/ef/core/modeling/#use-fluent-api-to-configure-a-model):

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

V tomto kurzu používáte rozhraní Fluent API jenom pro mapování databáze, které nemůžete s atributy dělat. Rozhraní Fluent API ale můžete použít i k určení většiny pravidel formátování, ověřování a mapování, která můžete provádět pomocí atributů. Některé atributy, jako `MinimumLength` například, se nedají použít s rozhraním API Fluent. Jak bylo zmíněno `MinimumLength` dříve, nemění schéma, používá pouze pravidlo ověřování na straně klienta a serveru.

Někteří vývojáři dávají přednost použití rozhraní Fluent API, aby mohli zachovat třídy entit "vyčistit". V případě potřeby můžete kombinovat atributy a rozhraní API Fluent a existuje několik úprav, které je možné provést jenom pomocí rozhraní Fluent API, ale obecně doporučujeme zvolit jednu z těchto dvou přístupů a použít ji konzistentně co nejvíce. Pokud použijete obojí, mějte na paměti, že pokud dojde ke konfliktu, rozhraní Fluent API Přepisuje atributy.

Další informace o atributech vs. Fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagram entit znázorňující vztahy

Následující ilustrace znázorňuje diagram, který nástroje Entity Framework Power Tools vytvoří pro dokončený školní model.

![Diagram entit](complex-data-model/_static/diagram.png)

Kromě čar vztahů 1:1 (1 – n \*) se tady můžete podívat na řádek relace 1:1 (1 – 0.1) mezi entitami instruktor a OfficeAssignment a řádkem relace nula-a 1:1 (0.. 1 do *) mezi entitami instruktor a oddělení.

## <a name="seed-database-with-test-data"></a>Počáteční databáze s testovacími daty

Nahraďte kód v souboru *data/DbInitializer. cs* následujícím kódem, aby bylo možné poskytnout počáteční data pro nově vytvořené entity.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Jak jste viděli v prvním kurzu, většina tohoto kódu jednoduše vytvoří nové objekty entity a načte vzorová data do vlastností podle potřeby pro testování. Všimněte si, jak jsou zpracovávány relace m:n: kód vytvoří relace vytvořením entit v sadách entit `Enrollments` a. `CourseAssignment`

## <a name="add-a-migration"></a>Přidání migrace

Uložte změny a sestavte projekt. Pak otevřete příkazové okno ve složce projektu a zadejte `migrations add` příkaz (zatím neprovádějte příkaz Update-Database):

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

Zobrazí se upozornění na možnou ztrátu dat.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Pokud jste se v tomto okamžiku `database update` pokusili spustit příkaz (ještě to neuděláte), zobrazí se následující chyba:

> Příkaz ALTER TABLE je v konfliktu s omezením CIZÍho klíče FK_dbo. Course_dbo. Department_DepartmentID ". Ke konfliktu došlo v databázi "ContosoUniversity", tabulce "dbo". Oddělení ", sloupec" DepartmentID ".

Při provádění migrace s existujícími daty je někdy potřeba vložit do databáze zástupná data, aby bylo možné splnit omezení cizího klíče. Vygenerovaný kód v `Up` metodě přidá do tabulky Course DepartmentID cizí klíč, který nemůže mít hodnotu null. Pokud se v tabulce kurzu již nacházejí řádky, když je kód spuštěn, operace `AddColumn` se nezdařila, protože SQL Server neví, jaká hodnota má být vložena do sloupce, který nemůže mít hodnotu null. Pro účely tohoto kurzu spustíte migraci na nové databázi, ale v produkční aplikaci, kterou byste museli udělat, aby migrace zpracovala existující data, takže následující pokyny ukazují příklad toho, jak to udělat.

Pokud chcete, aby tato migrace fungovala se stávajícími daty, je nutné změnit kód tak, aby nový sloupec poskytoval výchozí hodnotu, a vytvořit oddělení zástupných procedur s názvem "Temp", které bude fungovat jako výchozí oddělení. V důsledku toho budou existující řádky kurzu až po spuštění metody v `Up` relaci s "dočasným" oddělením.

* Otevřete soubor *{timestamp} _ComplexDataModel. cs* .

* Odkomentujte řádek kódu, který přidá sloupec DepartmentID do tabulky Course.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Po kódu, který vytvoří tabulku oddělení, přidejte následující zvýrazněný kód:

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

V produkční aplikaci byste mohli napsat kód nebo skripty pro přidání řádků oddělení a související řádky kurzu pro nové řádky oddělení. Nebudete už potřebovat "dočasné" oddělení nebo výchozí hodnotu ve sloupci Course. DepartmentID.

Uložte změny a sestavte projekt.

## <a name="change-the-connection-string"></a>Změna připojovacího řetězce

Nyní máte nový kód ve `DbInitializer` třídě, který přidá počáteční data pro nové entity do prázdné databáze. Chcete-li, aby EF vytvořil novou prázdnou databázi, změňte název databáze v připojovacím řetězci v souboru *appSettings. JSON* na ContosoUniversity3 nebo na jiný název, který jste nepoužili na počítači, který používáte.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Uložte změnu do souboru *appSettings. JSON*.

> [!NOTE]
> Jako alternativu ke změně názvu databáze můžete databázi odstranit. Použijte **Průzkumník objektů systému SQL Server** (SSOX) nebo příkaz `database drop` CLI:
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>Aktualizace databáze

Poté, co jste změnili název databáze nebo odstranili databázi, spusťte `database update` příkaz v příkazovém okně a spusťte migrace.

```dotnetcli
dotnet ef database update
```

Spusťte aplikaci, aby se `DbInitializer.Initialize` metoda spustila a naplnila nová databáze.

Otevřete databázi v SSOX jako dříve a rozbalte uzel **tabulky** , abyste viděli, že se vytvořily všechny tabulky. (Pokud máte stále SSOX otevřený ze staršího času, klikněte na tlačítko **aktualizovat** .)

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

Spusťte aplikaci, aby aktivovala inicializační kód, který vysemena databáze.

Klikněte pravým tlačítkem na tabulku **CourseAssignment** a vyberte **Zobrazit data** a ověřte, zda obsahuje data.

![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>Získání kódu

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Přizpůsobení datového modelu
> * Provedli jsme změny entity studenta
> * Vytvořená entita instruktora
> * Vytvořila se entita OfficeAssignment
> * Upravená entita kurzu
> * Entita vytvořeného oddělení
> * Upravená entita registrace
> * Aktualizace kontextu databáze
> * Dosazení databáze s testovacími daty
> * Přidání migrace
> * Změna připojovacího řetězce
> * Aktualizace databáze

V dalším kurzu se dozvíte víc o tom, jak přistupovat k souvisejícím datům.

> [!div class="nextstepaction"]
> [Další: přístup k datům v relaci](read-related-data.md)
