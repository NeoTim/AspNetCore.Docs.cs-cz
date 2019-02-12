---
title: 'Kurz: Implementace dědičnosti – ASP.NET MVC s EF Core'
description: V tomto kurzu se seznámíte implementace dědičnosti v datovém modelu, s použitím Entity Framework Core v aplikaci ASP.NET Core.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/05/2019
ms.topic: tutorial
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 0a5eb1aba43bc2adf746202772c7f98eff49b4ff
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56103004"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a>Kurz: Implementace dědičnosti – ASP.NET MVC s EF Core

V předchozím kurzu zpracování výjimky souběžnosti. Tento kurzu se dozvíte, jak implementovat dědičnosti v datovém modelu.

V objektově orientované programování, můžete dědičnost usnadňuje opakované využívání kódu. V tomto kurzu změníte `Instructor` a `Student` tak, že jsou odvozeny z třídy `Person` základní třída, která obsahuje vlastnosti, například `LastName` , které jsou společné pro vyučující a studenty. Nebude přidat nebo změnit libovolné webové stránky, ale změníte kód, a tyto změny se automaticky projeví v databázi.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Mapovat dědičnosti do databáze
> * Vytvořte třídu osoby
> * Aktualizace instruktorem a studentů
> * Do modelu přidat osoby
> * Vytvoření a aktualizaci migrace
> * Provedení testu

## <a name="prerequisites"></a>Požadavky

* [Popisovač souběžnosti s EF Core ve webové aplikaci ASP.NET Core MVC](concurrency.md)

## <a name="map-inheritance-to-database"></a>Mapovat dědičnosti do databáze

`Instructor` a `Student` třídy v datovém modelu školy mají několik vlastností, které jsou shodné:

![Třídy pro studenty a instruktorem](inheritance/_static/no-inheritance.png)

Předpokládejme, že chcete vyloučit redundantní kód pro vlastnosti, které jsou sdíleny `Instructor` a `Student` entity. Nebo chcete zadat službu, která dokáže formátovat názvy bez caring, zda název pochází instruktorem nebo student. Můžete vytvořit `Person` základní třída, která obsahuje pouze ty sdílené vlastnosti a pak proveďte `Instructor` a `Student` třídy dědí ze základní třídy, jak je znázorněno na následujícím obrázku:

![Pro studenty a kurzů vedených třídy odvozené od třídy osoby](inheritance/_static/inheritance.png)

Existuje několik způsobů, které tato struktura dědičnosti můžou být reprezentované v databázi. Můžete mít tabulku osoba, která obsahuje informace o studenty a vyučující v jediné tabulce. Některé sloupce může použít pouze na Instruktoři (HireDate), některé jenom pro studenty (EnrollmentDate), některé na obě (LastName, FirstName). Obvykle museli jste sloupec diskriminátoru, který označuje, který typ každý řádek představuje. Pro sloupec diskriminátoru může mít například "Kurzů vedených" pro vyučující a "Studentů" pro studenty.

![Příklad tabulky podle hierarchie](inheritance/_static/tph.png)

Tento model generování struktury dědičnosti entity z tabulky izolované databáze se nazývá dědičnosti na hierarchii tabulky (TPH).

Další možností je databázi tak, aby vypadat více jako struktury dědičnosti. Například může obsahovat pouze název pole v tabulce osoby a mít samostatné instruktorem a studentů tabulky s poli datum.

![Dědičnost za typ tabulky](inheritance/_static/tpt.png)

Tento model vytváření tabulky databáze pro každou třídu entity se nazývá tabulka na jeden typ (TPT) dědičnosti.

Další možností je ještě všechny typy neabstraktní namapovat jednotlivé tabulky. Všechny vlastnosti třídy, včetně zděděné vlastnosti mapovat na sloupce příslušné tabulky. Tento model se nazývá dědičnost tabulky na konkrétní třídy (TPC). Pokud jste implementovali TPC dědičnosti pro osobu, studenty a kurzů vedených třídy, jak je uvedeno výše, by vypadalo tabulky studentů a kurzů vedených nijak neliší po implementaci dědičnosti než dříve.

TPC a TPH vzory dědičnosti obecně doručit lepší výkon než vzory TPT dědičnosti, protože TPT vzory může vést k dotazům komplexní spojení.

Tento kurz ukazuje, jak implementovat TPH dědičnosti. TPH je pouze dědičnosti vzor, který podporuje Entity Framework Core.  Co můžete udělat, je vytvořit `Person` tříd, změnit `Instructor` a `Student` třídy, které jsou odvozeny z `Person`, přidejte novou třídu do `DbContext`a vytvořit migrace.

> [!TIP]
> Zvažte možnost uložení kopie projektu před provedením následující změny.  Pak pokud narazíte na problémy a potřebujete začít znovu, ji bude snazší spuštění z projektu uložený místo vrácení kroků v tomto kurzu nebo že přejdete zpět na začátek celou řadu.

## <a name="create-the-person-class"></a>Vytvořte třídu osoby

Ve složce modely vytvořit Person.cs a nahraďte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a>Aktualizace instruktorem a studentů

V *Instructor.cs*, kurzů vedených třída odvozena od třídy osoby a odstraňte klíč a název pole. Kód bude vypadat jako v následujícím příkladu:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

Provést stejné změny v *Student.cs*.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a>Do modelu přidat osoby

Přidat typ entity osoby k *SchoolContext.cs*. Nové řádky jsou zvýrazněné.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

To je vše, Entity Framework potřebuje, aby konfigurace tabulky na hierarchii dědičnosti. Jak zjistíte, když se aktualizuje databázi, bude mít tabulku osoba místo tabulky studentů a instruktorem.

## <a name="create-and-update-migrations"></a>Vytvoření a aktualizaci migrace

Uložte změny a sestavte projekt. Potom otevřete okno příkazového řádku ve složce projektu a zadejte následující příkaz:

```console
dotnet ef migrations add Inheritance
```

Při spuštění `database update` ještě příkazu. Tento příkaz způsobí nedošlo ke ztrátě dat. vzhledem k tomu, že bude tabulku instruktorem a přejmenovat Tabulka Student osobě. Budete muset zadat vlastní kód, chcete-li zachovat existující data.

Otevřít *migrace /\<časové razítko > _Inheritance.cs* a nahraďte `Up` metodu s následujícím kódem:

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

Tento kód se postará o tyto úlohy aktualizace databáze:

* Odebere omezení cizího klíče a indexy, které odkazují na tabulce studentů.

* Přejmenuje tabulce kurzů vedených jako osoba a provede změny, které jsou potřeba, aby se uložení údajů studentů:

* Přidá EnrollmentDate s možnou hodnotou Null pro studenty.

* Přidá sloupec diskriminátoru k označení, zda je řádek student nebo instruktorem.

* Protože student řádků nebude mít dat, díky HireDate s možnou hodnotou Null.

* Přidá dočasné pole, který se použije k aktualizaci cizí klíče, které odkazují na studenty. Při kopírování studenty do tabulky osob dostanou nové hodnoty primárního klíče.

* Kopíruje data z tabulky Student do tabulky osob. To způsobí, že studenti mohli přiřadit nové hodnoty primárního klíče.

* Opravy hodnoty cizího klíče, které odkazují na studenty.

* Znovu vytvoří omezení cizího klíče a indexy, nyní je odkazující na tabulku osoby.

(Namísto celého čísla byste použili identifikátor GUID jako typ primárního klíče, student hodnoty primárního klíče nemusí měnit a některé z těchto kroků může vynechat.)

Spustit `database update` příkaz:

```console
dotnet ef database update
```

(V produkční systém by proveďte příslušné změny `Down` metodu v případě někdy museli vrátit k předchozí verzi databáze použít. V tomto kurzu nebudete používat `Down` metoda.)

> [!NOTE]
> Je možné získat další chyby při provedení změn schématu v databázi, která obsahuje už existující data. Pokud se zobrazí chyby při migraci, které nelze vyřešit, můžete změnit název databáze v připojovacím řetězci nebo odstranit databázi. S novou databázi nejsou žádná data k migraci a příkazu update databáze má větší pravděpodobnost dokončí bez chyb. Pokud chcete odstranit databázi, použijte SSOX nebo spusťte `database drop` příkazu rozhraní příkazového řádku.

## <a name="test-the-implementation"></a>Provedení testu

Spusťte aplikaci a vyzkoušejte různé stránky. Všechno, co funguje stejně jako předtím.

V **Průzkumník objektů systému SQL Server**, rozbalte **datového připojení/SchoolContext** a potom **tabulky**, a uvidíte, že byly nahrazeny tabulky studentů a instruktorem Tabulka osoby. Otevření Návrháře tabulky osoby a uvidíte, že obsahuje všechny sloupce, které mají být používány v tabulkách studentů a kurzů vedených.

![Tabulky osob v SSOX](inheritance/_static/ssox-person-table.png)

Klikněte pravým tlačítkem na tabulku osoba a potom klikněte na **zobrazit Data tabulky** zobrazit sloupec diskriminátoru.

![Osoba tabulky v SSOX - tabulkových dat](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Další zdroje

Další informace o dědičnosti v Entity Framework Core najdete v tématu [dědičnosti](/ef/core/modeling/inheritance).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Mapovaná dědičnosti do databáze
> * Vytvoření třídy osoby
> * Aktualizované instruktorem a studentů
> * Přidání, kdo modelu
> * Vytvoření a aktualizaci migrace
> * Testování implementace

Přejděte k dalším článku se dozvíte, jak zpracovat širokou škálu relativně pokročilé scénáře rozhraní Entity Framework.
> [!div class="nextstepaction"]
> [Pokročilá témata](advanced.md)
