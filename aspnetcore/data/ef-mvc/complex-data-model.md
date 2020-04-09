---
title: 'Kurz: Vytvoření komplexního datového modelu - ASP.NET MVC s EF Core'
description: V tomto kurzu přidejte další entity a vztahy a přizpůsobte datový model zadáním pravidel formátování, ověření a mapování.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 91fd09874ecab8bfdb6a38a404faba04aeb73edc
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657428"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>Kurz: Vytvoření komplexního datového modelu - ASP.NET MVC s EF Core

V předchozích kurzech jste pracovali s jednoduchým datovým modelem, který se skládal ze tří entit. V tomto kurzu přidáte další entity a vztahy a přizpůsobíte datový model zadáním pravidel formátování, ověření a mapování databáze.

Po dokončení budou třídy entit tvořit dokončený datový model, který je znázorněn na následujícím obrázku:

![Diagram entit](complex-data-model/_static/diagram.png)

V tomto kurzu jste:

> [!div class="checklist"]
> * Přizpůsobení datového modelu
> * Provádění změn v entitě Student
> * Entita Vytvořit instruktora
> * Entita Vytvořit officeassignment
> * Upravit entitu kurzu
> * Entita Vytvořit oddělení
> * Změnit entitu registrace
> * Aktualizace kontextu databáze
> * Databáze osiva s testovacími daty
> * Přidání migrace
> * Změna připojovacího řetězce
> * Aktualizace databáze

## <a name="prerequisites"></a>Požadavky

* [Použití migrace EF Core](migrations.md)

## <a name="customize-the-data-model"></a>Přizpůsobení datového modelu

V této části uvidíte, jak přizpůsobit datový model pomocí atributů, které určují pravidla formátování, ověřování a mapování databáze. Pak v několika z následujících částí vytvoříte kompletní školní datový model přidáním atributů do tříd, které jste již vytvořili, a vytvořením nových tříd pro zbývající typy entit v modelu.

### <a name="the-datatype-attribute"></a>Atribut DataType

U dat zápisu studenta se na všech webových stránkách aktuálně zobrazuje čas spolu s datem, ačkoli jediné, na čem se zajímáte pro toto pole, je datum. Pomocí atributů anotace dat můžete provést jednu změnu kódu, která opraví formát zobrazení v každém zobrazení, které zobrazuje data. Chcete-li zobrazit příklad, jak to udělat, přidáte `EnrollmentDate` atribut do `Student` vlastnosti ve třídě.

V *části Models/Student.cs*přidejte `using` příkaz pro obor `System.ComponentModel.DataAnnotations` názvů a přidejte `DataType` a `DisplayFormat` atributy do `EnrollmentDate` vlastnosti, jak je znázorněno v následujícím příkladu:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

Atribut `DataType` se používá k určení datového typu, který je konkrétnější než vnitřní typ databáze. V tomto případě chceme pouze sledovat datum, ne datum a čas. `DataType` Výčet poskytuje mnoho datových typů, jako je například datum, čas, telefonní číslo, měna, e-mailová adresa a další. Atribut `DataType` může také povolit aplikaci automaticky poskytovat funkce specifické pro daný typ. Lze například `mailto:` vytvořit odkaz `DataType.EmailAddress`pro aplikaci a `DataType.Date` v prohlížečích podporujících html5 lze zadat volič data. Atribut `DataType` vyzařuje atributy HTML 5 `data-` (vyslovuje se pomlčka dat), kterým prohlížeče HTML 5 rozumějí. Atributy `DataType` neposkytují žádné ověření.

`DataType.Date`neurčuje formát zobrazeného data. Ve výchozím nastavení je datové pole zobrazeno podle výchozích formátů založených na serveru CultureInfo.

Atribut `DisplayFormat` se používá k explicitnímu zadání formátu data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Toto `ApplyFormatInEditMode` nastavení určuje, že formátování by mělo být použito také při zobrazení hodnoty v textovém poli pro úpravy. (To to možná nebudete chtít u některých polí – například pro hodnoty měny, pravděpodobně nechcete, aby symbol měny v textovém poli pro úpravy.)

Atribut můžete `DisplayFormat` použít samostatně, ale obecně je vhodné použít `DataType` atribut také. Atribut `DataType` vyjadřuje sémantiku dat na rozdíl od způsobu jejich vykreslení na obrazovce a poskytuje následující výhody, které nezískáte s `DisplayFormat`:

* Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolměny odpovídající národnímu prostředí, e-mailové odkazy, některé ověření vstupu na straně klienta atd.).

* Ve výchozím nastavení prohlížeč vykreslí data ve správném formátu na základě národního prostředí.

Další informace naleznete v [ \<dokumentaci ke vstupní> značky .](../../mvc/views/working-with-forms.md#the-input-tag-helper)

Spusťte aplikaci, přejděte na stránku Studentindex a všimněte si, že časy se již nezobrazují pro data registrace. Totéž platí pro všechny pohledy, které používá model Student.

![Studenty index stránku zobrazující data bez časů](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Atribut StringLength

Můžete také zadat pravidla ověření dat a chybové zprávy ověření pomocí atributů. Atribut `StringLength` nastaví maximální délku v databázi a poskytuje ověření na straně klienta a serveru pro ASP.NET Core MVC. Můžete také zadat minimální délku řetězce v tomto atributu, ale minimální hodnota nemá žádný vliv na schéma databáze.

Předpokládejme, že chcete zajistit, aby uživatelé nezadali více než 50 znaků pro název. Chcete-li přidat `StringLength` toto omezení, přidejte atributy do vlastností `LastName` a, `FirstMidName` jak je znázorněno v následujícím příkladu:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Atribut `StringLength` nezabrání uživateli v zadání prázdného místa pro název. Atribut můžete `RegularExpression` použít omezení pro vstup. Například následující kód vyžaduje, aby první znak byl velkými písmeny a zbývající znaky byly abecední:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Atribut `MaxLength` poskytuje funkce podobné `StringLength` atributu, ale neposkytuje ověření na straně klienta.

Model databáze se nyní změnil způsobem, který vyžaduje změnu ve schématu databáze. Migrace budete používat k aktualizaci schématu bez ztráty dat, která jste přidali do databáze pomocí uj.

Uložte změny a vytvořte projekt. Potom otevřete příkazové okno ve složce projektu a zadejte následující příkazy:

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

Příkaz `migrations add` varuje, že může dojít ke ztrátě dat, protože změna zkracuje maximální délku pro dva sloupce.  Migrace vytvoří soubor s názvem * \<timeStamp>_MaxLengthOnNames.cs*. Tento soubor obsahuje `Up` kód v metodě, která bude aktualizovat databázi tak, aby odpovídala aktuálnímu datovému modelu. Příkaz `database update` spustil tento kód.

Předponou časového razítka k názvu souboru migrace framework používá k objednání migrace. Před spuštěním příkazu aktualizovat databázi můžete vytvořit více migrace a potom všechny migrace budou použity v pořadí, ve kterém byly vytvořeny.

Spusťte aplikaci, vyberte kartu **Studenti,** klikněte na **Vytvořit nový**a zkuste zadat jedno jméno delší než 50 znaků. Aplikace by vám měla zabránit v tom. 

### <a name="the-column-attribute"></a>Atribut Column

Atributy můžete také určit, jak jsou vaše třídy a vlastnosti mapovány do databáze. Předpokládejme, že `FirstMidName` jste použili název pole křestního jména, protože toto pole může také obsahovat prostřední jméno. Chcete však, aby byl `FirstName`sloupec databáze pojmenován , protože uživatelé, kteří budou psát dotazy ad hoc proti databázi, jsou na tento název zvyklí. Chcete-li toto mapování, `Column` můžete použít atribut.

Atribut `Column` určuje, že při vytvoření databáze bude `Student` pojmenován `FirstMidName` `FirstName`sloupec tabulky, která se mapuje na vlastnost . Jinými slovy, pokud váš `Student.FirstMidName`kód odkazuje na , data budou `FirstName` pocházet `Student` z nebo aktualizovat ve sloupci tabulky. Pokud nezadáte názvy sloupců, budou mít stejný název jako název vlastnosti.

V *souboru Student.cs* `using` přidejte `System.ComponentModel.DataAnnotations.Schema` příkaz pro `FirstMidName` vlastnost a přidejte atribut název sloupce, jak je znázorněno v následujícím zvýrazněném kódu:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Přidání atributu `Column` změní model, `SchoolContext`který podporuje , takže se nebude shodovat s databází.

Uložte změny a vytvořte projekt. Potom otevřete příkazové okno ve složce projektu a zadejte následující příkazy pro vytvoření další migrace:

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

V **Průzkumníku objektů serveru SQL Server**otevřete návrháře tabulek Student poklepáním na tabulku **Student.**

![Tabulka studentů ve SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

Před použitím prvních dvou migrací byly sloupce názvů typu nvarchar(MAX). Nyní jsou nvarchar(50) a název sloupce se změnil z FirstMidName na FirstName.

> [!Note]
> Pokud se pokusíte zkompilovat před dokončením vytváření všech tříd entit v následujících částech, může dojít k chybám kompilátoru.

## <a name="changes-to-student-entity"></a>Změny studentské entity

![Studentská entita](complex-data-model/_static/student-entity.png)

V *části Models/Student.cs*nahraďte kód, který jste přidali dříve, následujícím kódem. Změny jsou zvýrazněny.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Atribut Povinné

Atribut `Required` vytvoří pole vlastností názvu jako povinná. Atribut `Required` není potřeba pro typy s nulou, jako jsou typy hodnot (DateTime, int, double, float atd.). Typy, které nemohou být nulové, jsou automaticky považovány za povinná pole.

Atribut `Required` musí být `MinimumLength` použit `MinimumLength` s pro vynucení.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Atribut Zobrazit

Atribut `Display` určuje, že titulek pro textová pole by měl být "Jméno", "Příjmení", "Celé jméno" a "Datum zápisu" namísto názvu vlastnosti v každé instanci (která nemá prostor dělení slov).

### <a name="the-fullname-calculated-property"></a>Vypočtená vlastnost FullName

`FullName`je vypočtená vlastnost, která vrací hodnotu vytvořenou zřetězením dvou dalších vlastností. Proto má pouze get přistupujícího oru a žádný `FullName` sloupec bude generován v databázi.

## <a name="create-instructor-entity"></a>Entita Vytvořit instruktora

![Entita instruktora](complex-data-model/_static/instructor-entity.png)

Vytvořte *models/instructor.cs*a nahrazujte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Všimněte si, že několik vlastností jsou stejné v studentských a instruktor subjekty. V [kurzě implementace dědičnosti](inheritance.md) dále v této řadě refaktorujete tento kód, abyste eliminovali redundanci.

Na jeden řádek můžete umístit více atributů, `HireDate` takže můžete také zapsat atributy následujícím způsobem:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Vlastnosti navigace CourseAssignments a OfficeAssignment

`CourseAssignments` Vlastnosti `OfficeAssignment` a jsou navigační vlastnosti.

Instruktor může vyučovat `CourseAssignments` libovolný počet kurzů, takže je definován jako sbírka.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Pokud navigační vlastnost může obsahovat více entit, musí být jejím typem seznam, ve kterém lze položky přidávat, odstraňovat a aktualizovat.  Můžete zadat `ICollection<T>` nebo typ, `List<T>` `HashSet<T>`například nebo . Pokud zadáte `ICollection<T>`, EF `HashSet<T>` vytvoří kolekci ve výchozím nastavení.

Důvod, proč `CourseAssignment` se jedná o entity, je vysvětlen níže v části o vztazích N:N.

Obchodní pravidla Contoso University uvádějí, že instruktor může mít `OfficeAssignment` maximálně jednu kancelář, takže vlastnost obsahuje jednu entitu OfficeAssignment (která může být nulová, pokud není přiřazena žádná kancelář).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>Entita Vytvořit officeassignment

![Entita OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Vytvořit *modely/OfficeAssignment.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Atribut Key

Existuje vztah 1:0 nebo jeden mezi entitami Instruktor a OfficeAssignment. Přiřazení kanceláře existuje pouze ve vztahu k instruktorovi, ke kterým je přiřazen, a proto je jeho primárním klíčem také jeho cizí klíč k entitě Instruktor. Ale entity Framework nelze automaticky rozpoznat InstructorID jako primární klíč této entity, protože jeho název nedodržuje ID nebo classnameID konvence pojmenování. Proto `Key` atribut slouží k identifikaci jako klíč:

```csharp
[Key]
public int InstructorID { get; set; }
```

`Key` Atribut můžete také použít, pokud entita má svůj vlastní primární klíč, ale chcete pojmenovat vlastnost jinak než classnameID nebo ID.

Ve výchozím nastavení ef zachází s klíčem jako s nedatabasegenerovaným, protože sloupec je určen pro identifikační vztah.

### <a name="the-instructor-navigation-property"></a>Navigační vlastnost instruktora

Entita Instruktor má `OfficeAssignment` vlastnost navigace s možnou hodnotou null (protože instruktor nemusí mít `Instructor` přiřazení kanceláře) a entita OfficeAssignment má `InstructorID` navigační vlastnost s nenulovou hodnotou (protože přiřazení kanceláře nemůže existovat bez instruktora – je nenulové). Pokud má entita Instruktor související entitu OfficeAssignment, každá entita bude mít odkaz na druhou ve své navigační vlastnosti.

Můžete umístit `[Required]` atribut na instruktornavigační vlastnost určit, že musí být související instruktor, ale není `InstructorID` nutné provést, protože cizí klíč (který je také klíčem k této tabulce) je nenulovatelný.

## <a name="modify-course-entity"></a>Upravit entitu kurzu

![Entita kurzu](complex-data-model/_static/course-entity.png)

V *části Models/Course.cs*nahraďte kód, který jste přidali dříve, následujícím kódem. Změny jsou zvýrazněny.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Entita kurzu má `DepartmentID` vlastnost cizího klíče, která odkazuje `Department` na související entitu oddělení a má navigační vlastnost.

Entity Framework nevyžaduje přidání vlastnosti cizího klíče do datového modelu, pokud máte vlastnost navigace pro související entitu.  EF automaticky vytvoří cizí klíče v databázi všude tam, kde jsou potřeba, a vytvoří stínové [vlastnosti](/ef/core/modeling/shadow-properties) pro ně. Ale s cizí klíč v datovém modelu může aktualizace jednodušší a efektivnější. Například při načtení entity kurzu k úpravám, entity oddělení je null, pokud nechcete načíst, takže při aktualizaci entity kurzu, budete muset nejprve načíst entity oddělení. Pokud je vlastnost `DepartmentID` cizího klíče zahrnuta v datovém modelu, není nutné před aktualizací načítat entitu Oddělení.

### <a name="the-databasegenerated-attribute"></a>Atribut DatabaseGenerated

Atribut `DatabaseGenerated` s `None` parametrem `CourseID` na vlastnost určuje, že hodnoty primárního klíče jsou poskytovány uživatelem, nikoli generovány databází.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Ve výchozím nastavení entity Framework předpokládá, že hodnoty primárního klíče jsou generovány databází. To je to, co chcete ve většině scénářů. Pro entity kurzu však použijete uživatelem zadané číslo kurzu, například řadu 1000 pro jedno oddělení, řadu 2000 pro jiné oddělení a tak dále.

Atribut `DatabaseGenerated` lze také použít ke generování výchozích hodnot, jako v případě sloupců databáze používaných k zaznamenání data vytvoření nebo aktualizace řádku.  Další informace naleznete v tématu [Generated Properties](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti cizího klíče a navigační vlastnosti v entitě Course odrážejí následující vztahy:

Kurz je přiřazen k jednomu oddělení, `DepartmentID` takže z `Department` výše uvedených důvodů existuje cizí klíč a navigační vlastnost.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Kurz může mít libovolný počet studentů zapsaných `Enrollments` v něm, takže navigační vlastnost je kolekce:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurz může být vyučován `CourseAssignments` více instruktory, takže `CourseAssignment` navigační vlastnost je kolekce (typ je vysvětlen [později](#many-to-many-relationships)):

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Entita Vytvořit oddělení

![Subjekt oddělení](complex-data-model/_static/department-entity.png)

Vytvořit *modely/Department.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Atribut Column

Dříve jste `Column` atribut použili ke změně mapování názvů sloupců. V kódu entity Oddělení se `Column` atribut používá ke změně mapování datového typu SQL tak, aby byl sloupec definován pomocí typu peněz serveru SQL Server v databázi:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapování sloupců není obecně vyžadováno, protože entity Framework zvolí příslušný datový typ serveru SQL Server na základě typu CLR, který definujete pro vlastnost. Typ CLR `decimal` se mapuje `decimal` na typ serveru SQL Server. Ale v tomto případě víte, že sloupec bude držet částky měny a datový typ peněz je pro to vhodnější.

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti cizího klíče a navigace odrážejí následující vztahy:

Oddělení může nebo nemusí mít správce a správce je vždy instruktor. Proto `InstructorID` je vlastnost zahrnuta jako cizí klíč k entitě Instruktor `int` a otazník je přidán za označení typu k označení vlastnosti jako nullable. Vlastnost navigace je `Administrator` pojmenována, ale obsahuje entitu Instruktor:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Oddělení může mít mnoho kurzů, takže je navigační vlastnost Kurzů:

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Podle konvence entity framework umožňuje kaskádové odstranění pro cizí klíče s možnou neplatnou a pro vztahy N:N. To může mít za následek cyklické kaskády odstranit pravidla, která způsobí výjimku při pokusu o přidání migrace. Například pokud jste nedefinovali Department.InstructorID vlastnost jako nullable, EF by nakonfigurovat pravidlo kaskádového odstranění odstranit oddělení při odstranění instruktora, což není to, co chcete, aby se stalo. Pokud vaše obchodní `InstructorID` pravidla vyžadovala, aby vlastnost byla nenulovatelná, budete muset použít následující příkaz fluent API k zakázání kaskádového odstranění ve vztahu:
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Změnit entitu registrace

![Entita zápisu](complex-data-model/_static/enrollment-entity.png)

V *části Modely/Enrollment.cs*nahraďte kód, který jste přidali dříve, následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti cizího klíče a navigační vlastnosti odrážejí následující vztahy:

Záznam zápisu je pro jeden kurz, `CourseID` takže je cizí `Course` klíč vlastnost a navigační vlastnost:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Záznam zápisu je pro jednoho studenta, `StudentID` takže je `Student` cizí klíč vlastnost a navigační vlastnost:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Vztahy N:N

Existuje vztah N:N mezi entitami Student a Kurz a entita Zápis funguje jako tabulka spojení N:N *s datovou částí* v databázi. "S datovou částí" znamená, že tabulka Registrace obsahuje další data kromě cizích klíčů pro spojené tabulky (v tomto případě primární klíč a vlastnost Grade).

Následující obrázek znázorňuje, jak tyto vztahy vypadají v diagramu entity. (Tento diagram byl vygenerován pomocí entity framework elektrické nástroje pro EF 6.x; vytvoření diagramu není součástí kurzu, je to jen používá zde jako ilustrace.)

![Student-Kurz mnoho k mnoha vztahům](complex-data-model/_static/student-course.png)

Každá čára vztahu má 1 na jednom konci a hvězdičku (*) na straně druhé, označující vztah 1:N.

Pokud tabulka Zápis neobsahuje informace o hodnocení, musí obsahovat pouze dva cizí klíče CourseID a StudentID. V takovém případě by se spojit tabulka N:N bez datové části (nebo čisté tabulky spojení) v databázi. Entity Instruktora a kurzu mají tento druh relace N:N a dalším krokem je vytvoření třídy entity, která bude fungovat jako tabulka spojení bez datové části.

(EF 6.x podporuje implicitní spojit tabulky pro relace N:N, ale EF Core není. Další informace naleznete [v diskusi v úložišti EF Core GitHub](https://github.com/aspnet/EntityFramework/issues/1368).)

## <a name="the-courseassignment-entity"></a>Entita CourseAssignment

![Entita CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Vytvořit *modely/CourseAssignment.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Spojit názvy entit

Tabulka spojení je vyžadována v databázi pro vztah Instruktor-kurzy N:N a musí být reprezentována sadou entit. Je běžné pojmenovat entitu `EntityName1EntityName2`spojení , která `CourseInstructor`by v tomto případě byla . Doporučujeme však zvolit název, který popisuje vztah. Datové modely začínají jednoduché a rostou, s no-datové části připojí často získat datové části později. Pokud začnete s popisným názvem entity, nebudete muset později název měnit. V ideálním případě by entita spojení měla svůj vlastní přirozený název (případně jedno slovo) v obchodní doméně. Knihy a zákazníci mohou být například propojeni prostřednictvím hodnocení. Pro tento `CourseAssignment` vztah, je `CourseInstructor`lepší volbou než .

### <a name="composite-key"></a>Složený klíč

Vzhledem k tomu, že cizí klíče nelze utajet s platností a společně jednoznačně identifikují každý řádek tabulky, není nutné samostatný primární klíč. Vlastnosti *InstructorID* a *CourseID* by měly fungovat jako složený primární klíč. Jediný způsob, jak identifikovat složené primární klíče ef je pomocí *fluent API* (nelze provést pomocí atributů). V další části uvidíte, jak nakonfigurovat složený primární klíč.

Složený klíč zajišťuje, že zatímco můžete mít více řádků pro jeden kurz a více řádků pro jednoho instruktora, nemůžete mít více řádků pro stejného instruktora a kurz. Entita `Enrollment` spojení definuje svůj vlastní primární klíč, takže jsou možné duplikáty tohoto druhu. Chcete-li těmto duplikátům zabránit, můžete do `Enrollment` polí cizího klíče `CourseAssignment`přidat jedinečný index nebo nakonfigurovat pomocí primárního složeného klíče podobného . Další informace naleznete v tématu [Indexy](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Aktualizace kontextu databáze

Do souboru *Data/SchoolContext.cs* přidejte následující zvýrazněný kód:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Tento kód přidá nové entity a nakonfiguruje složený primární klíč entity CourseAssignment.

## <a name="about-a-fluent-api-alternative"></a>O plynulé alternativě rozhraní API

Kód v `OnModelCreating` metodě `DbContext` třídy používá *plynulé rozhraní API* ke konfiguraci chování EF. Rozhraní API se nazývá "plynulý", protože se často používá navlékáním řady volání metod do jednoho příkazu, jako v tomto příkladu z [dokumentace EF Core](/ef/core/modeling/#use-fluent-api-to-configure-a-model):

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

V tomto kurzu používáte plynulé rozhraní API pouze pro mapování databáze, které nelze provést s atributy. Pomocí rozhraní FLUENT API však můžete také určit většinu pravidel formátování, ověřování a mapování, která můžete provést pomocí atributů. Některé atributy, `MinimumLength` jako je například nelze použít s plynulý rozhraní API. Jak již bylo `MinimumLength` zmíněno dříve, nezmění schéma, použije pouze ověřovací pravidlo na straně klienta a serveru.

Někteří vývojáři dávají přednost použití rozhraní FLUENT API výhradně tak, aby mohli zachovat své třídy entit "čisté". Můžete kombinovat atributy a plynulé rozhraní API, pokud chcete, a existuje několik úprav, které lze provést pouze pomocí fluent API, ale obecně doporučujeme zvolit jeden z těchto dvou přístupů a používat konzistentně co nejvíce. Pokud používáte obojí, všimněte si, že všude tam, kde dojde ke konfliktu, přepíše atributy Fluent API.

Další informace o atributech vs. fluent API naleznete v [tématu Metody konfigurace](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagram entit znázorňující vztahy

Následující obrázek znázorňuje diagram, který vytvoří nástroje Entity Framework Power Tools pro dokončený školní model.

![Diagram entit](complex-data-model/_static/diagram.png)

Kromě linek vztahu 1:N (1 až \*), zde můžete vidět řádek vztahu 1:0 nebo jeden (1 až 0..1) mezi entitami Instruktor a OfficeAssignment a řádek vztahu nula nebo jeden na mnoho (0..1 až *) mezi entitami Instruktor a Oddělení.

## <a name="seed-database-with-test-data"></a>Databáze osiva s testovacími daty

Nahraďte kód v souboru *Data/DbInitializer.cs* následujícím kódem, abyste poskytli počáteční data pro nové entity, které jste vytvořili.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Jak jste viděli v prvním kurzu, většina tohoto kódu jednoduše vytvoří nové entity objekty a načte ukázková data do vlastností, jak je požadováno pro testování. Všimněte si, jak jsou zpracovány relace N:N: kód `Enrollments` vytváří `CourseAssignment` vztahy vytvořením entit v sadách entit a spojit je.

## <a name="add-a-migration"></a>Přidání migrace

Uložte změny a vytvořte projekt. Potom otevřete příkazové okno ve `migrations add` složce projektu a zadejte příkaz (zatím neprovázte příkaz aktualizovat databázi):

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

Dostanete upozornění na možnou ztrátu dat.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Pokud jste se `database update` pokusili spustit příkaz v tomto okamžiku (ještě to nedělají), zobrazí se následující chyba:

> Příkaz ALTER TABLE je v konfliktu s omezením "FK_dbo cizí klíč. Course_dbo. Department_DepartmentID". Ke konfliktu došlo v databázi "ContosoUniversity", tabulka "dbo. Department", sloupec "DepartmentID".

Někdy při provádění migrace s existujícími daty je třeba vložit data se zakázaným inzerováním do databáze, aby byla splněna omezení cizího klíče. Generovaný kód `Up` v metodě přidá cizí klíč DepartmentID, který není možné udát, do tabulky Kurz. Pokud již existují řádky v tabulce kurz při `AddColumn` spuštění kódu, operace se nezdaří, protože SQL Server neví, jakou hodnotu umístit do sloupce, který nemůže být null. Pro účely tohoto kurzu spustíte migraci v nové databázi, ale v produkční aplikaci budete muset provést migraci zpracovat existující data, takže následující pokyny ukazují příklad, jak to udělat.

Chcete-li, aby tato migrace fungovala s existujícími daty, musíte změnit kód, aby nový sloupec měl výchozí hodnotu, a vytvořit oddělení se zakázaným inzerováním s názvem "Temp", které bude fungovat jako výchozí oddělení. V důsledku toho existující řádky kurzu budou všechny souviset `Up` s oddělením "Temp" po spuštění metody.

* Otevřete soubor *{timestamp}_ComplexDataModel.cs.*

* Zakomentujte řádek kódu, který přidá sloupec DepartmentID do tabulky Kurz.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Za kód, který vytvoří tabulku Oddělení, přidejte následující zvýrazněný kód:

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

V produkční aplikaci byste napsali kód nebo skripty pro přidání řádků oddělení a propojovali řádky kurzu s novými řádky oddělení. Potom byste již nepotřebovali oddělení "Temp" nebo výchozí hodnotu ve sloupci Course.DepartmentID.

Uložte změny a vytvořte projekt.

## <a name="change-the-connection-string"></a>Změna připojovacího řetězce

Nyní máte nový kód `DbInitializer` ve třídě, která přidá data osiva pro nové entity do prázdné databáze. Chcete-li ef vytvořit novou prázdnou databázi, změňte název databáze v připojovacím řetězci v *appsettings.json* na ContosoUniversity3 nebo jiný název, který jste nepoužili v počítači, který používáte.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Uložte změnu na *appsettings.json*.

> [!NOTE]
> Jako alternativu ke změně názvu databáze můžete databázi odstranit. Použijte **Průzkumník objektů SERVERU SQL** Server `database drop` (SSOX) nebo příkaz cli:
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>Aktualizace databáze

Po změně názvu databáze nebo odstranění databáze `database update` spusťte příkaz v příkazovém okně a proveďte migrace.

```dotnetcli
dotnet ef database update
```

Spusťte aplikaci `DbInitializer.Initialize` způsobit metodu ke spuštění a naplnění nové databáze.

Otevřete databázi ve SSOX stejně jako dříve a rozbalte uzel **Tabulky,** abyste viděli, že byly vytvořeny všechny tabulky. (Pokud máte stále ssox otevřený z předchozího času, klepněte na tlačítko **Aktualizovat.)**

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

Spusťte aplikaci a aktivujte kód inicializátoru, který zasílá databázi.

Klikněte pravým tlačítkem myši na tabulku **CourseAssignment** a výběrem **možnosti Zobrazit data** ověřte, zda v ní jsou data.

![CourseAssignment data v SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>Získání kódu

[Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Přizpůsobené datového modelu
> * Provedené změny v entitě Student
> * Entita Vytvořeného instruktora
> * Entita Vytvořeno OfficeAssignment
> * Modifikovaná entita kurzu
> * Entita Vytvořeného oddělení
> * Entita Upravené registrace
> * Byl aktualizován kontext databáze.
> * Nasevovaná databáze s testovacími daty
> * Přidána migrace
> * Změna připojovacího řetězce
> * Byla aktualizována databáze

Přejdete k dalšímu kurzu, kde se dozvíte více o tom, jak získat přístup k souvisejícím datům.

> [!div class="nextstepaction"]
> [Další: Přístup k souvisejícím datům](read-related-data.md)
