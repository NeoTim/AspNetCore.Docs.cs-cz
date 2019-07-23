---
title: 'Kurz: Vytvoření složitého datového modelu – ASP.NET MVC s EF Core'
description: V tomto kurzu přidat další entity a relace a přizpůsobte si datový model zadáním formátování, ověřování a pravidel mapování.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 8ef458108e60850d0d54e7b1e6472c5b92984ccc
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813546"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>Kurz: Vytvoření složitého datového modelu – ASP.NET MVC s EF Core

V předchozích kurzech jste pracovali s jednoduchý datový model, který se skládá z tři entity. V tomto kurzu přidáte další entity a relace a tak, že zadáte formátování, ověřování a pravidel mapování database budete Přizpůsobte si datový model.

Jakmile budete hotovi, tříd entit budou použity k vytvoření dokončeného datový model, který je znázorněno na následujícím obrázku:

![Entity diagram](complex-data-model/_static/diagram.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přizpůsobte si datový model
> * Provádět změny entity studenta
> * Vytvoření entity instruktorem
> * Vytvoření OfficeAssignment entity
> * Upravit entity kurzu
> * Vytvoření entity oddělení
> * Upravit entity registrace
> * Aktualizace kontext databáze
> * Počáteční hodnota databáze s testovací data
> * Přidejte migraci
> * Změňte připojovací řetězec
> * Aktualizace databáze

## <a name="prerequisites"></a>Požadavky

* [Pomocí migrace EF Core](migrations.md)

## <a name="customize-the-data-model"></a>Přizpůsobte si datový model

V této části uvidíte, jak přizpůsobit datového modelu s použitím atributů, které určují databáze mapování pravidel ověřování a formátování. Potom v některé z následujících částí, které si vytvoříte kompletní datový model školní přidáním atributů třídy jste již vytvořili a vytvářet nové třídy pro zbývající typy entit v modelu.

### <a name="the-datatype-attribute"></a>Datový typ atributu

Student zápisu dat všechny webové stránky aktuálně zobrazit čas spolu s datem, i když je všechno, co vás zajímá pro toto pole datum. S použitím anotace atributů dat, můžete si ho kódu změnu, která budou v každé zobrazení, která zobrazuje data opravit formát zobrazení. Chcete-li zobrazit příklad tohoto postupu, že přidáte atribut, který má `EnrollmentDate` vlastnost `Student` třídy.

V *Models/Student.cs*, přidejte `using` příkaz pro `System.ComponentModel.DataAnnotations` obor názvů a přidejte `DataType` a `DisplayFormat` atributů `EnrollmentDate` vlastnost, jak je znázorněno v následujícím příkladu:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

`DataType` Atribut se používá k určení datový typ, který je specifičtější než vnitřní typ databáze. V tomto případě chceme jenom udržovat přehled o datum, nejsou data a času. `DataType` Výčet poskytuje pro mnoho typů dat, jako je například datum, čas, telefonní číslo, Měna, EmailAddress a další. `DataType` Atribut můžete také povolit aplikace a zajistit tak automaticky specifické pro typ funkce. Například `mailto:` propojení lze vytvořit pro `DataType.EmailAddress`, a selektor date lze zadat pro `DataType.Date` v prohlížečích podporujících HTML5. `DataType` Atribut vysílá HTML 5 `data-` (čteno data dash) atributy, které umožní pochopit prohlížeče HTML 5. `DataType` Atributy neposkytují žádné ověřování.

`DataType.Date` neurčuje formátu, který se zobrazí datum. Ve výchozím nastavení zobrazí se pole data podle výchozí formát založený na serveru CultureInfo.

`DisplayFormat` Atribut se používá s ohledem na formát data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode` Nastavení určuje, že formátování také bude použito při hodnota se zobrazí v textovém poli pro úpravu. (Není vhodné pro některá pole – například pro hodnoty měny, nemusí požadované symbol měny v textovém poli pro úpravu.)

Můžete použít `DisplayFormat` atribut samotný, ale to je obecně vhodné použít `DataType` také atribut. `DataType` Atribut přenáší sémantiku dat na rozdíl od vykreslování na obrazovce a nabízí následující výhody, které vám s `DisplayFormat`:

* HTML5 funkce můžete povolit v prohlížeči (například zobrazení ovládacího prvku kalendář, symbol měny odpovídající národní prostředí, odkazy na e-mailu, některé klientské vstupní ověřování atd.).

* Ve výchozím prohlížeči bude vykreslovat data ve správném formátu podle vašeho národního prostředí.

Další informace najdete v tématu [ \<vstupní > dokumentace pomocné rutiny značky](../../mvc/views/working-with-forms.md#the-input-tag-helper).

Spusťte aplikaci, přejděte na stránku Index studenty a Všimněte si, že časy se už nezobrazují pro data registrace. Stejné bude platit pro všechna zobrazení, která používá model studentů.

![Studenti indexová stránka zobrazující data bez časy](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Atribut StringLength

Můžete také zadat data ověřovací pravidla a chybových zpráv ověření pomocí atributů. `StringLength` Atribut Nastaví maximální počet znaků v databázi a na straně klienta a na straně serveru ověřování pro ASP.NET Core MVC. Minimální délka řetězce. můžete také zadat v tomto atributu, ale minimální hodnota nemá žádný vliv na schéma databáze.

Předpokládejme, že chcete mít jistotu, že uživatelé nezadávejte více než 50 znaků pro název. Chcete-li přidat toto omezení, přidejte `StringLength` atributů `LastName` a `FirstMidName` vlastnosti, jak je znázorněno v následujícím příkladu:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

`StringLength` Atribut nebude zabránit uživateli v zadávání prázdné znaky pro název. Můžete použít `RegularExpression` atributu použít omezení na vstup. Například následující kód vyžaduje prvního znaku na velká písmena a zbývající znaky, které mají být abecední znak:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

`MaxLength` Atribut poskytuje podobné funkce `StringLength` atribut, ale neposkytuje na straně klienta ověření.

Model databáze se změnilo tak, aby vyžaduje změnu schématu databáze. Migrace budete používat k aktualizaci schématu bez ztráty dat, která jste mohli přidat do databáze s použitím uživatelského rozhraní aplikace.

Uložte změny a sestavte projekt. Potom otevřete okno příkazového řádku ve složce projektu a zadejte následující příkazy:

```console
dotnet ef migrations add MaxLengthOnNames
```

```console
dotnet ef database update
```

`migrations add` Příkaz vás upozorní, že může dojít ke ztrátě dat, protože díky této změně maximální délku kratší pro dva sloupce.  Migrace vytvoří soubor s názvem  *\<časové razítko > _MaxLengthOnNames.cs*. Tento soubor obsahuje kód v `Up` metodu, která aktualizuje databázi tak, aby odpovídaly aktuálním datovým modelem. `database update` Tento kód byl spuštěn příkaz.

Časové razítko, před kterou je připojený k názvu souboru migrace používá Entity Framework pro řazení migrace. Můžete vytvořit více migrace před spuštěním příkazu update databáze a pak všechny migrace se použijí v pořadí, ve kterém byly vytvořeny.

Spusťte aplikaci, vyberte **studenty** klikněte na tlačítko **vytvořit nový**a pokuste se zadat buď název delší než 50 znaků. Aplikace by měla by vám bránily v to. 

### <a name="the-column-attribute"></a>Atribut sloupce

Atributy můžete také řídit, jak třídy a vlastnosti jsou namapovány na databázi. Předpokládejme, že byste použili název `FirstMidName` pro název prvního pole, protože pole může obsahovat také křestní jméno. Chcete sloupec databáze s názvem, ale `FirstName`, protože uživatelé budou psát ad-hoc dotazy na databázi jsou zvyklí na tento název. Chcete-li toto mapování, můžete použít `Column` atribut.

`Column` Atribut určuje, že když se vytvoří databáze, sloupci `Student` tabulku, která se mapuje `FirstMidName` bude mít název vlastnosti `FirstName`. Jinými slovy, pokud váš kód odkazuje na `Student.FirstMidName`, data budou přicházet z nebo v aktualizovat `FirstName` sloupec `Student` tabulky. Pokud nechcete zadat názvy sloupců, že mu udělená stejný název jako název vlastnosti.

V *Student.cs* přidejte `using` příkaz pro `System.ComponentModel.DataAnnotations.Schema` a přidat sloupec název atributu `FirstMidName` vlastnost, jak je znázorněno v následující zvýrazněný kód:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Přidání `Column` atribut změní základní model `SchoolContext`, takže nebude odpovídat databázi.

Uložte změny a sestavte projekt. Potom otevřete okno příkazového řádku ve složce projektu a zadejte následující příkazy vytvoří další migraci:

```console
dotnet ef migrations add ColumnFirstName
```

```console
dotnet ef database update
```

V **Průzkumník objektů systému SQL Server**, otevřete Návrhář tabulky Student dvojitým kliknutím **Student** tabulky.

![Tabulky Studenti v SSOX po migraci](complex-data-model/_static/ssox-after-migration.png)

Před použitím první dvě migrace název sloupce se mají z typu nvarchar(MAX). Jsou to teď nvarchar(50) a název sloupce byl změněn z FirstMidName na jméno.

> [!Note]
> Pokud se pokusíte zkompilovat před dokončení vytváření všech tříd entit v následujících částech, může docházet k chybám kompilátoru.

## <a name="changes-to-student-entity"></a>Změny entity studenta

![Student entity](complex-data-model/_static/student-entity.png)

V *Models/Student.cs*, nahraďte kód, který jste přidali dříve následujícím kódem. Změny jsou zvýrazněné.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Požadovaný atribut

`Required` Atribut je povinná pole název vlastnosti. `Required` Atribut není potřeba pro typy neumožňující hodnotu, jako jsou typy hodnot (datum a čas, int, dvakrát, float, atd.). Typy, které nemůže mít hodnotu null se automaticky považují za povinná pole.

Můžete odebrat `Required` atribut a nahraďte ji metodou minimální délku parametru `StringLength` atribut:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Atribut zobrazení

`Display` Atribut určuje, že titulek pro textová pole by měl být "Jméno", "Last Name", "Jméno" a "Datum registrace" namísto názvu vlastnosti v každé instanci (která nemá místo dělení slov).

### <a name="the-fullname-calculated-property"></a>Celý název počítané vlastnosti

`FullName` je počítaná vlastnost, která vrací hodnotu, která se vytváří zřetězením dvou dalších vlastností. Proto má pouze přístupový objekt get a ne `FullName` vygeneruje sloupec v databázi.

## <a name="create-instructor-entity"></a>Vytvoření entity instruktorem

![Entita instruktorem](complex-data-model/_static/instructor-entity.png)

Vytvoření *Models/Instructor.cs*, nahraďte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Všimněte si, že několik vlastnosti jsou stejné v entitách studenty a instruktorem. V [implementace dědičnosti](inheritance.md) kurz později v této sérii, budete Refaktorovat tento kód eliminovat redundance.

Na jednom řádku, můžete umístit více atributů, tak by mohl taky zapisovat `HireDate` atributy následujícím způsobem:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Navigační vlastnosti CourseAssignments a OfficeAssignment

`CourseAssignments` a `OfficeAssignment` jsou navigační vlastnosti.

Instruktorem můžete naučit libovolný počet kurzů, takže `CourseAssignments` je definovaná jako kolekce.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Pokud vlastnost navigace může obsahovat více entit, jeho typ musí být seznam, ve kterém položky lze přidávat, odstranit a aktualizovat.  Můžete zadat `ICollection<T>` nebo typu jako `List<T>` nebo `HashSet<T>`. Pokud zadáte `ICollection<T>`, vytvoří EF `HashSet<T>` kolekcí ve výchozím nastavení.

Důvod, proč jsou `CourseAssignment` entity je popsán níže v části o relacích many-to-many.

Obchodní pravidla contoso University stavu instruktorem může mít pouze nejvýše jeden office, takže `OfficeAssignment` vlastnost obsahuje jednu entitu OfficeAssignment (který může mít hodnotu null, pokud není přiřazena žádná office).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>Vytvoření OfficeAssignment entity

![OfficeAssignment entity](complex-data-model/_static/officeassignment-entity.png)

Vytvoření *Models/OfficeAssignment.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Atribut Key

Existuje vztah jeden: nula nebo 1 až kurzů vedených OfficeAssignment entity. Přiřazení office existuje pouze ve vztahu k instruktorem, který je přiřazen k a proto jeho primární klíč se také jeho cizí klíč do kurzů vedených entity. Ale Entity Framework nemůže rozpoznat automaticky InstructorID jako primární klíč tuto entitu protože jeho název není podle ID nebo classnameID konvence. Proto `Key` atribut se používá k jeho identifikaci jako klíč:

```csharp
[Key]
public int InstructorID { get; set; }
```

Můžete také použít `Key` atribut Pokud entita nemá primární klíč, ale budete chtít název vlastnosti něco jiného než classnameID nebo ID.

Ve výchozím nastavení EF považuje za klíč bez databáze vygenerovala protože sloupec je pro identifikující relaci.

### <a name="the-instructor-navigation-property"></a>Navigační vlastnost instruktorem

Kurzů vedených entita má s povolenou hodnotou Null `OfficeAssignment` navigační vlastnosti (protože instruktorem nemusí mít přiřazení office), a OfficeAssignment entita má zakázanou `Instructor` navigační vlastnosti (protože nelze přiřazení office existovat bez instruktorem – `InstructorID` je null). Pokud má entita kurzů vedených související entita OfficeAssignment, každá entita bude mít odkaz na druhou v jeho navigační vlastnost.

Můžete umístit `[Required]` atribut u vlastnosti navigace kurzů vedených a určit tak, že musí být související instruktorem, ale nemáte to provést, protože `InstructorID` cizí klíč (což je také klíč do této tabulky) je null.

## <a name="modify-course-entity"></a>Upravit entity kurzu

![Kurz entity](complex-data-model/_static/course-entity.png)

V *Models/Course.cs*, nahraďte kód, který jste přidali dříve následujícím kódem. Změny jsou zvýrazněné.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Kurz entita má vlastnost cizího klíče `DepartmentID` který odkazuje na související entity oddělení a má `Department` navigační vlastnost.

Entity Framework nevyžaduje, můžete přidat vlastnost cizího klíče do datového modelu, když máte navigační vlastnost pro související entity.  EF automaticky vytvoří cizí klíče v databázi bez ohledu na to budete potřebovat a vytvoří [stínové vlastnosti](/ef/core/modeling/shadow-properties) pro ně. Ale s cizího klíče v datovém modelu může být aktualizace jednodušší a efektivnější. Například při načtení entity kurzu upravit entity oddělení má hodnotu null Pokud načtete nemusíte ho, proto při aktualizaci entity kurzu budete mít se nejdřív načíst entity oddělení. Pokud vlastnost cizího klíče `DepartmentID` je zahrnuta v datovém modelu, není nutné k načtení entity oddělení před aktualizací.

### <a name="the-databasegenerated-attribute"></a>Atribut DatabaseGenerated

`DatabaseGenerated` Atributem `None` parametru u `CourseID` vlastnost určuje, že hodnoty primárního klíče jsou uživatelem zadané místo generován databází.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Ve výchozím nastavení Entity Framework předpokládá, že hodnoty primárního klíče je generován databází. Který se má ve většině scénářů. Pro entity kurzu, budete však použít číslo uživatel zadal kurzu například řadu 1000 pro jedno oddělení, řadu 2000 pro jiného oddělení a tak dále.

`DatabaseGenerated` Atribut lze také generovat výchozí hodnoty, jako v případě sloupců databáze slouží k záznamu datum řádek byl vytvořen nebo aktualizován.  Další informace najdete v tématu [vygenerovaným vlastnostem](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti cizího klíče a navigačních vlastností v entitě kurzu zahrnují následující vztahy:

Kurz je přiřazena jednoho oddělení, takže `DepartmentID` cizího klíče a `Department` navigační vlastnost z důvodů uvedených výše.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Kurz můžete mít libovolný počet studentů zaregistrované, takže `Enrollments` navigační vlastnost je kolekce:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurz může být vedená instruktorů více, proto `CourseAssignments` navigační vlastnost je kolekce (typ `CourseAssignment` je vysvětleno [později](#many-to-many-relationships)):

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Vytvoření entity oddělení

![Oddělení entity](complex-data-model/_static/department-entity.png)

Vytvoření *Models/Department.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Atribut sloupce

Dříve jste použili `Column` atribut, chcete-li změnit název mapování sloupců. V kódu pro entitu oddělení `Column` atribut se používá, chcete-li změnit SQL mapování datového typu tak, aby sloupec bude definován pomocí typ money systému SQL Server v databázi:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Mapování sloupců se obecně nevyžaduje, protože rozhraní Entity Framework vybere odpovídající datový typ SQL serveru na základě typu CLR, které definujete pro vlastnost. Modul CLR `decimal` zadejte mapuje se na serveru SQL Server `decimal` typu. Ale v tomto případě víte, že sloupec se drží peněžních hodnot, a je vhodnější pro tento datový typ money.

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti cizího klíče a navigace zahrnují následující vztahy:

Oddělení může nebo nemusí mít správce a správce je vždy instruktorem. Proto `InstructorID` vlastnost je součástí jako cizí klíč k entitě instruktorem a přidá se otazník za `int` označení označit vlastnosti jako datový typ s možnou hodnotou Null typu. Navigační vlastnost jmenuje `Administrator` ale obsahuje entitu kurzů vedených:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Oddělení může mít mnoho kurzů, tedy navigační vlastnost kurzy:

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Podle konvence rozhraní Entity Framework umožňuje kaskádové odstranění pro Null cizí klíče a vztahy many-to-many. To může způsobit Cyklické kaskádové odstranění pravidla, která způsobí výjimku při pokusu o přidání migrace. Například pokud definujete neměli vlastnost Department.InstructorID jako s možnou hodnotou Null, EF by nakonfigurovat pravidlo cascade delete můžete odstranit oddělení, když odstraníte instruktorem, které se mají se stane. V případě potřeby obchodních pravidel `InstructorID` vlastnosti být null, je třeba použít následující příkaz rozhraní API fluent zakázat kaskádové odstranění v relaci:
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Upravit entity registrace

![Registrace entity](complex-data-model/_static/enrollment-entity.png)

V *Models/Enrollment.cs*, nahraďte kód, který jste přidali dříve následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Vlastnosti cizího klíče a navigace

Vlastnosti cizího klíče a navigačních vlastností zahrnují následující vztahy:

Záznam registrace je jeden kurzům, tedy `CourseID` vlastnost cizího klíče a `Course` navigační vlastnost:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Záznam registrace je pro jeden student, tedy `StudentID` vlastnost cizího klíče a `Student` navigační vlastnost:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relace m: N

Existuje many-to-many vztah mezi entitami studenty a kurzu a entitou registrace funguje jako tabulka many-to-many spojení *s datovou částí* v databázi. "S datovou částí" znamená, že registrace tabulka obsahuje další údaje kromě cizí klíče pro spojené tabulky (v tomto případě primární klíč a vlastnost na podnikové úrovni).

Následující obrázek znázorňuje, jak tyto vztahy vypadat v diagramu entity. (Tento diagram se vygeneroval pomocí Entity Framework Power Tools pro EF 6.x; vytvoření diagramu, které nejsou součástí tohoto kurzu, je právě používán jako ilustraci zde.)

![Kurz student mnoho na mnoho vztah](complex-data-model/_static/student-course.png)

Každý řádek vztah má 1 na jednom konci a hvězdičku (*) na druhém, určující vztah jeden mnoho.

Pokud v tabulce registrace nezahrnuli informace na podnikové úrovni, bude pouze nutné tak, aby obsahovala dva cizího klíče CourseID a StudentID. V takovém případě by bylo tabulku spojení many-to-many bez datové části (nebo čistě spojení tabulku) v databázi. Entity instruktorem a kurzu mají tento druh vztahu many-to-many a dalším krokem je vytvoření třídu entity jako tabulku spojení bez datové části.

(EF 6.x podporuje implicitní spojení tabulek pro vztahy many-to-many, ale EF Core nepodporuje. Další informace najdete v tématu [diskuze v úložišti EF Core GitHub](https://github.com/aspnet/EntityFramework/issues/1368).)

## <a name="the-courseassignment-entity"></a>CourseAssignment entity

![CourseAssignment entity](complex-data-model/_static/courseassignment-entity.png)

Vytvoření *Models/CourseAssignment.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Připojte se k názvy entit

V databázi pro vztah many-to-many kurzů vedených kurzy se vyžaduje tabulku spojení a musí být reprezentována sadu entit. Je běžné název entity spojení `EntityName1EntityName2`, který v tomto případě bude `CourseInstructor`. Doporučujeme však, že vyberete název, který popisuje relace. Datové modely začíná jednoduchou i při pozdějším růstu s LINQ pomocí spojení ne datové části často získání datových částí později. Pokud byste začali s entity popisný název, nebudete muset později změnit název. Spojení entit v ideálním případě by mít svůj vlastní přirozené název (může být jediné slovo) v obchodní domény. Například může knihy a zákazníci spojený prostřednictvím hodnocení. Pro tento vztah `CourseAssignment` je vhodnější než `CourseInstructor`.

### <a name="composite-key"></a>Složený klíč

Protože cizí klíče nejsou s možnou hodnotou Null a dohromady jedinečně identifikují každý řádek v tabulce, není nutné pro samostatný primární klíč. *InstructorID* a *CourseID* vlastnosti by měla fungovat jako složený primární klíč. Jediný způsob, jak identifikovat složené primárního klíče na EF je použít *rozhraní fluent API* (ho nelze provést s použitím atributů). Uvidíte jak nakonfigurovat složený primární klíč v další části.

Složený klíč zajistí, že i když můžete mít více řádků pro jeden kurz a více řádků pro jeden instruktorem, nemůže mít více řádků pro stejnou instruktorem a kurzu. `Enrollment` Spojení entita definuje vlastní primární klíč tak, aby byly možné duplicity toto řazení. Aby se tyto duplikáty, mohou přidat jedinečný index na pole cizích klíčů nebo nakonfigurovat `Enrollment` s primární složený klíč podobný `CourseAssignment`. Další informace najdete v tématu [indexy](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Aktualizace kontext databáze

Přidejte následující zvýrazněný kód do *Data/SchoolContext.cs* souboru:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Tento kód přidá nové entity a nakonfiguruje CourseAssignment entita složený primární klíč.

## <a name="about-a-fluent-api-alternative"></a>O fluent alternativu rozhraní API

Kód v `OnModelCreating` metodu `DbContext` třídy používá *rozhraní fluent API* konfigurace EF chování. Rozhraní API se nazývá "fluent", protože je často používána zavěšování řadu volání metody společně na jediném příkazu, jako v následujícím příkladu od [EF Core dokumentaci](/ef/core/modeling/#use-fluent-api-to-configure-a-model):

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

V tomto kurzu se při použití rozhraní fluent API pouze pro mapování databáze, které nelze použít s atributy. Rozhraní fluent API však můžete také použít k určení většinu formátování, ověřování a pravidla mapování, která vám pomůžou s použitím atributů. Některé atributy, jako `MinimumLength` nelze použít s rozhraním API fluent. Jak už bylo zmíněno dříve, `MinimumLength` nedojde ke změně schématu, vztahuje se pouze ověřovacího pravidla na straně klienta a serveru.

Někteří vývojáři dávají přednost používání rozhraní fluent API výhradně tak, aby se zachovat jejich tříd entit "vyčištění." Pokud chcete, a existuje několik přizpůsobení, které lze provést pouze s použitím rozhraní fluent API je možné kombinovat atributy a dynamického rozhraní API, ale obecně doporučeným postupem je zvolte jednu z těchto dvou přístupů a použití, který konzistentně dosahovat. Pokud používáte obě, mějte na paměti, že bez ohledu na to dojde ke konfliktu, rozhraní Fluent API přepisuje atributy.

Další informace o atributech vs. rozhraní fluent API najdete v tématu [metody konfigurace](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagram znázorňující entitami

Následující obrázek znázorňuje diagram, který Entity Framework Power Tools vytvořit pro dokončené model školy.

![Entity diagram](complex-data-model/_static/diagram.png)

Kromě vztah jednoho k několika řádky (1 k \*), kterou tady vidíte řádek jedna nula nebo 1 v relaci m (1-0..1) mezi instruktorem a OfficeAssignment entity a relace nula nebo 1 n řádek (0.. 1 na *) mezi Entity instruktorem a oddělení.

## <a name="seed-database-with-test-data"></a>Počáteční hodnota databáze s testovací data

Nahraďte kód v *Data/DbInitializer.cs* souboru následujícím kódem, aby bylo možné poskytnout data počáteční hodnotu pro nové entity, které jste vytvořili.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Jak už jste viděli v první kurz, většina tento kód jednoduše vytvoří nové objekty entity a načte ukázková data do vlastnosti podle potřeby pro testování. Všimněte si, jak se zpracovává relace many-to-many: kód vytvoří relace tak, že vytvoříte entity v `Enrollments` a `CourseAssignment` připojení sady entit.

## <a name="add-a-migration"></a>Přidejte migraci

Uložte změny a sestavte projekt. Pak otevřete okno příkazového řádku ve složce projektu a zadejte `migrations add` příkazu (neprovádějte příkaz aktualizace databáze ještě):

```console
dotnet ef migrations add ComplexDataModel
```

Zobrazí se upozornění možnou ztrátu.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Pokud jste se pokusili spustit `database update` příkaz v tomto okamžiku (neprovádějte to zatím), by se zobrazí následující chyba:

> Příkaz ALTER TABLE způsobil konflikt s omezení FOREIGN KEY "FK_dbo. Course_dbo. Department_DepartmentID". Ke konfliktu došlo v databázi "ContosoUniversity" table "dbo. Oddělení", sloupec"DepartmentID".

Někdy při spuštění migrace s existujícími daty, je třeba vložit data zástupných procedur do databáze splňovat omezení cizího klíče. Generovaný kód `Up` metoda přidá do tabulky kurzu cizího klíče nepřipouštějící DepartmentID. Pokud už existují řádky v tabulce kurzu při spuštění kódu `AddColumn` operace selhala, protože systém SQL Server nebude vědět, jakou hodnotu put ve sloupci, který nemůže mít hodnotu null. Pro účely tohoto kurzu budete spouštět na migraci na novou databázi, ale v produkční aplikace bude muset migraci existujících dat, zpracování tak následující pokyny slouží jako ukázka toho, jak to udělat.

Práce s existujícími daty, budete muset změnit kód poskytnout výchozí hodnoty nového sloupce a vytvořit se zakázaným inzerováním oddělení tuto migraci s názvem "Temp" tak, aby fungoval jako výchozí oddělení. V důsledku toho existující řádky kurzu budou všechny souviset s oddělení "Temp" po `Up` metoda spuštění.

* Otevřít *{timestamp}_ComplexDataModel.cs* souboru.

* Zakomentovat řádek kódu, který přidá DepartmentID sloupec v tabulce kurzu.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Přidejte následující zvýrazněný kód za kód, který vytvoří tabulku oddělení:

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

V produkční aplikace měli byste napsat kód nebo skripty pro přidání řádků oddělení a jejich souvislostí kurzu řádků pro nové řádky oddělení. By pak už nepotřebujete oddělení "Temp" nebo výchozí hodnotu pro sloupec Course.DepartmentID.

Uložte změny a sestavte projekt.

## <a name="change-the-connection-string"></a>Změňte připojovací řetězec

Teď máte nový kód `DbInitializer` třídu, která přidá data počáteční hodnotu pro nové entity pro prázdnou databázi. Chcete-li vytvořit nové prázdné databáze EF, změňte název databáze v připojovacím řetězci v *appsettings.json* ContosoUniversity3 nebo jiný název, který jste ještě nepoužívali v počítači, který používáte.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Uložit změny do *appsettings.json*.

> [!NOTE]
> Jako alternativu k změně názvu databáze je odstranit databázi. Použití **Průzkumník objektů systému SQL Server** (SSOX) nebo `database drop` příkazu rozhraní příkazového řádku:
>
> ```console
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>Aktualizace databáze

Poté, co jste změnili název databáze nebo odstraní databáze, spusťte `database update` příkazu v příkazovém okně k provedení migrace.

```console
dotnet ef database update
```

Spuštění aplikace způsobí `DbInitializer.Initialize` metoda spuštění a naplňte jimi novou databázi.

Otevřete databázi v SSOX, jako jste to udělali dříve a rozbalte **tabulky** uzel zobrazíte, že všechny tabulky byly vytvořeny. (Pokud stále máte SSOX otevřít z dřívější čas, klikněte na tlačítko **aktualizovat** tlačítko.)

![Tabulky v SSOX](complex-data-model/_static/ssox-tables.png)

Spusťte aplikaci aktivovat inicializační kód, který nasazení nasazuje do databáze.

Klikněte pravým tlačítkem myši **CourseAssignment** tabulce a vybrat **Data zobrazení** ověřte, že má data v něm.

![Data CourseAssignment v SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vlastní datový model
> * Provedl změny entity studenta
> * Vytvořené entity instruktorem
> * Vytvořené entity OfficeAssignment
> * Upravená entita kurzu
> * Vytvořené entity oddělení
> * Upravená entita registrace
> * Aktualizovat kontext databáze
> * Dosazené databáze s testovací data
> * Přidání migrace
> * Změnit připojovací řetězec
> * Aktualizovat databázi

Přejděte k dalšímu kurzu, kde Další informace o tom, jak přistupovat k související data.

> [!div class="nextstepaction"]
> [Další: Přístup k související data](read-related-data.md)
