---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Kurz: Čtení souvisejících dat s EF v aplikaci ASP.NET MVC'
description: V tomto kurzu budete čtení a zobrazení souvisejících dat – to znamená, že data, která načte Entity Framework do navigační vlastnosti.
author: tdykstra
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: 18cdd896-8ed9-4547-b143-114711e3eafb
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 5f45820cd7abd60e69c19368cee50e91e31a75a0
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836828"
---
# <a name="tutorial-read-related-data-with-ef-in-an-aspnet-mvc-app"></a>Kurz: Čtení souvisejících dat s EF v aplikaci ASP.NET MVC

V předchozím kurzu jste dokončili školní datového modelu. V tomto kurzu budete čtení a zobrazení souvisejících dat – to znamená, že data, která načte Entity Framework do navigační vlastnosti.

Na následujících obrázcích stránky, kterou budete pracovat.

![](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Instructors_index_page_with_instructor_and_course_selected](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zjistěte, jak načíst související data
> * Vytvoření stránky kurzy
> * Vytvoření stránky Instruktoři

## <a name="prerequisites"></a>Požadavky

* [Vytvoření složitějšího datového modelu](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)

## <a name="learn-how-to-load-related-data"></a>Zjistěte, jak načíst související data

Existuje několik způsobů, Entity Framework mohou načíst související data do navigační vlastnosti entity:

- *Opožděné načtení*. Pokud entita je nejdřív přečíst, související data nebude načten. Ale při prvním pokusu o přístup k vlastnosti navigace data požadovaná pro tuto navigační vlastnost je automaticky načte. Výsledkem je více dotazy odeslané do databáze – jeden pro samotné entity a jeden pokaždé, když související data entity musí být načten. `DbContext` Třídy ve výchozím nastavení povoluje opožděné načtení.

    ![Lazy_loading_example](https://asp.net/media/2577850/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Lazy_loading_example_2c44eabb-5fd3-485a-837d-8e3d053f2c0c.png)
- *Předběžné načítání*. Při čtení entity související data načíst společně. Obvykle v důsledku jednoho spojení dotaz, který zkopíruje všechna data, který je nezbytný. Předběžné načítání určíte pomocí `Include` metody.

    ![Eager_loading_example](https://asp.net/media/2577856/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Eager_loading_example_33f907ff-f0b0-4057-8e75-05a8cacac807.png)
- *Explicitní načtení*. To se podobá opožděné načtení, s tím rozdílem, že explicitně načíst související data v kódu. je tomu tak není automaticky při přístupu k vlastnosti navigace. Ruční načtení souvisejících dat tím, že získáme položka správce stavu objektu pro entitu a volání [Collection.Load](https://msdn.microsoft.com/library/gg696220(v=vs.103).aspx) metodu pro kolekce nebo [Reference.Load](https://msdn.microsoft.com/library/gg679166(v=vs.103).aspx) metodu pro vlastnosti, které hostují jedna entita. (V následujícím příkladu, pokud chcete načíst správce navigační vlastnost, by byly nahrazeny `Collection(x => x.Courses)` s `Reference(x => x.Administrator)`.) Obvykle můžete využít explicitní načtení jenom v případě, že aktivovali jsme opožděné načtení vypnout.

    ![Explicit_loading_example](https://asp.net/media/2577862/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Explicit_loading_example_79d8c368-6d82-426f-be9a-2b443644ab15.png)

Protože jejich není okamžitě hodnoty vlastností, opožděné načtení a explicitní načtení se obě označují jako *odložené načítání*.

### <a name="performance-considerations"></a>Důležité informace o výkonu

Pokud víte, že budete potřebovat pro každou entitu načíst související data, nemůžou dočkat, až načítání často nabízí nejlepší výkon, protože jednoho dotazu odeslaného do databáze je obvykle mnohem efektivnější než samostatné dotazy pro každou entitu načíst. Například ve výše uvedených příkladech předpokládejme, že každé oddělení má deset související kurzy. Předběžné načítání příklad způsobí pouze jednou (spojení) dotazu a jeden odezvy k databázi. Opožděné načtení a příklady explicitní načtení obě způsobí jedenáct dotazy a jedenáct zpátečních cest k databázi. Další zpátečních cest k databázi jsou zvlášť ztráty výkonu, když má vysokou latenci.

Na druhé straně v některých případech je efektivnější opožděné načtení. Předběžné načítání může vést k velmi složité spojení být vytvořen, kterou SQL Server nemůže zpracovat efektivně. Nebo pokud potřebujete přístup k entity navigační vlastnosti pouze pro dílčí sadu entit se zpracování, opožděné načtení může být lepší provést, protože nemůžou dočkat, až načítání by byl načten více dat, než potřebujete. Pokud je nejdůležitější výkon, je nejvhodnější pro testování výkonu oba způsoby, aby bylo možné správně se rozhodnout.

Opožděné načtení může zastínit kód, který způsobuje problémy s výkonem. Kód, který nemá určenou nemůžou dočkat, až nebo explicitní načtení, ale zpracovává velký počet entit a používá několik vlastností navigace v každé iteraci například může být velmi neefektivní (z důvodu počet zpátečních cest k databázi). Aplikace, který provádí i při vývoji pomocí SQL serveru na místní může mít problémy s výkonem při přesunu do Azure SQL Database z důvodu vyšší latence a opožděné načtení. Profiluje dotazy databáze s realistické zkušební zatížení vám pomůže určit, pokud opožděné načtení je vhodné. Další informace najdete v části [uvedení Entity Framework strategií: Načítání souvisejících dat](https://msdn.microsoft.com/magazine/hh205756.aspx) a [chcete snížit latenci sítě pro SQL Azure pomocí Entity Frameworku](https://msdn.microsoft.com/magazine/gg309181.aspx).

### <a name="disable-lazy-loading-before-serialization"></a>Zakázat před serializací opožděné načtení

Necháte-li opožděné načtení povoleno během serializace, můžete skončit dotazování podstatně víc dat, než jste zamýšleli. Serializace obecně funguje tak, že přístup k každou vlastnost v instanci typu. Přístup k vlastnostem aktivuje opožděné načtení a entit opožděné načíst serializují. Procesu serializace následně přistupuje k opožděné načtení entity potenciálně nezpůsobil další opožděné načtení a serializace se jednotlivé vlastnosti. Pokud chcete zabránit této run-away řetězové reakce, zapněte opožděné načtení vypnout před serializovat entity.

Serializace může také složité třídy proxy, které používá Entity Framework, jak je vysvětleno v [kurz pro pokročilé scénáře](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#proxies).

Jedním ze způsobů, aby nedocházelo k problémům serializace je určená k serializaci objektů pro přenos dat (DTO) namísto objekty entity, jak je znázorněno [pomocí webového rozhraní API s Entity Framework](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-5.md) kurzu.

Pokud nepoužíváte DTO, můžete zakázat opožděné načtení a nedocházelo k problémům s proxy službou [zakázání vytváření proxy](https://msdn.microsoft.com/data/jj592886.aspx).

Tady jsou některé další [způsoby, jak zakázat opožděné načtení](https://msdn.microsoft.com/data/jj574232):

- Pro konkrétní navigační vlastnosti vynechat, nechte `virtual` – klíčové slovo deklarovat vlastnost.
- Pro všechny vlastnosti navigace, nastavte `LazyLoadingEnabled` k `false`, vložte následující kód v konstruktoru třídy kontextu:

    [!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

## <a name="create-a-courses-page"></a>Vytvoření stránky kurzy

`Course` Entita obsahuje vlastnost navigace, která obsahuje `Department` entity, která je přiřazena celé oddělení. Chcete-li zobrazit název přiřazený oddělení v seznamu kurzů, je potřeba získat `Name` vlastnost z `Department` entity, která je v `Course.Department` navigační vlastnost.

Vytvoření řadiče s názvem `CourseController` (ne CoursesController) pro `Course` typ entity, pomocí stejných možností pro **kontroler MVC 5 se zobrazeními, používá nástroj Entity Framework** generátor, který jste provedli dříve pro `Student` kontroleru:

| Nastavení | Hodnota |
| ------- | ----- |
| Třída modelů | Vyberte **kurzu (ContosoUniversity.Models)**. |
| Třída kontextu dat | Vyberte **SchoolContext (ContosoUniversity.DAL)**. |
| Název řadiče | Zadejte *CourseController*. Znovu ne *CoursesController* s *s*. Pokud jste vybrali **kurzu (ContosoUniversity.Models)**, **názvu Kontroleru** hodnota se vyplní automaticky. Budete muset změnit hodnotu. |

Ponechte výchozí hodnoty a přidat kontroler.

Otevřít *Controllers\CourseController.cs* a podívejte se na `Index` metody:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Automatické generování uživatelského rozhraní má zadanou předběžné načítání pro `Department` navigační vlastnost s použitím `Include` metody.

Otevřít *Views\Course\Index.cshtml* a nahraďte kód šablony následujícím kódem. Změny jsou zvýrazněny:

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cshtml?highlight=4,7,14-16,23-25,31-33,40-42)]

Automaticky generovaný kód, které jste udělali následující změny:

- Změnit záhlaví z **Index** k **kurzy**.
- Přidá **číslo** sloupec, který ukazuje `CourseID` hodnotu vlastnosti. Ve výchozím nastavení nejsou automaticky generovaný primární klíče, protože jsou obvykle nemá význam pro koncové uživatele. Ale v tomto případě má smysl primární klíč a chcete zobrazit.
- Přesunout **oddělení** sloupec na pravou stranu a změnit jeho záhlaví. Scaffolder správně zvolili zobrazíte `Name` vlastnost z `Department` entity, ale tady na stránce kurzu by měl být na záhlaví sloupce **oddělení** spíše než **název**.

Všimněte si, že pro oddělení sloupců, automaticky generovaný kód zobrazí `Name` vlastnost `Department` entity, který je načten do `Department` navigační vlastnost:

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=2)]

Spuštění stránky (vyberte **kurzy** kartu na domovské stránce Contoso University) zobrazíte seznam s názvy oddělení.

## <a name="create-an-instructors-page"></a>Vytvoření stránky Instruktoři

V této části vytvoříte řadič a zobrazit `Instructor` entitu, aby bylo možné zobrazit stránku Instruktoři. Tato stránka načte a zobrazí související data následujícími způsoby:

- V seznamu instruktorů zobrazí související data z `OfficeAssignment` entity. `Instructor` a `OfficeAssignment` entity jsou ve vztahu k nule nebo jednom. Budete používat pro předběžné načítání `OfficeAssignment` entity. Jak jsme vysvětlili výše, předběžné načítání je obvykle mnohem efektivnější, když potřebujete související data pro všechny načtené řádky v tabulce primární. V takovém případě budete chtít zobrazit přiřazení office pro všechny zobrazené Instruktoři.
- Když uživatel vybere instruktorem, související `Course` entity jsou zobrazeny. `Instructor` a `Course` entity jsou v relaci m: m. Budete používat pro předběžné načítání `Course` entit a jejich související `Department` entity. Opožděné načtení v takovém případě může být mnohem efektivnější, protože potřebujete jenom pro vybrané kurzů vedených kurzů. Však tento příklad ukazuje způsob použití předběžné načítání pro navigační vlastnosti v rámci entity, které představují samy o sobě v navigační vlastnosti.
- Když uživatel vybere kurz, související data z `Enrollments` se zobrazí sada entit. `Course` a `Enrollment` entity jsou v vztah jeden mnoho. Přidejte explicitní načtení pro `Enrollment` entit a jejich související `Student` entity. (Explicitní načtení není nutný, protože je povolené opožděné načtení, ale to ukazuje, jak provést explicitní načtení.)

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Vytvoření modelu zobrazení pro zobrazení indexu instruktorem

Na stránce Instruktoři zobrazují třech různých tabulkách. Proto vytvoříte zobrazení modelu, který obsahuje tři vlastnosti každé obsahující data pro jednu z tabulek.

V *modely ViewModels* složku, vytvořte *InstructorIndexData.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Vytvoření Kontroleru instruktorem a zobrazení

Vytvoření `InstructorController` (ne InstructorsController) kontroler s EF čtení/zápis akce:

| Nastavení | Hodnota |
| ------- | ----- |
| Třída modelů | Vyberte **kurzů vedených (ContosoUniversity.Models)**. |
| Třída kontextu dat | Vyberte **SchoolContext (ContosoUniversity.DAL)**. |
| Název řadiče | Zadejte *InstructorController*. Znovu ne *InstructorsController* s *s*. Pokud jste vybrali **kurzu (ContosoUniversity.Models)**, **názvu Kontroleru** hodnota se vyplní automaticky. Budete muset změnit hodnotu. |

Ponechte výchozí hodnoty a přidat kontroler.

Otevřít *Controllers\InstructorController.cs* a přidejte `using` příkaz pro `ViewModels` obor názvů:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

Automaticky generovaný kód v `Index` určuje předběžné načítání pouze pro metody `OfficeAssignment` navigační vlastnost:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

Nahradit `Index` metody následující kód pro načtení další související data a vložit ho do modelu zobrazení:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

Metoda přijímá data volitelné trasy (`id`) a parametru řetězce dotazu (`courseID`), zadejte hodnoty ID vybrané instruktorem a vybrané kurzu a splní všechna požadovaná data do zobrazení. Parametry jsou poskytovány **vyberte** hypertextové odkazy na stránky.

Vytváření instance zobrazení modelu a jeho uvedením seznamu instruktorů začíná kód. Předběžné načítání pro Určuje kód `Instructor.OfficeAssignment` a `Instructor.Courses` navigační vlastnost.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs?highlight=3-4)]

Druhá `Include` metoda načte kurzy a jednotlivých kurzů, který je načten funguje předběžné načítání pro `Course.Department` navigační vlastnost.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

Jak už bylo zmíněno dříve, nemůžou dočkat, až načítání se nevyžadují, ale se provádí pro zvýšení výkonu. Protože zobrazení vždycky vyžaduje, aby `OfficeAssignment` entity, je efektivnější pro načtení, které ve stejném dotazu. `Course` entity jsou požadovány při výběru instruktorem na webové stránce, takže předběžné načítání je obecně lepší než opožděné načtení jenom v případě, že na stránce se zobrazí častěji kurzu vybrali než bez.

Pokud jste vybrali ID instruktorem, vybraných kurzů vedených je načten ze seznamu školitelů v modelu zobrazení. Model zobrazení `Courses` vlastnost je pak načten pomocí `Course` entity z této kurzů vedených `Courses` navigační vlastnost.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

`Where` Metoda vrátí kolekci, ale v tomto případě kritéria předat výsledek této metody v jedné `Instructor` entity se vrací. `Single` Metoda převede kolekci do jednoho `Instructor` entity, která umožňuje přístup k dané entity `Courses` vlastnost.

Můžete použít [jeden](https://msdn.microsoft.com/library/system.linq.enumerable.single.aspx) metody na kolekci, když víte, kolekce budou mít pouze jednu položku. `Single` Metoda vyvolá výjimku, pokud do něho předaný kolekce je prázdná, nebo pokud existuje více než jednu položku. Alternativou je [SingleOrDefault](https://msdn.microsoft.com/library/bb342451.aspx), která vrátí výchozí hodnotu (`null` v tomto případě) Pokud kolekce je prázdná. Ale v tomto případě bude stále výsledkem výjimku (z pokusu o nalezení `Courses` vlastnosti `null` odkaz), a zpráva o výjimce by méně jasně ukazovat na příčinu problému. Při volání `Single` metodu, můžete také předat `Where` podmínku namísto volání metody `Where` metoda samostatně:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cs)]

Namísto:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

Dále pokud jste vybrali kurz, vybrané kurzu se načte z seznam kurzů v zobrazení modelu. Potom zobrazení modelu `Enrollments` vlastnosti je načtena s `Enrollment` entity z tohoto kurzu `Enrollments` navigační vlastnost.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs)]

### <a name="modify-the-instructor-index-view"></a>Úprava zobrazení indexu instruktorem

V *Views\Instructor\Index.cshtml*, nahraďte kód šablony následujícím kódem. Změny jsou zvýrazněny:

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cshtml?highlight=1,4,14-18,21,23-28,38-43,45)]

Stávající kód, které jste udělali následující změny:

- Změnit třídu modelu `InstructorIndexData`.
- Změnil se název stránky z **Index** k **Instruktoři**.
- Přidá **Office** sloupec, který zobrazuje `item.OfficeAssignment.Location` pouze tehdy, pokud `item.OfficeAssignment` nemá hodnotu null. (Protože je to vztah jeden: nula nebo 1, nemusí být se souvisejícím `OfficeAssignment` entity.)

    [!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cshtml)]
- Přidání kódu, která dynamicky přidá `class="success"` k `tr` element vybraných kurzů vedených. Tím se nastaví barvu pozadí vybraného řádku s využitím [Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap) třídy.

    [!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml)]
- Přidat nový `ActionLink` označené jako **vyberte** bezprostředně před další odkazy na každém řádku, což způsobí, že ID vybraných kurzů vedených k odeslání do `Index` metoda.

Spusťte aplikaci a vyberte **Instruktoři** kartu. Na stránce se zobrazí `Location` související vlastnost `OfficeAssignment` entit a prázdné tabulky buňky při žádné související `OfficeAssignment` entity.

V *Views\Instructor\Index.cshtml* soubor po zavření `table` – element (na konci souboru), přidejte následující kód. Tento kód zobrazí seznam kurzů související s instruktorem, pokud je vybrána instruktorem.

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cshtml)]

Tento kód čte `Courses` vlastnost model zobrazení zobrazíte seznam kurzů. Poskytuje také `Select` hypertextový odkaz, který odesílá ID vybrané kurz `Index` metody akce.

Spustit na stránku a vybrat instruktorem. Nyní uvidíte tabulku, která zobrazuje kurzy přiřazen k vybrané instruktorem a jednotlivých kurzů se zobrazí název přiřazený oddělení.

Za blok kódu, který jste právě přidali přidejte následující kód. Zobrazí seznam studentů, kteří se zaregistrují v kurzu při výběru tohoto kurzu.

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml)]

Tento kód čte `Enrollments` vlastnost model zobrazení, aby bylo možné zobrazit seznam studentů zaregistrovaný do kurzu.

Spustit na stránku a vybrat instruktorem. Vyberte kurzu zobrazíte seznam registrovaná studentů a jejich kvality.

### <a name="adding-explicit-loading"></a>Přidání explicitní načtení

Otevřít *InstructorController.cs* a podívejte se jak `Index` metoda načte seznam registrace pro vybrané kurzu:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs)]

Když jste získali seznam školitelů, jste zadali předběžné načítání pro `Courses` navigační vlastnost a `Department` vlastnost jednotlivých kurzů. Pak vložíte `Courses` kolekce v modelu zobrazení a nyní získáváte přístup k `Enrollments` navigační vlastnost z entit v této kolekci. Protože jste neurčili, nevložily předběžné načítání pro `Course.Enrollments` navigační vlastnost, data z dané vlastnosti se zobrazuje na stránce v důsledku opožděné načtení.

Pokud jste zakázali opožděné načtení beze změny kódu žádným jiným způsobem, `Enrollments` vlastnost by bez ohledu na to, kolik registrace na kurz ve skutečnosti měla hodnotu null. V takovém případě se načíst `Enrollments` vlastnost, je potřeba zadat předběžné načítání nebo explicitní načtení. Už jste viděli postup předběžné načítání. Chcete-li zobrazit příklad explicitní načtení, nahraďte `Index` metodu s následujícím kódem, který explicitně načte `Enrollments` vlastnost. Zvýrazní se kód změnil.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cs?highlight=20-31)]

Po získání vybrané `Course` entity, nový kód explicitně načte tento kurz `Enrollments` navigační vlastnost:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs)]

Pak explicitně načte každý `Enrollment` týkající entity `Student` entity:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cs)]

Všimněte si, že používáte `Collection` metoda načíst vlastnost kolekce, ale pro vlastnost, která obsahuje pouze jednu entitu, můžete použít `Reference` metody.

Spustit kurzů vedených indexovou stránku a zobrazí se vám nijak neliší obsah zobrazený na stránce, i když jste změnili, jak načíst data.

## <a name="get-the-code"></a>Získat kód

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Další zdroje

Odkazy na další zdroje Entity Framework najdete v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zjistili jste, jak načíst související data
> * Vytvoření stránky kurzy
> * Vytvoří stránku Instruktoři

Přejděte k dalším článku se naučíte, jak aktualizovat související data.

> [!div class="nextstepaction"]
> [Aktualizace souvisejících dat](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)