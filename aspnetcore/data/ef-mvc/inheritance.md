---
title: 'Kurz: Implementace dědičnosti – ASP.NET MVC s EF Core'
description: Tento kurz vám ukáže, jak implementovat dědičnost v datovém modelu pomocí entity framework core v aplikaci ASP.NET core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/inheritance
ms.openlocfilehash: dab3d2b057162f6d986db10e74e3681acc0ada3b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657239"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a>Kurz: Implementace dědičnosti – ASP.NET MVC s EF Core

V předchozím kurzu jste zpracovali výjimky souběžnosti. Tento kurz vám ukáže, jak implementovat dědičnost v datovém modelu.

V objektově orientovaném programování můžete použít dědičnost k usnadnění opakovaného použití kódu. V tomto kurzu změníte `Instructor` a `Student` třídy tak, `Person` aby byly odvozeny `LastName` ze základní třídy, která obsahuje vlastnosti, jako jsou společné pro instruktory i studenty. Nebudete přidávat ani měnit žádné webové stránky, ale změníte některé kódy a tyto změny se automaticky projeví v databázi.

V tomto kurzu jste:

> [!div class="checklist"]
> * Mapování dědičnosti do databáze
> * Vytvoření třídy Osoba
> * Aktualizovat instruktora a studenta
> * Přidat osobu do modelu
> * Vytváření a aktualizace migrací
> * Otestujte implementaci

## <a name="prerequisites"></a>Požadavky

* [Souběžnost popisovače](concurrency.md)

## <a name="map-inheritance-to-database"></a>Mapování dědičnosti do databáze

A `Instructor` `Student` třídy v datovém modelu škola mají několik vlastností, které jsou identické:

![Třídy studentů a instruktorů](inheritance/_static/no-inheritance.png)

Předpokládejme, že chcete odstranit redundantní kód pro `Instructor` `Student` vlastnosti, které jsou sdíleny a entity. Nebo chcete napsat službu, která může formátovat názvy bez ohledu na to, zda jméno pochází od instruktora nebo studenta. Můžete vytvořit `Person` základní třídu, která obsahuje pouze `Instructor` tyto `Student` sdílené vlastnosti, pak provést a třídy dědí z této základní třídy, jak je znázorněno na následujícím obrázku:

![Třídy studentů a instruktorů odvozené z třídy Osoba](inheritance/_static/inheritance.png)

Existuje několik způsobů, jak tato struktura dědičnosti může být reprezentována v databázi. V jedné tabulce můžete mít tabulku Osoba, která obsahuje informace o studentech i instruktorech. Některé sloupce se mohou vztahovat pouze na instruktory (HireDate), některé pouze pro studenty (EnrollmentDate), některé na oba (LastName, FirstName). Obvykle byste měli diskriminátor sloupec označující, který typ každý řádek představuje. Například diskriminátor sloupec může mít "Instruktor" pro instruktory a "Student" pro studenty.

![Příklad tabulky na hierarchii](inheritance/_static/tph.png)

Tento vzor generování struktury dědičnosti entity z jedné databázové tabulky se nazývá dědičnost tabulky na hierarchii (TPH).

Alternativou je, aby databáze vypadat spíše jako strukturu dědičnosti. Můžete mít například pouze pole název v tabulce Osoba a samostatné tabulky Instruktor a Student s poli data.

![Dědičnost tabulky na typ](inheritance/_static/tpt.png)

Tento vzor vytváření databázové tabulky pro každou třídu entity se nazývá dědičnost tabulky podle typu (TPT).

Další možností je mapování všech neabstraktních typů na jednotlivé tabulky. Všechny vlastnosti třídy, včetně zděděných vlastností, mapovat na sloupce odpovídající tabulky. Tento vzor se nazývá Table-per-Concrete Class (TPC) dědičnost. Pokud jste implementovali dědičnost TPC pro třídy Osoba, Student a Instruktor, jak je uvedeno výše, tabulky Student a Instruktor by po implementaci dědičnosti nevypadaly jinak než dříve.

Vzory dědičnosti TPC a TPH obecně poskytují lepší výkon než vzory dědičnosti TPT, protože vzory TPT mohou mít za následek složité dotazy spojení.

Tento kurz ukazuje, jak implementovat dědičnost TPH. TPH je jediný způsob dědičnosti, který podporuje jádro entity frameworku.  Co uděláte, je `Person` vytvořit třídu, změnit třídy `Instructor` a `Student` odvozené z `Person`, přidat novou třídu do `DbContext`, a vytvořit migraci.

> [!TIP]
> Před provedením následujících změn zvažte uložení kopie projektu.  Pak, pokud narazíte na problémy a je třeba začít znovu, bude snazší začít z uloženého projektu namísto obrácení kroků provedených pro tento kurz nebo návratu na začátek celé řady.

## <a name="create-the-person-class"></a>Vytvoření třídy Osoba

Ve složce Modely vytvořte Person.cs a nahraďte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a>Aktualizovat instruktora a studenta

V *Instructor.cs*odvodit třídu Instruktor z třídy Osoba a odebrat pole klíče a názvu. Kód bude vypadat jako následující příklad:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

Proveďte stejné změny v *Student.cs*.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a>Přidat osobu do modelu

Přidejte typ entity Osoba do *SchoolContext.cs*. Nové řádky jsou zvýrazněny.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

To je vše, co rozhraní entity framework potřebuje ke konfiguraci dědičnosti tabulky na hierarchii. Jak uvidíte, při aktualizaci databáze bude mít místo tabulek studenta a instruktora tabulku osoba.

## <a name="create-and-update-migrations"></a>Vytváření a aktualizace migrací

Uložte změny a vytvořte projekt. Potom otevřete příkazové okno ve složce projektu a zadejte následující příkaz:

```dotnetcli
dotnet ef migrations add Inheritance
```

Ještě nespouštějte `database update` příkaz. Tento příkaz bude mít za následek ztrátu dat, protože klesne instruktor tabulky a přejmenovat studenta tabulka na osobu. Chcete-li zachovat existující data, je třeba zadat vlastní kód.

Otevřete *migrace/\<časové razítko>_Inheritance.cs* a nahraďte metodu `Up` následujícím kódem:

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

Tento kód se postará o následující úlohy aktualizace databáze:

* Odebere omezení cizího klíče a indexy, které odkazují na tabulku Student.

* Přejmenuje tabulku Instruktor jako osoba a provede změny potřebné k ukládání dat studentů:

* Přidá datum, které lze utnou tzv.

* Přidá sloupec Diskriminátor označující, zda je řádek pro studenta nebo instruktora.

* Umožňuje hireDate nullable protože řádky studenta nebude mít data přijetí.

* Přidá dočasné pole, které bude použito k aktualizaci cizích klíčů, které odkazují na studenty. Když studenty zkopírujete do tabulky Osoba, získají nové hodnoty primárního klíče.

* Zkopíruje data z tabulky Student do tabulky Osoba. To způsobí, že studentům budou přiřazeny nové hodnoty primárního klíče.

* Opravuje hodnoty cizího klíče, které odkazují na studenty.

* Znovu vytvoří omezení cizího klíče a indexy, nyní s měnou na osobu tabulky.

(Pokud jste jako typ primárního klíče použili identifikátor GUID místo celého čísla, hodnoty primárního klíče studenta by se nemusely měnit a několik těchto kroků mohlo být vynecháno.)

Spusťte `database update` příkaz:

```dotnetcli
dotnet ef database update
```

(V produkčním systému byste provedli odpovídající změny `Down` metody v případě, že byste ji někdy museli použít k návratu k předchozí verzi databáze. Pro tento kurz nebudete používat `Down` metodu.)

> [!NOTE]
> Je možné získat další chyby při provádění změn schématu v databázi, která má existující data. Pokud se vám budou zobrazovat chyby migrace, které nelze vyřešit, můžete změnit název databáze v připojovacím řetězci nebo databázi odstranit. S novou databází nejsou k dispozici žádná data k migraci a příkaz aktualizovat databázi je pravděpodobnější, že bude dokončen bez chyb. Chcete-li databázi odstranit, použijte `database drop` příkaz SSOX nebo spusťte příkaz CLI.

## <a name="test-the-implementation"></a>Otestujte implementaci

Spusťte aplikaci a vyzkoušejte různé stránky. Všechno funguje stejně jako předtím.

V **Průzkumníku objektů SQL Server**rozbalte **položku Data Connections/SchoolContext** a potom **tabulky**a uvidíte, že tabulky Student a Instruktor byly nahrazeny tabulkou Osoba. Otevřete návrháře tabulek Osoba a uvidíte, že obsahuje všechny sloupce, které bývaly v tabulkách Student a Instruktor.

![Osobní stůl ve SSOX](inheritance/_static/ssox-person-table.png)

Klikněte pravým tlačítkem myši na tabulku Osoba a potom kliknutím na **Zobrazit data tabulky** zobrazte sloupec diskriminátoru.

![Tabulka osob ve SSOX - tabulkové údaje](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a>Získání kódu

[Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Další zdroje

Další informace o dědičnosti v jádru entity frameworku naleznete v [tématu Dědičnost](/ef/core/modeling/inheritance).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Mapovaná dědičnost do databáze
> * Vytvoření třídy Osoba
> * Aktualizovaný instruktor a student
> * Přidána osoba do modelu
> * Vytvořené a aktualizovat migrace
> * Testováno provádění

Přejdete k dalšímu kurzu, kde se dozvíte, jak zpracovat různé relativně pokročilé scénáře entity frameworku.

> [!div class="nextstepaction"]
> [Další: Pokročilá témata](advanced.md)
