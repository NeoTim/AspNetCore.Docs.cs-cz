---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-a-more-complex-data-model-for-an-asp-net-mvc-application
title: 'Kurz: Vytvoření složitějšího datového modelu pro aplikace ASP.NET MVC'
author: tdykstra
description: V tomto kurzu přidáte další entity a relace a tak, že zadáte formátování, ověřování a pravidel mapování database budete Přizpůsobte si datový model.
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: 46f7f3c9-274f-4649-811d-92222a9b27e2
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-a-more-complex-data-model-for-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 5d05d456315c019c545a91b527bb05510126c06b
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836622"
---
# <a name="tutorial-create-a-more-complex-data-model-for-an-aspnet-mvc-app"></a>Kurz: Vytvoření složitějšího datového modelu pro aplikace ASP.NET MVC

V předchozích kurzech jste pracovali s jednoduchý datový model, který se skládá z tři entity. V tomto kurzu přidáte další entit a vztahů, a přizpůsobte si datový model zadáním formátování, ověřování a pravidel mapování databáze. Tento článek ukazuje dva způsoby, jak Přizpůsobte si datový model: přidáním atributů do tříd entit a přidáním kódu do třídy kontextu databáze.

Jakmile budete hotovi, tříd entit budou použity k vytvoření dokončeného datový model, který je znázorněno na následujícím obrázku:

![School_class_diagram](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image1.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přizpůsobte si datový model
> * Aktualizace entity studenta
> * Vytvoření entity instruktorem
> * Vytvoření OfficeAssignment entity
> * Upravit entity kurzu
> * Vytvoření entity oddělení
> * Upravit entity registrace
> * Přidání kódu do místní databáze
> * Počáteční hodnota databáze s testovací data
> * Přidejte migraci
> * Aktualizace databáze

## <a name="prerequisites"></a>Požadavky

* [První migrace a nasazení kódu](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="customize-the-data-model"></a>Přizpůsobte si datový model

V této části uvidíte, jak přizpůsobit datového modelu s použitím atributů, které určují databáze mapování pravidel ověřování a formátování. Pak v některé z následujících částí vytvoříte kompletní `School` modelu data přidáním atributy do třídy již vytvořené a vytváření nových tříd pro zbývající typy entit v modelu.

### <a name="the-datatype-attribute"></a>Datový typ atributu

Student zápisu dat všechny webové stránky aktuálně zobrazit čas spolu s datem, i když je všechno, co vás zajímá pro toto pole datum. S použitím anotace atributů dat, můžete si ho kódu změnu, která budou v každé zobrazení, která zobrazuje data opravit formát zobrazení. Chcete-li zobrazit příklad tohoto postupu, že přidáte atribut, který má `EnrollmentDate` vlastnost `Student` třídy.

V *Models\Student.cs*, přidejte `using` příkaz pro `System.ComponentModel.DataAnnotations` obor názvů a přidejte `DataType` a `DisplayFormat` atributů `EnrollmentDate` vlastnost, jak je znázorněno v následujícím příkladu:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample1.cs?highlight=3,12-13)]

[Datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atribut se používá k určení datový typ, který je specifičtější než vnitřní typ databáze. V tomto případě chceme jenom udržovat přehled o datum, nejsou data a času. [Datový typ výčtu](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) poskytuje pro mnoho typů dat, například *datum, čas, telefonní číslo, Měna, EmailAddress* a provádění dalších akcí. `DataType` Atribut můžete také povolit aplikace a zajistit tak automaticky specifické pro typ funkce. Například `mailto:` propojení lze vytvořit pro [DataType.EmailAddress](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx), a selektor date lze zadat pro [DataType.Date](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) v prohlížečích podporujících [HTML5](http://html5.org/). [Datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributy vysílá HTML 5 [data -](http://ejohn.org/blog/html-5-data-attributes/) (vyslovováno *data dash*) atributy, které umožní pochopit prohlížeče HTML 5. [Datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributy se neposkytuje žádné ověřování.

`DataType.Date` neurčuje formátu, který se zobrazí datum. Ve výchozím nastavení, zobrazí se pole data podle výchozí formát založený na serveru [CultureInfo](https://msdn.microsoft.com/library/vstudio/system.globalization.cultureinfo(v=vs.110).aspx).

`DisplayFormat` Atribut se používá s ohledem na formát data:


[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample2.cs)]


`ApplyFormatInEditMode` Nastavení určuje, že zadané formátování také bude použito při hodnota se zobrazí v textovém poli pro úpravu. (Pokud nechcete pro některá pole – například pro hodnoty měny, nemusí chcete symbol měny v textovém poli pro úpravu.)

Můžete použít [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) atribut samotný, ale to je obecně vhodné použít [datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) také atribut. `DataType` Atribut přenáší *sémantiku* dat jako rozdíl od vykreslování na obrazovce a nabízí následující výhody, které vám s `DisplayFormat`:

- HTML5 funkce můžete povolit v prohlížeči (například zobrazení ovládacího prvku kalendář, symbol měny odpovídající národní prostředí, odkazy na e-mailu, některé klientské vstupní ověřování atd.).
- Ve výchozím prohlížeči bude vykreslovat data ve správném formátu, na základě vašich [národní prostředí](https://msdn.microsoft.com/library/vstudio/wyzd2bce.aspx).
- [Datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atribut můžete povolit MVC zvolit šablonu pravé pole k poskytnutí dat ( [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) používá šablonu řetězec). Další informace najdete v tématu Brada Wilsona [šablony ASP.NET MVC 2](http://bradwilson.typepad.com/blog/2009/10/aspnet-mvc-2-templates-part-1-introduction.html). (I když napsané pro MVC 2, tento článek stále platí pro aktuální verzi technologie ASP.NET MVC.)

Pokud používáte `DataType` atribut pomocí pole pro datum, budete muset zadat `DisplayFormat` atribut také aby se správně vykresluje pole v prohlížečích Chrome. Další informace najdete v tématu [toto vlákno na StackOverflow](http://stackoverflow.com/questions/12633471/mvc4-datatype-date-editorfor-wont-display-date-value-in-chrome-fine-in-ie).

Další informace o tom, jak zpracovávat další formáty data v aplikaci MVC, přejděte na [Úvod MVC 5: Zkoumání metod úpravy a zobrazení pro úpravy](../introduction/examining-the-edit-methods-and-edit-view.md) a hledání ve stránce &quot;internacionalizace&quot;.

Znovu spusťte Student indexovou stránku a Všimněte si, že časy se už nezobrazují pro data registrací. Stejné budou platit pro všechna zobrazení, která se používá `Student` modelu.

![Students_index_page_with_formatted_date](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image2.png)

### <a name="the-stringlengthattribute"></a>StringLengthAttribute

Můžete také zadat data ověřovací pravidla a chybových zpráv ověření pomocí atributů. [StringLength atribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) Nastaví maximální počet znaků v databázi a poskytuje na straně klienta a na straně serveru ověřování pro architekturu ASP.NET MVC. Minimální délka řetězce. můžete také zadat v tomto atributu, ale minimální hodnota nemá žádný vliv na schéma databáze.

Předpokládejme, že chcete mít jistotu, že uživatelé nezadávejte více než 50 znaků pro název. Chcete-li přidat toto omezení, přidejte [StringLength](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) atributů `LastName` a `FirstMidName` vlastnosti, jak je znázorněno v následujícím příkladu:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample3.cs?highlight=10,12)]

[StringLength](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) atribut nebude zabránit uživateli v zadávání prázdné znaky pro název. Můžete použít [regulární výraz](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) atributu použít omezení na vstup. Například následující kód vyžaduje prvního znaku na velká písmena a zbývající znaky, které mají být abecední znak:

`[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]`

[MaxLength](https://msdn.microsoft.com/library/System.ComponentModel.DataAnnotations.MaxLengthAttribute.aspx) atribut poskytuje podobné funkce jako [StringLength](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) atribut, ale neposkytuje na straně klienta ověření.

Spusťte aplikaci a klikněte na tlačítko **studenty** kartu. Zobrazí následující chyba:

*Model zálohování kontextu 'SchoolContext' byl změněn, protože byla vytvořena databáze. Zvažte použití migrace Code First k aktualizaci databáze ([https://go.microsoft.com/fwlink/?LinkId=238269](https://go.microsoft.com/fwlink/?LinkId=238269)).*

Model databáze byl změněn způsobem, který vyžaduje změnu schématu databáze a Entity Framework, které zjistil. Migrace budete používat k aktualizaci schématu bez ztráty dat, který jste přidali k databázi pomocí uživatelského rozhraní. Pokud jste změnili dat, který byl vytvořen `Seed` metoda, která bude změněna zpět do původního stavu z důvodu [AddOrUpdate](https://msdn.microsoft.com/library/hh846520(v=vs.103).aspx) metodu, která používáte v `Seed` metody. ([AddOrUpdate](https://msdn.microsoft.com/library/hh846520(v=vs.103).aspx) je ekvivalentní k operaci "upsert", z databáze terminologie.)

V balíčku správce konzoly (konzolu PMC), zadejte následující příkazy:

[!code-console[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample4.cmd)]

`add-migration` Příkaz vytvoří soubor s názvem  *&lt;časové razítko&gt;\_MaxLengthOnNames.cs*. Tento soubor obsahuje kód v `Up` metodu, která aktualizuje databázi tak, aby odpovídaly aktuálním datovým modelem. `update-database` Tento kód byl spuštěn příkaz.

Časové razítko pro název souboru migrace používá Entity Framework pro řazení migrace. Můžete vytvořit více migrace dřív, než spustíte `update-database` příkaz a pak pro všechny migrace se použijí v pořadí, ve kterém byly vytvořeny.

Spustit **vytvořit** stránku a zadejte buď název delší než 50 znaků. Po kliknutí na **vytvořit**, ověřování na straně klienta se zobrazí chybová zpráva: *Pole LastName musí být řetězec s délkou maximálně 50.*

### <a name="the-column-attribute"></a>Atribut sloupce

Atributy můžete také řídit, jak třídy a vlastnosti jsou namapovány na databázi. Předpokládejme, že byste použili název `FirstMidName` pro název prvního pole, protože pole může obsahovat také křestní jméno. Chcete sloupec databáze s názvem, ale `FirstName`, protože uživatelé budou psát ad-hoc dotazy na databázi jsou zvyklí na tento název. Chcete-li toto mapování, můžete použít `Column` atribut.

`Column` Atribut určuje, že když se vytvoří databáze, sloupci `Student` tabulku, která se mapuje `FirstMidName` bude mít název vlastnosti `FirstName`. Jinými slovy, pokud váš kód odkazuje na `Student.FirstMidName`, data budou přicházet z nebo v aktualizovat `FirstName` sloupec `Student` tabulky. Pokud nechcete zadat názvy sloupců, jsou uvedeny stejný název jako název vlastnosti.

V *Student.cs* přidejte `using` příkaz pro [System.ComponentModel.DataAnnotations.Schema](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.aspx) a přidat sloupec název atributu `FirstMidName` vlastnost, jak je znázorněno následující zvýrazněný kód:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample5.cs?highlight=4,14)]

Přidání [atribut sloupce](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute.aspx) změní modelu zálohování SchoolContext, takže nebude odpovídat databázi. V konzole PMC vytvořit další migraci zadejte následující příkazy:

[!code-console[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample6.cmd)]

V **Průzkumníka serveru**, otevřete *Student* návrháře tabulky dvojitým kliknutím *Student* tabulky.

Následující obrázek ukazuje původní název sloupce, protože byla použita první dvě migrace. Kromě názvu sloupce změny z `FirstMidName` k `FirstName`, dva sloupce název byly změněny z `MAX` délku až 50 znaků.

![](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image5.png)

Můžete také provádět databáze pomocí změny mapování [rozhraní Fluent API](https://msdn.microsoft.com/data/jj591617), jak uvidíte později v tomto kurzu.

> [!NOTE]
> Pokud se pokusíte zkompilovat před dokončení vytváření všech tříd entit v následujících částech, může docházet k chybám kompilátoru.

## <a name="update-student-entity"></a>Aktualizace entity studenta

V *Models\Student.cs*, nahraďte kód, který jste přidali dříve následujícím kódem. Změny jsou zvýrazněné.

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample7.cs?highlight=11,13,15,18,22,25-32)]

### <a name="the-required-attribute"></a>Požadovaný atribut

[Požadovaný atribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute.aspx) díky povinná pole název vlastnosti. `Required attribute` Není potřeba u typů hodnot, jako je například datum a čas, int, double a float. Typy hodnot nelze přiřadit hodnotu null, aby ze své podstaty jsou považovány za povinné pole. Můžete odebrat [požadovaný atribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute.aspx) a nahraďte ji metodou minimální délku parametru `StringLength` atribut:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample8.cs?highlight=2)]

### <a name="the-display-attribute"></a>Atribut zobrazení

`Display` Atribut určuje, že titulek pro textová pole by měl být "Jméno", "Last Name", "Jméno" a "Datum registrace" namísto názvu vlastnosti v každé instanci (která nemá místo dělení slov).

### <a name="the-fullname-calculated-property"></a>FullName počítá vlastností

`FullName` je počítaná vlastnost, která vrací hodnotu, která se vytváří zřetězením dvou dalších vlastností. Proto má pouze `get` přistupující objekt a ne `FullName` vygeneruje sloupec v databázi.

## <a name="create-instructor-entity"></a>Vytvoření entity instruktorem

Vytvoření *Models\Instructor.cs*, nahraďte kód šablony následujícím kódem:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample9.cs)]

Všimněte si, že některé vlastnosti jsou ve stejné `Student` a `Instructor` entity. V [implementace dědičnosti](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md) kurz později v této sérii, budete Refaktorovat tento kód eliminovat redundance.

Více atributů můžete umístit na jednom řádku, proto by mohl taky zapisovat třídy kurzů vedených následujícím způsobem:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample10.cs)]

### <a name="the-courses-and-officeassignment-navigation-properties"></a>Kurzy a OfficeAssignment navigační vlastnosti

`Courses` a `OfficeAssignment` jsou navigační vlastnosti. Jak bylo vysvětleno dříve, se obvykle definují jako [virtuální](https://msdn.microsoft.com/library/9fkccyh4(v=vs.110).aspx) tak, aby můžete využít Entity Frameworku funkci s názvem [opožděné načtení](https://msdn.microsoft.com/magazine/hh205756.aspx). Kromě toho pokud navigační vlastnost může obsahovat více entit, jeho typ musí implementovat [rozhraní ICollection&lt;T&gt; ](https://msdn.microsoft.com/library/92t2ye13.aspx) rozhraní. Například [IList&lt;T&gt; ](https://msdn.microsoft.com/library/5y536ey6.aspx) kvalifikuje ale ne [IEnumerable&lt;T&gt; ](https://msdn.microsoft.com/library/9eekhta0.aspx) protože `IEnumerable<T>` neimplementuje [přidat ](https://msdn.microsoft.com/library/63ywd54z.aspx).

Instruktorem můžete naučit libovolný počet kurzů, takže `Courses` je definována jako kolekce `Course` entity.

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample11.cs)]

Naše obchodní pravidla stavu instruktorem může mít pouze nejvýše jeden office, takže `OfficeAssignment` je definován jako jeden `OfficeAssignment` entity (která může být `null` Pokud není přiřazena žádná office).

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample12.cs)]

## <a name="create-officeassignment-entity"></a>Vytvoření OfficeAssignment entity

Vytvoření *Models\OfficeAssignment.cs* následujícím kódem:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample13.cs)]

Sestavení projektu, který uloží změny a ověřuje, že nebyla provedena jakékoli kopírování a vkládání chyb, které kompilátor může zachytit.

### <a name="the-key-attribute"></a>Klíčový atribut

Existuje jedna: nula nebo 1 vztah mezi `Instructor` a `OfficeAssignment` entity. Přiřazení kanceláře existuje pouze ve vztahu k instruktorem, je přiřazen, a proto jeho primární klíč se také jeho cizí klíč `Instructor` entity. Entity Framework nemůže rozpoznat automaticky, ale `InstructorID` jako primární klíče této entity, protože jeho název `ID` nebo *classname* `ID` zásady vytváření názvů. Proto `Key` atribut se používá k jeho identifikaci jako klíč:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample14.cs)]

Můžete také použít `Key` atribut Pokud entita nemá primární klíč, ale budete chtít název vlastnosti něco jiného než `classnameID` nebo `ID`. Ve výchozím nastavení EF považuje za klíč bez databáze vygenerovala protože sloupec je pro identifikující relaci.

### <a name="the-foreignkey-attribute"></a>Atribut cizí klíč

Když dojde k nule nebo jednom relaci nebo relaci mezi dvěma entitami (takové mezi `OfficeAssignment` a `Instructor`), které end je závislý a které konci vztahu se objekt zabezpečení nemůže pracovat EF. Relace 1: 1 mají vlastnost navigační odkaz v každé třídě na třídu. [Klíč ForeignKey atribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute.aspx) lze použít u závislé třídy a tím vytvoří vztah. Vynecháte-li [klíč ForeignKey atribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute.aspx), při pokusu o vytvoření migrace se zobrazí následující chyba:

*Nepovedlo se určit hlavní konec asociace mezi typy "ContosoUniversity.Models.OfficeAssignment" a "ContosoUniversity.Models.Instructor". Hlavní konec toto přidružení explicitně nastavené pomocí rozhraní API fluent relace nebo datových poznámek.*

Později v tomto kurzu uvidíte, jak nakonfigurovat tuto relaci s rozhraním API fluent.

### <a name="the-instructor-navigation-property"></a>Navigační vlastnost instruktorem

`Instructor` Entita má s povolenou hodnotou Null `OfficeAssignment` navigační vlastnosti (protože instruktorem nemusí mít přiřazení office) a `OfficeAssignment` entita má zakázanou `Instructor` navigační vlastnosti (protože nelze přiřazení office existovat bez instruktorem – `InstructorID` je null). Když `Instructor` entita má se souvisejícím `OfficeAssignment` entity, každá entita bude mít odkaz na druhou v jeho navigační vlastnost.

Můžete umístit `[Required]` atribut u vlastnosti navigace kurzů vedených k určení, že musí být související instruktorem, ale nemáte to provést, protože InstructorID cizí klíč (což je také klíč do této tabulky) je null.

## <a name="modify-the-course-entity"></a>Upravit entity kurzu

V *Models\Course.cs*, nahraďte kód, který jste přidali dříve následujícím kódem:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample15.cs)]

Kurz entita má vlastnost cizího klíče `DepartmentID` který odkazuje na související `Department` entity a má `Department` navigační vlastnost. Entity Framework nevyžaduje, můžete přidat vlastnost cizího klíče do datového modelu, když máte navigační vlastnost pro související entity. EF automaticky vytvoří cizí klíče v databázi bez ohledu na to se v případě potřeby zapíná. Ale s cizího klíče v datovém modelu může být aktualizace jednodušší a efektivnější. Například při načtení entity kurzu chcete upravit, `Department` entity má hodnotu null Pokud načtete není to, proto když aktualizujete entity kurzu budete mít nejdřív načíst `Department` entity. Když vlastnost cizího klíče `DepartmentID` je zahrnuta v datovém modelu, není nutné načíst `Department` entity před aktualizací.

### <a name="the-databasegenerated-attribute"></a>Atribut DatabaseGenerated

[DatabaseGenerated atribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute.aspx) s [žádný](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedoption(v=vs.110).aspx) parametru u `CourseID` vlastnost určuje, že hodnoty primárního klíče jsou uživatelem zadané místo generován databází.

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample16.cs)]

Ve výchozím nastavení Entity Framework předpokládá, že hodnoty primárního klíče je generován databází. Který se má ve většině scénářů. Ale pro `Course` entity, budete používat číslo uživatel zadal kurzu například řadu 1000 pro jedno oddělení, řadu 2000 pro jiného oddělení a tak dále.

### <a name="foreign-key-and-navigation-properties"></a>Cizí klíč a navigačních vlastností

Vlastnosti cizího klíče a vlastnosti navigace v `Course` entity zahrnují následující vztahy:

- Kurz je přiřazena jednoho oddělení, takže `DepartmentID` cizího klíče a `Department` navigační vlastnost z důvodů uvedených výše.

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample17.cs)]
- Kurz můžete mít libovolný počet studentů zaregistrované, takže `Enrollments` navigační vlastnost je kolekce:

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample18.cs)]
- Kurz může být vedená instruktorů více, proto `Instructors` navigační vlastnost je kolekce:

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample19.cs)]

## <a name="create-the-department-entity"></a>Vytvoření entity oddělení

Vytvoření *Models\Department.cs* následujícím kódem:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample20.cs)]

### <a name="the-column-attribute"></a>Atribut sloupce

Dříve jste použili [atribut sloupce](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute.aspx) Chcete-li změnit název mapování sloupců. V kódu `Department` entity, `Column` atribut se používá k změnit SQL mapování datového typu tak, aby sloupec bude definován pomocí serveru SQL Server [peníze](https://msdn.microsoft.com/library/ms179882.aspx) typ databáze:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample21.cs)]

Mapování sloupců se obecně nevyžaduje, protože rozhraní Entity Framework obvykle vybere odpovídající datový typ SQL serveru na základě typu CLR, které definujete pro vlastnost. Modul CLR `decimal` zadejte mapuje se na serveru SQL Server `decimal` typu. Ale v takovém případě budete vědět, že sloupec se drží peněžních hodnot a [peníze](https://msdn.microsoft.com/library/ms179882.aspx) datový typ je vhodnější k tomu. Další informace o datové typy CLR a jak přizpůsobit datové typy serveru SQL Server najdete v tématu [SqlClient pro typy Entity Framework](https://msdn.microsoft.com/library/bb896344.aspx).

### <a name="foreign-key-and-navigation-properties"></a>Cizí klíč a navigačních vlastností

Vlastnosti cizího klíče a navigace zahrnují následující vztahy:

- Oddělení může nebo nemusí mít správce a správce je vždy instruktorem. Proto `InstructorID` vlastnost je součástí jako cizí klíč `Instructor` přidá entity a otazník za `int` označení označit vlastnosti jako datový typ s možnou hodnotou Null typu. Navigační vlastnost jmenuje `Administrator` obsahuje, ale `Instructor` entity:

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample22.cs)]
- Oddělení může mít mnoho kurzů, takže `Courses` navigační vlastnost:

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample23.cs)]

  > [!NOTE]
  > Podle konvence rozhraní Entity Framework umožňuje kaskádové odstranění pro Null cizí klíče a vztahy many-to-many. To může způsobit Cyklické kaskádové odstranění pravidla, která způsobí výjimku při pokusu o přidání migrace. Například pokud definujete nebyla `Department.InstructorID` vlastnost jako s možnou hodnotou Null, získali byste k následující výjimce: "Referenční vztahu povede cyklického odkazu, který není povolen." V případě potřeby obchodní pravidla `InstructorID` vlastnosti být null, je třeba použít následující příkaz rozhraní API fluent zakázat kaskádové odstranění v relaci:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample24.cs)]

## <a name="modify-the-enrollment-entity"></a>Upravit entity registrace

 V *Models\Enrollment.cs*, nahraďte kód, který jste přidali dříve následujícím kódem

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample25.cs?highlight=1,15)]

### <a name="foreign-key-and-navigation-properties"></a>Cizí klíč a navigačních vlastností

Vlastnosti cizího klíče a navigačních vlastností zahrnují následující vztahy:

- Záznam registrace je jeden kurzům, tedy `CourseID` vlastnost cizího klíče a `Course` navigační vlastnost:

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample26.cs)]
- Záznam registrace je pro jeden student, tedy `StudentID` vlastnost cizího klíče a `Student` navigační vlastnost:

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample27.cs)]

### <a name="many-to-many-relationships"></a>Relace m: N

Existuje vztah n: n mezi `Student` a `Course` entity a `Enrollment` entity funguje jako tabulka many-to-many spojení *s datovou částí* v databázi. To znamená, že `Enrollment` tabulka obsahuje další údaje kromě cizí klíče pro spojené tabulky (v tomto případě primární klíč a `Grade` vlastnost).

Následující obrázek znázorňuje, jak tyto vztahy vypadat v diagramu entity. (Tento diagram se vygeneroval pomocí [Entity Framework Power Tools](https://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d); vytvoření diagramu, které nejsou součástí tohoto kurzu, je právě používán jako ilustraci zde.)

![Student-Course_many-to-many_relationship](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image12.png)

Každý řádek vztah má 1 na jednom konci a hvězdičku (\*) na druhém, určující vztah jeden mnoho.

Pokud `Enrollment` tabulky nezahrnuli na podnikové úrovni informace, třeba jenom tak, aby obsahovala dva cizího klíče `CourseID` a `StudentID`. V takovém případě by odpovídat na tabulku spojení many-to-many *bez datové části* (nebo *čistě vazební tabulka*) v databázi, a nebude nutné vytvořit třídu modelu, vůbec. `Instructor` a `Course` entity mají tento druh vztahu many-to-many, a jak je vidět, mezi nimi neexistuje žádná třída entity:

![Kurzů vedených Course_many k many_relationship](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image13.png)

Vazební tabulka v databázi, ale vyžaduje, jak je znázorněno v následujícím diagramu databáze:

![Kurzů vedených Course_many k many_relationship_tables](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image14.png)

Entity Framework automaticky vytvoří `CourseInstructor` tabulku a číst a aktualizovat ho nepřímo pomocí čtení a aktualizace `Instructor.Courses` a `Course.Instructors` navigační vlastnosti.

## <a name="entity-relationship-diagram"></a>Diagram vztahů entit

Následující obrázek znázorňuje diagram, který Entity Framework Power Tools vytvořit pro dokončené model školy.

![School_data_model_diagram](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image1.png)

Kromě many-to-many vztahu čáry (\* k \*) a vztah jednoho k několika řádky (1 \*), Zde uvidíte řádek jedna nula nebo 1 v relaci m (1-0..1) mezi `Instructor` a `OfficeAssignment` entity a relace nula nebo 1 n řádek (0.. 1 na \*) mezi entitami instruktorem a oddělení.

## <a name="add-code-to-database-context"></a>Přidání kódu do místní databáze

Dále přidáte nové entity, které chcete `SchoolContext` třídy a přizpůsobit některé z mapování pomocí [rozhraní fluent API](https://msdn.microsoft.com/data/jj591617) volání. Rozhraní API je "fluent", protože je často používána zavěšování řadu volání metody společně na jediném příkazu, jako v následujícím příkladu:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample28.cs)]

V tomto kurzu použijete rozhraní fluent API pouze pro mapování databáze, které nelze použít s atributy. Rozhraní fluent API však můžete také použít k určení většinu formátování, ověřování a pravidla mapování, která vám pomůžou s použitím atributů. Některé atributy, jako `MinimumLength` nelze použít s rozhraním API fluent. Jak už bylo zmíněno dříve, `MinimumLength` nedojde ke změně schématu, vztahuje se pouze ověřovacího pravidla na straně klienta a serveru

Někteří vývojáři dávají přednost používání rozhraní fluent API výhradně tak, aby se zachovat jejich tříd entit "vyčištění." Pokud chcete, a existuje několik přizpůsobení, které lze provést pouze s použitím rozhraní fluent API je možné kombinovat atributy a dynamického rozhraní API, ale obecně doporučeným postupem je zvolte jednu z těchto dvou přístupů a použití, který konzistentně dosahovat.

Pokud chcete přidat nové entity data model a provést mapování databáze, které jste neprovedli pomocí atributů, nahraďte kód v *DAL\SchoolContext.cs* následujícím kódem:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample29.cs)]

Nový příkaz v [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating(v=vs.103).aspx) metoda nakonfiguruje many-to-many vazební tabulka:

- Pro many-to-many vztah mezi `Instructor` a `Course` entity, kód určuje názvy tabulek a sloupců pro tabulku spojení. Kód nejprve můžete nakonfigurovat vztah mnoho mnoho pro vás bez tohoto kódu, ale pokud není říkat, zobrazí se výchozí názvy, jako `InstructorInstructorID` pro `InstructorID` sloupce.

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample30.cs)]

Následující kód znázorňuje, jak může použitých rozhraní fluent API namísto atributů k určení vztahu mezi `Instructor` a `OfficeAssignment` entity:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample31.cs)]

Informace o příkazech "rozhraní API fluent" činnosti na pozadí, najdete v článku [rozhraní Fluent API](https://blogs.msdn.com/b/aspnetue/archive/2011/05/04/entity-framework-code-first-tutorial-supplement-what-is-going-on-in-a-fluent-api-call.aspx) blogový příspěvek.

## <a name="seed-database-with-test-data"></a>Počáteční hodnota databáze s testovací data

Nahraďte kód v *Migrations\Configuration.cs* souboru následujícím kódem, aby bylo možné poskytnout data počáteční hodnotu pro nové entity, které jste vytvořili.

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample32.cs)]

Jak už jste viděli v první kurz, většina tento kód jednoduše aktualizuje nebo vytváří nové objekty entity a načte ukázková data do vlastnosti podle potřeby pro testování. Všimněte si však jak `Course` entity, která má vztah mnoho mnoho s `Instructor` entity, probíhá:

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample33.cs)]

Když vytvoříte `Course` objektu, je inicializovat `Instructors` navigační vlastnost jako prázdnou kolekci pomocí kódu `Instructors = new List<Instructor>()`. Díky tomu je možné přidat `Instructor` entity, které se vztahují k tomuto `Course` pomocí `Instructors.Add` metody. Pokud jste nevytvořili je seznam prázdný, jste nemohli přidat tyto relace, protože `Instructors` vlastnost by mít hodnotu null a nebude mít `Add` metody. Inicializační seznam můžete také přidat do konstruktoru.

## <a name="add-a-migration"></a>Přidejte migraci

V konzole PMC, zadejte `add-migration` příkazu (přitom nedělají nic `update-database` ještě příkazu):

`add-Migration ComplexDataModel`

Pokud jste se pokusili spustit `update-database` příkaz v tomto okamžiku (neprovádějte to zatím), by se zobrazí následující chyba:

*Příkaz ALTER TABLE způsobil konflikt s omezení FOREIGN KEY "FK\_dbo. Kurz\_dbo. Oddělení\_DepartmentID ". Ke konfliktu došlo v databázi "ContosoUniversity" table "dbo. Oddělení", sloupec"DepartmentID".*

V případech, kdy můžete spustit migrace s existujícími daty, je třeba vložit data zástupných procedur do databáze splňovat omezení cizího klíče a, který je, co musíte udělat teď. Generovaný kód ComplexDataModel `Up` metoda přidá neumožňující `DepartmentID` cizí klíč `Course` tabulky. Protože jsou již řádků v `Course` tabulky při spuštění kódu `AddColumn` operace selže, protože systém SQL Server nebude vědět, jakou hodnotu put ve sloupci, který nemůže mít hodnotu null. Proto budete muset změnit kód nového sloupce výchozí hodnotu, a vytvořit se zakázaným inzerováním oddělení s názvem "Temp" tak, aby fungoval jako výchozí oddělení. V důsledku toho existující `Course` řádky budou všechny souviset s oddělení "Temp" po `Up` metoda spuštění. Propojovat je správný oddělením ve `Seed` metody.

Upravit &lt; *časové razítko&gt;\_ComplexDataModel.cs* , Odkomentujte řádek kódu, který přidá DepartmentID sloupec v tabulce kurzu a přidejte následující zvýrazněný kód (komentářem řádek je také zvýrazněna):

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample34.cs?highlight=14-18)]

Při `Seed` metoda pracuje, vloží řádků v `Department` tabulky a jejich vztah existující `Course` řádků, které mají tyto nové `Department` řádků. Pokud jste nepřidali žádné kurzy v uživatelském rozhraní, by potom už nepotřebujete oddělení "Temp" nebo výchozí hodnotu na `Course.DepartmentID` sloupce. Povolit možnost, že někdo může přidali kurzy s použitím aplikace, by také chcete aktualizovat `Seed` kódu metody zajistit, aby všechny `Course` řádky (nejen těm, které jsou vloženy pomocí předchozích spuštění `Seed` metoda) mají platný `DepartmentID` hodnoty před odebráním výchozí hodnotu ze sloupce a odstranit oddělení "Temp".

## <a name="update-the-database"></a>Aktualizace databáze

Po dokončení úprav &lt; *časové razítko&gt;\_ComplexDataModel.cs* soubor, zadejte `update-database` příkazu v konzole PMC k provedení migrace.

[!code-powershell[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample35.ps1)]

> [!NOTE]
> Je možné získat další chyby při migraci dat a provádění změn schématu. Pokud se zobrazí chyby při migraci, které nelze vyřešit, můžete změnit název databáze v připojovacím řetězci nebo odstranit databázi. Nejjednodušším způsobem je přejmenovat databázi v *Web.config* souboru. Následující příklad ukazuje název změní na kapacitní jednotka\_testu:
>
> [!code-xml[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample36.xml?highlight=1)]
>
> S novou databázi, nejsou žádná data chcete migrovat a `update-database` příkaz je mnohem pravděpodobnější k dokončení bez chyb. Pokyny k odstranění databáze najdete v tématu [jak vyřadit databázi ze sady Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/).
>
> Pokud selže, je dalším krokem, který můžete vyzkoušet, znovu inicializovat databázi zadáním následujícího příkazu v konzole PMC:
>
> `update-database -TargetMigration:0`

Otevřít databázi v **Průzkumníka serveru** stejně jako dříve a rozbalte **tabulky** uzel zobrazíte, že všechny tabulky byly vytvořeny. (Pokud stále máte **Průzkumníka serveru** otevřít z dřívější čas, klikněte na tlačítko **aktualizovat** tlačítko.)

![](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image16.png)

Jste nevytvořili třídy modelu pro `CourseInstructor` tabulky. Jak jsme vysvětlili výše, toto je tabulka spojení pro many-to-many vztah mezi `Instructor` a `Course` entity.

Klikněte pravým tlačítkem myši `CourseInstructor` tabulky a vyberte **zobrazit Data tabulky** ověřte, že má data v něm kvůli `Instructor` entity, které jste přidali do `Course.Instructors` navigační vlastnost.

![Table_data_in_CourseInstructor_table](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image17.png)

## <a name="get-the-code"></a>Získat kód

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Další zdroje

Odkazy na další zdroje Entity Framework najdete v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vlastní datový model
> * Aktualizovaná entita studenta
> * Vytvořené entity instruktorem
> * Vytvořené entity OfficeAssignment
> * Upravit entity kurzu
> * Vytvoření entity oddělení
> * Upravit entity registrace
> * Přidání kódu do místní databáze
> * Dosazené databáze s testovací data
> * Přidání migrace
> * Aktualizovat databázi

Přejděte k dalším článku se dozvíte, jak načíst a zobrazit související data, která načte Entity Framework do navigační vlastnosti.

> [!div class="nextstepaction"]
> [Čtení souvisejících dat](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)
