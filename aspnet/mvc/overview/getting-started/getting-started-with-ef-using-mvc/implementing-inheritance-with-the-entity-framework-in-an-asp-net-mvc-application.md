---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Šablony: Implementace dědičnosti s EF v aplikacích pro rozhraní ASP.NET MVC 5'
description: Tento kurzu se dozvíte, jak implementovat dědičnosti v datovém modelu.
author: tdykstra
ms.author: riande
ms.date: 01/21/2019
ms.topic: tutorial
ms.assetid: 08834147-77ec-454a-bb7a-d931d2a40dab
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: df8715e4416ce3ccdf1d9e380addcded553d85f8
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444282"
---
# <a name="template-implement-inheritance-with-ef-in-an-aspnet-mvc-5-app"></a>Šablony: Implementace dědičnosti s EF v aplikaci ASP.NET MVC 5

V předchozím kurzu zpracování výjimky souběžnosti. Tento kurzu se dozvíte, jak implementovat dědičnosti v datovém modelu.

V objektově orientované programování, můžete použít [dědičnosti](http://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) usnadnit [opakované použití kódu](http://en.wikipedia.org/wiki/Code_reuse). V tomto kurzu změníte `Instructor` a `Student` tak, že jsou odvozeny z třídy `Person` základní třída, která obsahuje vlastnosti, například `LastName` , které jsou společné pro vyučující a studenty. Nebude přidat nebo změnit libovolné webové stránky, ale změníte kód, a tyto změny se automaticky projeví v databázi.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zjistěte, jak mapovat dědičnosti do databáze
> * Vytvořte třídu osoby
> * Aktualizace instruktorem a studentů
> * Do modelu přidat osoby
> * Vytvoření a aktualizaci migrace
> * Provedení testu
> * Nasazení do Azure

## <a name="prerequisites"></a>Požadavky

* [Implementace dědičnosti](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="map-inheritance-to-database"></a>Mapovat dědičnosti do databáze

`Instructor` a `Student` tříd v `School` datovém modelu mají několik vlastností, které jsou shodné:

![Student_and_Instructor_classes](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

Předpokládejme, že chcete vyloučit redundantní kód pro vlastnosti, které jsou sdíleny `Instructor` a `Student` entity. Nebo chcete zadat službu, která dokáže formátovat názvy bez caring, zda název pochází instruktorem nebo student. Můžete vytvořit `Person` základní třída, která obsahuje pouze ty sdílené vlastnosti a pak proveďte `Instructor` a `Student` entit dědí ze základní třídy, jak je znázorněno na následujícím obrázku:

![Student_and_Instructor_classes_deriving_from_Person_class](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

Existuje několik způsobů, které tato struktura dědičnosti můžou být reprezentované v databázi. Mohli byste mít `Person` tabulku, která obsahuje informace o studenty a vyučující v jediné tabulce. Některé sloupce, které může používat jenom u Instruktoři (`HireDate`), některé jenom pro studenty (`EnrollmentDate`), některé obě (`LastName`, `FirstName`). Obvykle byste měli *diskriminátoru* sloupec, aby označoval, jaký typ každý řádek představuje. Pro sloupec diskriminátoru může mít například "Kurzů vedených" pro vyučující a "Studentů" pro studenty.

![Table-per-hierarchy_example](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

Tento model generování struktury dědičnosti entity z tabulky izolovanou databázi nazvali *na hierarchii tabulky* dědičnost (TPH).

Další možností je databázi tak, aby vypadat více jako struktury dědičnosti. Například může mít pouze název pole `Person` tabulky a mít samostatné `Instructor` a `Student` tabulky s poli datum.

![Table-per-type_inheritance](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

Tento model vytváření tabulky databáze pro každou třídu entity se nazývá *tabulky podle typu* dědičnosti (TPT).

Další možností je ještě všechny typy neabstraktní namapovat jednotlivé tabulky. Všechny vlastnosti třídy, včetně zděděné vlastnosti mapovat na sloupce příslušné tabulky. Tento model se nazývá dědičnost tabulky na konkrétní třídy (TPC). Pokud jste implementovali TPC dědičnosti pro `Person`, `Student`, a `Instructor` třídy, jak je uvedeno výše, `Student` a `Instructor` tabulky by vypadalo nijak neliší po implementaci dědičnosti než dříve.

TPC a vzory dědičnosti TPH obecně poskytují lepší výkon v Entity Framework než vzory TPT dědičnosti, protože TPT vzory může vést k dotazům komplexní spojení.

Tento kurz ukazuje, jak implementovat TPH dědičnosti. TPH je výchozí model dědičnosti v Entity Framework, aby vše, co musíte udělat, je vytvořit `Person` tříd, změnit `Instructor` a `Student` třídy, které jsou odvozeny z `Person`, přidejte novou třídu do `DbContext`a vytvořit migrace. (Informace o tom, jak implementovat další vzory dědičnosti, naleznete v tématu [mapování dědičnosti na typ tabulky (TPT)](https://msdn.microsoft.com/data/jj591617#2.5) a [mapování tabulky na konkrétní třídy (TPC) dědičnosti](https://msdn.microsoft.com/data/jj591617#2.6) v MSDN Dokumentace Entity Framework.)

## <a name="create-the-person-class"></a>Vytvořte třídu osoby

V *modely* složku, vytvořte *Person.cs* a nahraďte kód šablony následujícím kódem:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

## <a name="update-instructor-and-student"></a>Aktualizace instruktorem a studentů

Teď aktualizovat *Instructor.cs* a *Sudent.cs* dědit z hodnoty *Person.sc*.

V *Instructor.cs*, odvozovat `Instructor` třídy z `Person` třídy a odstraňte klíč a název pole. Kód bude vypadat jako v následujícím příkladu:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Provádět podobné změny *Student.cs*. `Student` Třídy bude vypadat jako v následujícím příkladu:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

## <a name="add-person-to-the-model"></a>Do modelu přidat osoby

V *SchoolContext.cs*, přidejte `DbSet` vlastnost `Person` typ entity:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

To je vše, Entity Framework potřebuje, aby konfigurace tabulky na hierarchii dědičnosti. Jak zjistíte, když se aktualizuje databázi, bude mít `Person` tabulky místo `Student` a `Instructor` tabulky.

## <a name="create-and-update-migrations"></a>Vytvoření a aktualizaci migrace

V balíčku správce konzoly (konzolu PMC), zadejte následující příkaz:

`Add-Migration Inheritance`

Spustit `Update-Database` příkazu v konzole PMC. Příkaz se nezdaří v tomto okamžiku, když máme existující data, která migrace nebude vědět, jak zpracovat. Získáte třeba následující chybová zpráva:

> *Nelze vyřadit objekt "dbo. Instruktorem ' protože se odkazuje omezení FOREIGN KEY.*


Otevřít *migrace\&lt; časové razítko&gt;\_Inheritance.cs* a nahraďte `Up` metodu s následujícím kódem:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

Tento kód se postará o tyto úlohy aktualizace databáze:

- Odebere omezení cizího klíče a indexy, které odkazují na tabulce studentů.
- Přejmenuje tabulce kurzů vedených jako osoba a provede změny, které jsou potřeba, aby se uložení údajů studentů:

    - Přidá EnrollmentDate s možnou hodnotou Null pro studenty.
    - Přidá sloupec diskriminátoru k označení, zda je řádek student nebo instruktorem.
    - Protože student řádků nebude mít dat, díky HireDate s možnou hodnotou Null.
    - Přidá dočasné pole, který se použije k aktualizaci cizí klíče, které odkazují na studenty. Při kopírování do tabulky osob studenti získají první nové hodnoty primárního klíče.
- Kopíruje data z tabulky Student do tabulky osob. To způsobí, že studenti mohli přiřadit nové hodnoty primárního klíče.
- Opravy hodnoty cizího klíče, které odkazují na studenty.
- Znovu vytvoří omezení cizího klíče a indexy, nyní je odkazující na tabulku osoby.

(Namísto celého čísla byste použili identifikátor GUID jako typ primárního klíče, student hodnoty primárního klíče nemusí měnit a některé z těchto kroků může vynechat.)

Spustit `update-database` příkaz znovu.

(V produkční systém by změníte odpovídající metody dolů v případě, že někdy museli vrátit k předchozí verzi databáze použít. V tomto kurzu nebudete používat metody dolů.)

> [!NOTE]
> Je možné získat další chyby při migraci dat a provádění změn schématu. Pokud se zobrazí chyby při migraci nevyřešíte sami, můžete pokračovat v kurzu tak, že změníte připojovací řetězec *Web.config* souboru nebo odstraněním databáze. Nejjednodušším způsobem je přejmenovat databázi *Web.config* souboru. Například změňte název databáze do ContosoUniversity2, jak je znázorněno v následujícím příkladu:
>
> [!code-xml[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.xml?highlight=2)]
>
> S novou databázi, nejsou žádná data chcete migrovat a `update-database` příkaz je mnohem pravděpodobnější k dokončení bez chyb. Pokyny k odstranění databáze najdete v tématu [jak vyřadit databázi ze sady Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/). Pokud budete postupovat tímto přístupem budete pokračovat v kurzu, na konci tohoto kurzu přeskočit krok nasazení nebo nasazení do nové lokality a databáze. Pokud nasazujete aktualizace do stejné lokality, které jste se nasazujete do již, EF dojde ke stejné chybě migrace automaticky spustí. Pokud chcete odstranění chyby migrace, je nejlepší prostředek Entity Framework fóra nebo StackOverflow.com.

## <a name="test-the-implementation"></a>Provedení testu

Spuštění tohoto webu a vyzkoušejte různé stránky. Všechno, co funguje stejně jako předtím.

V **Průzkumníka serveru** rozbalte **Data Connections\SchoolContext** a potom **tabulky**, a uvidíte, že **Student** a **Kurzů vedených** byly nahrazeny tabulky **osoba** tabulky. Rozbalte **osoba** tabulky a najdete v článku, že obsahuje všechny sloupce, které mají být používány v **Student** a **kurzů vedených** tabulky.

Klikněte pravým tlačítkem na tabulku osoba a potom klikněte na **zobrazit Data tabulky** zobrazit sloupec diskriminátoru.

Následující diagram znázorňuje strukturu nové databáze školy:

![School_database_diagram](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

## <a name="deploy-to-azure"></a>Nasazení do Azure

Tato část vyžaduje, abyste dokončili nepovinný **nasazení aplikace do Azure** tématu [část 3, řazení, filtrování a stránkování](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md) této série kurzů. Pokud jste měli migrace chyby, které jste vyřešili tak, že odstraníte databáze v projektu pro místní, přeskočte tento krok. nebo vytvořte novou lokalitu a databázi a nasadit do nového prostředí.

1. Ve Visual Studiu klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat** v místní nabídce.

2. Klikněte na tlačítko **publikovat**.

    Webové aplikace se otevře ve vašem výchozím prohlížeči.

3. Otestování aplikace ověří, že funguje.

    Při prvním spuštění stránky, který přistupuje k databázi, Entity Framework provozovat všechny migrace `Up` metod požadovaných k aktuální s aktuálním datovým modelem databáze.

## <a name="get-the-code"></a>Získat kód

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Další zdroje

Odkazy na další zdroje Entity Framework najdete v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).

Další informace o tomto a dalších struktury dědičnosti najdete v tématu [model dědičnosti TPT](https://msdn.microsoft.com/data/jj618293) a [model dědičnosti TPH](https://msdn.microsoft.com/data/jj618292) na webové stránce MSDN. V dalším kurzu uvidíte, jak zpracovat širokou škálu relativně pokročilé scénáře rozhraní Entity Framework.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zjistili, jak mapovat dědičnosti do databáze
> * Vytvoření třídy osoby
> * Aktualizované instruktorem a studentů
> * Přidání, kdo modelu
> * Vytvoření a aktualizaci migrace
> * Testování implementace
> * Nasazení do Azure

Přejděte k dalším článku se dozvíte o tématech, které jsou užitečné mít na paměti při nad rámec základní informace o vývoji webových aplikací ASP.NET, které používají Entity Framework Code First.
> [!div class="nextstepaction"]
> [Pokročilé scénáře pro Entity Framework](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)