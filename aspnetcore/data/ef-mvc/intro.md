---
title: 'Kurz: Začínáme s EF Core ve webové aplikaci ASP.NET MVC'
description: Toto je první v sérii kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University od začátku.
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: 3450ac5b46e2a03b5d58c8760b78a52065343992
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985363"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Kurz: Začínáme s EF Core ve webové aplikaci ASP.NET MVC

::: moniker range=">= aspnetcore-3.0"

Tento kurz se neaktualizoval na ASP.NET Core 3,0. [Verze Razor Pages](xref:data/ef-rp/intro) byla aktualizována. Informace o tom, kdy je možné aktualizaci aktualizovat, najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/13920).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Ukázková webová aplikace společnosti Contoso University ukazuje, jak vytvářet webové aplikace ASP.NET Core 2,2 MVC pomocí Entity Framework (EF) Core 2,2 a Visual Studio 2017 nebo 2019.

Ukázková aplikace je web pro fiktivní univerzitě společnosti Contoso. Zahrnuje funkce, jako student přijetí, kurz vytvoření a přiřazení instruktorem. Toto je první v sérii kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University od začátku.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření webové aplikace ASP.NET Core MVC
> * Nastavit styl lokality
> * Další informace o EF Core balíčcích NuGet
> * Vytvoření datového modelu
> * Vytvoření kontextu databáze
> * Registrace kontextu pro vkládání závislostí
> * Inicializace databáze s testovacími daty
> * Vytvoření kontroleru a zobrazení
> * Zobrazení databáze

## <a name="prerequisites"></a>Požadavky

* [.NET Core SDK 2,2](https://www.microsoft.com/net/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s následujícími úlohami:
  * **ASP.NET a webové vývojové** úlohy
  * **Vývojová úloha .NET Core pro různé platformy**

## <a name="troubleshooting"></a>Poradce při potížích

Pokud narazíte na problém nevyřešíte sami, můžete najít řešení obvykle porovnáním kódu [dokončený projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final). Seznam běžných chyb a jejich řešení najdete v [části věnované řešení potíží v posledním kurzu v řadě](advanced.md#common-errors). Pokud tam nenajdete, co potřebujete, můžete odeslat otázku do StackOverflow.com pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Toto je série 10 kurzů, z nichž každá sestaví na tom, co se děje v předchozích kurzech. Zvažte uložení kopie projektu po každém úspěšném dokončení kurzu. Pak Pokud narazíte na problémy, můžete začít znovu z předchozího kurzu a nemusíte se vrátit na začátek celé řady.

## <a name="contoso-university-web-app"></a>Webová aplikace Contoso University

Aplikace, kterou budete sestavovat v těchto kurzech, je jednoduchý web na univerzitě.

Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem. Tady je několik obrazovek, které vytvoříte.

![Studenti indexová stránka](intro/_static/students-index.png)

![Stránky pro úpravu studentů](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Vytvoření webové aplikace

* Otevřít Visual Studio.

* V nabídce **soubor** vyberte **Nový > projekt**.

* V levém podokně vyberte **nainstalované > Visual C# > Web**.

* Vyberte šablonu projektu **ASP.NET Core webové aplikace** .

* Jako název zadejte **ContosoUniversity** a klikněte na **OK**.

  ![Dialogové okno nového projektu](intro/_static/new-project2.png)

* Počkejte, než se zobrazí dialogové okno **nové webové aplikace ASP.NET Core** .

* Vyberte **.NET Core**, **ASP.NET Core 2,2** a šablonu **Webová aplikace (model-zobrazení-kontroler)** .

* Ujistěte se, že je **ověřování** nastaveno na **bez ověřování**.

* Vybrat **OK**

  ![Dialog nového projektu ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Nastavit styl lokality

V několika jednoduchých změnách se nastaví nabídka web, rozložení a Domovská stránka.

Otevřete *views/Shared/_Layout. cshtml* a proveďte následující změny:

* Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso". Existují tři výskyty.

* Přidejte položky nabídky pro, **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **osobních údajů** .

Změny jsou zvýrazněné.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

V *zobrazeních/Home/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Stisknutím kombinace kláves CTRL + F5 spusťte projekt nebo zvolte možnost **ladění > spustit bez ladění** z nabídky. Zobrazí se stránka domů s kartami pro stránky, které vytvoříte v těchto kurzech.

![Domovská stránka společnosti Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>Informace o EF Core balíčků NuGet

Chcete-li přidat do projektu podporu EF Core, nainstalujte poskytovatele databáze, na který chcete cílit. V tomto kurzu se používá SQL Server a balíček poskytovatele je [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Tento balíček je zahrnutý ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), takže nemusíte odkazovat na balíček.

Balíček EF SQL Server a jeho závislosti (`Microsoft.EntityFrameworkCore` a `Microsoft.EntityFrameworkCore.Relational`) poskytují podporu modulu runtime pro EF. Přidáte balíček nástrojů později v kurzu [migrace](migrations.md) .

Informace o dalších poskytovatelích databází, které jsou k dispozici pro Entity Framework Core, najdete v tématu [poskytovatelé databáze](/ef/core/providers/).

## <a name="create-the-data-model"></a>Vytvoření datového modelu

V dalším kroku vytvoříte třídy entit pro aplikaci Contoso University. Začnete s následujícími třemi entitami.

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

Mezi `Student` entitami a `Enrollment` entitami existuje vztah 1: n a mezi `Course` entitami a `Enrollment` entitami je vztah 1:1. Jinými slovy, student může být zaregistrovaný v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.

V následujících částech vytvoříte třídu pro každou z těchto entit.

### <a name="the-student-entity"></a>Entita studenta

![Diagram entity studenta](intro/_static/student-entity.png)

Ve složce *modely* vytvořte soubor třídy s názvem *student.cs* a nahraďte kód šablony následujícím kódem.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID` Vlastnost se změní na sloupec primárního klíče tabulky databáze, který odpovídá této třídě. Ve výchozím nastavení Entity Framework interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.

`Enrollments` Je vlastnost [navigační vlastnost](/ef/core/modeling/relationships). Navigační vlastnosti obsahují další entity, které se vztahují k této entitě. V `Enrollments` takovém případě `Student entity` `Student` bude vlastnost objektu obsahovat všechny entity,kterésevztahujíkdanéentitě.`Enrollment` Jinými slovy, pokud daný řádek studenta v databázi obsahuje dva související řádky zápisu (řádky, které obsahují hodnotu primárního klíče tohoto studenta ve sloupci StudentID cizí klíč), vlastnost `Student` `Enrollments` navigace této entity bude obsahovat. dvě `Enrollment` entity.

Pokud navigační vlastnost může obsahovat více entit (jako v relacích m:n nebo 1:1), musí se jednat o seznam, ve kterém lze přidávat, odstraňovat a aktualizovat položky, jako `ICollection<T>`je například. Můžete zadat `ICollection<T>` nebo typ `List<T>` jako nebo `HashSet<T>`. Pokud zadáte `ICollection<T>`, EF `HashSet<T>` vytvoří ve výchozím nastavení kolekci.

### <a name="the-enrollment-entity"></a>Registrace entity

![Diagram entity registrace](intro/_static/enrollment-entity.png)

Ve složce *modely* vytvořte *Enrollment.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

Vlastnost bude primární klíč. Tato entita `classnameID` používá vzor místo toho `ID` , jak jste viděli v `Student` entitě. `EnrollmentID` Obvykle byste zvolili jeden model a používali ho v rámci svého datového modelu. V tomto příkladu variace znázorňuje, že můžete použít libovolný vzor. V [pozdějším kurzu](inheritance.md)uvidíte, jak použít ID bez ClassName, usnadňuje implementaci dědičnosti v datovém modelu.

`Grade` Vlastnost je `enum`. Otazník po `Grade` deklarace typu znamená, že `Grade` vlastnost může mít hodnotu Null. Na podnikové úrovni, který má hodnotu null se liší od nulové třída – null znamená, že známku vyjádřenou není znám nebo ještě nebyly přiřazeny.

`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`. `Student` `Student.Enrollments` `Enrollment` Entita je přidružená k jedné `Student` entitě, takže vlastnost může uchovávat jenom jednu entitu (na rozdíl od vlastnosti navigace, kterou jste viděli dříve, která může obsahovat víc entit). `Enrollment`

`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`. `Enrollment` Entita je přidružený nejméně k jednomu `Course` entity.

Entity Framework interpretuje vlastnost jako vlastnost cizího klíče, pokud má název `<navigation property name><primary key property name>` (například pro `Student` vlastnost navigace `StudentID` , protože `Student` primární klíč entity je `ID`). Vlastnosti cizího klíče lze také pojmenovat `<primary key property name>` jednoduše (například vzhledem `CourseID` `Course` k tomu, že primární klíč entity `CourseID`je).

### <a name="the-course-entity"></a>Kurz entity

![Diagram kurzu entity](intro/_static/course-entity.png)

Ve složce *modely* vytvořte *Course.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments` Je navigační vlastnost. A `Course` entit může souviset s libovolným počtem `Enrollment` entity.

V [pozdějším kurzu](complex-data-model.md) v této `DatabaseGenerated` sérii zaznamenáme Další informace o atributu. V podstatě vám tento atribut umožňuje zadat primární klíč pro kurz místo toho, aby ho databáze vygenerovala.

## <a name="create-the-database-context"></a>Vytvoření kontextu databáze

Hlavní třída, která koordinuje funkce Entity Framework pro daný datový model, je třída kontextu databáze. Tuto třídu vytvoříte odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy. Ve vašem kódu určíte, které entity budou zahrnuty v datovém modelu. Můžete také přizpůsobit určité chování Entity Framework. V tomto projektu je s názvem třídy `SchoolContext`.

Ve složce projektu vytvořte složku s názvem *data*.

Ve složce *data* vytvořte nový soubor třídy s názvem *SchoolContext.cs*a nahraďte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Tento kód vytvoří `DbSet` vlastnost pro každou sadu entit. V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.

Mohli jste vynechat `DbSet<Enrollment>` příkazy a `DbSet<Course>` a to by fungovalo stejně. Entity Framework by je implicitně zahrnuly, protože `Student` entita `Enrollment` odkazuje na entitu `Course` a `Enrollment` odkazuje na entitu.

Při vytvoření databáze EF vytvoří tabulky, které mají názvy stejné jako `DbSet` názvy vlastností. Názvy vlastností pro kolekce jsou obvykle plural (Students spíše než student), ale vývojáři nesouhlasí, zda by měly být názvy tabulek v množném čísle. V těchto kurzech potlačíte výchozí chování zadáním názvů tabulek na jednotném čísle v DbContext. K tomu přidejte následující zvýrazněný kód za poslední vlastnost Negenerickými.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Zaregistrovat SchoolContext

ASP.NET Core implementuje [vkládání závislostí](../../fundamentals/dependency-injection.md) ve výchozím nastavení. Služby (jako kontext databáze EF) jsou registrovány pomocí injektáže závislosti během spuštění aplikace. Komponenty, které vyžadují tyto služby (například řadiče MVC), poskytují tyto služby prostřednictvím parametrů konstruktoru. V tomto kurzu se zobrazí kód konstruktoru kontroleru, který získá instanci kontextu později.

Pokud se `SchoolContext` chcete zaregistrovat jako služba, otevřete *Startup.cs*a přidejte `ConfigureServices` zvýrazněné řádky do metody.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

Název připojovacího řetězce je předán do kontextu voláním metody pro `DbContextOptionsBuilder` objekt. Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.

Přidejte `using` příkazy pro `ContosoUniversity.Data` a `Microsoft.EntityFrameworkCore` obory názvů a potom Sestavte projekt.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Otevřete soubor *appSettings. JSON* a přidejte připojovací řetězec, jak je znázorněno v následujícím příkladu.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Připojovací řetězec určuje SQL Server databázi LocalDB. LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí. LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace. Ve výchozím nastavení LocalDB vytvoří soubory databáze *. mdf* v `C:/Users/<user>` adresáři.

## <a name="initialize-db-with-test-data"></a>Inicializovat databázi s testovacími daty

Entity Framework pro vás vytvoří prázdnou databázi. V této části napíšete metodu, která je volána po vytvoření databáze, aby se naplnila testovacími daty.

Tady použijete `EnsureCreated` metodu k automatickému vytvoření databáze. V [pozdějším kurzu](migrations.md) se dozvíte, jak zpracovat změny modelu pomocí migrace Code First ke změně schématu databáze místo vyřazení a opětovnému vytvoření databáze.

Ve složce *data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a nahraďte kód šablony následujícím kódem, což způsobí, že se databáze vytvoří v případě potřeby a načte testovací data do nové databáze.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Kód kontroluje, zda jsou v databázi nějaké studenty, a pokud ne, předpokládá se, že je databáze nová a je nutné ji naplnit testovacími daty. Načte testovací data do pole spíše než `List<T>` kolekce za účelem optimalizace výkonu.

V *program.cs*upravte `Main` metodu tak, aby při spuštění aplikace provedete následující:

* Získá instanci kontextu databáze z kontejneru vkládání závislostí.
* Zavolejte metodu počáteční hodnoty a předejte jí kontext.
* Uvolněte kontext, pokud je metoda počáteční hodnoty hotová.

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Přidat `using` příkazy:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

Ve starších kurzech se můžete podívat na podobný kód v `Configure` metodě v *Startup.cs*. Tuto `Configure` metodu doporučujeme použít jenom k nastavení kanálu požadavků. Spouštěcí kód aplikace patří do `Main` metody.

Nyní při prvním spuštění aplikace se vytvoří databáze a dokončí se testovacími daty. Kdykoli změníte datový model, můžete databázi odstranit, aktualizovat metodu počáteční hodnoty a začít afresh s novou databází stejným způsobem. V dalších kurzech se dozvíte, jak změnit databázi při změně datového modelu, aniž byste ho museli odstranit a znovu vytvořit.

## <a name="create-controller-and-views"></a>Vytvořit kontroler a zobrazení

V dalším kroku použijete modul generování uživatelského rozhraní v aplikaci Visual Studio a přidáte kontroler MVC a zobrazení, které budou používat EF k dotazování a ukládání dat.

Automatické vytváření metod a zobrazení akcí CRUD se označuje jako generování uživatelského rozhraní. Generování uživatelského rozhraní se liší od generování kódu v tom, že generovaný kód je výchozím bodem, který lze upravit tak, aby vyhovoval vašim vlastním požadavkům, zatímco obvykle neupravujete generovaný kód. Pokud potřebujete přizpůsobit generovaný kód, použijete částečné třídy nebo znovu vygenerujete kód při změně.

* V **Průzkumník řešení** klikněte pravým tlačítkem na složku **řadiče** a vyberte **Přidat > Nová vygenerovaná položka**.

* V dialogovém okně **Přidat generování uživatelského rozhraní** :

  * **Pomocí Entity Framework vyberte kontroler MVC se zobrazeními**.

  * Klikněte na **Přidat**. Zobrazí se dialogové okno **Přidat kontroler MVC se Entity Framework zobrazeními** .

    ![Student pro generování uživatelského rozhraní](intro/_static/scaffold-student2.png)

  * V **třídě model** vyberte **student**.

  * V oblasti **datový kontext** vyberte **SchoolContext**.

  * Přijměte výchozí **StudentsController** jako název.

  * Klikněte na **Přidat**.

  Když kliknete na tlačítko **Přidat**, modul generování uživatelského rozhraní sady Visual Studio vytvoří soubor *StudentsController.cs* a sadu zobrazení (soubory *. cshtml* ), které pracují s řadičem.

(Modul generování uživatelského rozhraní může také vytvořit kontext databáze za vás, pokud ho nevytvoříte ručně, jako jste předtím v tomto kurzu. Novou třídu kontextu můžete zadat v poli **Přidat kontrolér** kliknutím na znaménko plus napravo od **třídy Context data**.  Visual Studio potom vytvoří `DbContext` třídu a také kontroler a zobrazení.)

Všimnete si, že kontroler přebírá `SchoolContext` jako parametr konstruktoru.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

Vložení závislostí ASP.NET Core se postará o předání `SchoolContext` instance do kontroleru. V souboru *Startup.cs* jste předtím nakonfigurovali.

Kontroler obsahuje `Index` metodu akce, která zobrazí všechny studenty v databázi. Metoda získá seznam studentů ze sady entit studentů načtením `Students` vlastnosti instance kontextu databáze:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

O asynchronních programovacích prvcích v tomto kódu se dozvíte později v tomto kurzu.

Zobrazení */studenty/index. cshtml* zobrazí tento seznam v tabulce:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Stisknutím kombinace kláves CTRL + F5 spusťte projekt nebo zvolte možnost **ladění > spustit bez ladění** z nabídky.

Kliknutím na kartu Students zobrazíte testovací data, která `DbInitializer.Initialize` metoda vložila. V závislosti na tom, jak úzká je okno prohlížeče, uvidíte `Students` v horní části stránky odkaz na kartu nebo v pravém horním rohu klikněte na navigační ikonu, aby se odkaz zobrazil.

![Úzká Domovská stránka společnosti Contoso University](intro/_static/home-page-narrow.png)

![Studenti indexová stránka](intro/_static/students-index.png)

## <a name="view-the-database"></a>Zobrazení databáze

Při spuštění aplikace `DbInitializer.Initialize` je volána `EnsureCreated`metoda. EF zjistil, že neexistovala žádná databáze a proto byla vytvořena, a potom zbytek `Initialize` kódu metody naplní databázi daty. K zobrazení databáze v aplikaci Visual Studio můžete použít **Průzkumník objektů systému SQL Server** (SSOX).

Zavřete prohlížeč.

Pokud okno SSOX ještě není otevřené, vyberte ho v nabídce **zobrazení** v aplikaci Visual Studio.

V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze**a potom klikněte na položku pro název databáze, která se nachází v připojovacím řetězci v souboru *appSettings. JSON* .

Rozbalte uzel **tabulky** , aby se zobrazily tabulky v databázi.

![Tabulky v SSOX](intro/_static/ssox-tables.png)

Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte sloupce, které byly vytvořeny, a řádky, které byly vloženy do tabulky.

![Tabulka studenta v SSOX](intro/_static/ssox-student-table.png)

Soubory databáze *. mdf* a *. ldf* jsou ve složce *\\C:\Users\<uživatelské_jméno >* .

`EnsureCreated` Vzhledem`Student` k tomu, že zavoláte metodu inicializátoru, která se spouští při spuštění aplikace, můžete nyní provést změnu třídy, odstranit databázi, znovu spustit aplikaci a databáze se automaticky znovu vytvoří, aby odpovídala vaší změně. Například pokud přidáte `EmailAddress` vlastnost `Student` do třídy, zobrazí se nový `EmailAddress` sloupec v znovu vytvořené tabulce.

## <a name="conventions"></a>Konvence

Množství kódu, který jste museli zapsat, aby Entity Framework mohl vytvořit úplnou databázi, je minimální z důvodu použití konvencí nebo předpokladů, které Entity Framework provádí.

* Názvy `DbSet` vlastností se používají jako názvy tabulek. Pro entity, na které není `DbSet` odkazováno pomocí vlastnosti, se názvy tříd entit používají jako názvy tabulek.

* Názvy vlastností entit se používají pro názvy sloupců.

* Vlastnosti entity s názvem ID nebo classnameID jsou rozpoznány jako vlastnosti primárního klíče.

* Vlastnost je interpretována jako vlastnost cizího klíče, pokud se `StudentID` nazývá  *\<název navigační vlastnosti >\<název vlastnosti primárního klíče >* (například pro `Student` vlastnost navigace od `Student`primární klíč entity je `ID`). Vlastnosti cizího klíče mohou mít také  *\<název jednoduché vlastnosti primárního klíče >* ( `Enrollment` například vzhledem k `EnrollmentID` tomu, že primární klíč entity je `EnrollmentID`).

Konvenční chování se dá přepsat. Můžete například explicitně zadat názvy tabulek, jak jste viděli dříve v tomto kurzu. A můžete nastavit názvy sloupců a nastavit jakoukoli vlastnost jako primární klíč nebo cizí klíč, protože se v [pozdějším kurzu](complex-data-model.md) v této sérii zobrazí.

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.

Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán. Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna. Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny. Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků. V důsledku toho asynchronního kódu umožňuje prostředky serveru použije efektivněji a aby zvládla větší provoz bez zpoždění je povoleno na serveru.

Asynchronní kód zavádí malé množství režie za běhu, ale u situací s nízkým objemem provozu je dosaženo zanedbatelného výkonu, zatímco v případě vysoké situace v provozu je potenciální zlepšení výkonu značné.

V následujícím kódu `async` klíčové slovo, `Task<T>` vrácené hodnoty, `await` klíčové slovo a `ToListAsync` metoda provede asynchronní spouštění kódu.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* Klíčové slovo instruuje kompilátor, aby vygeneroval zpětná volání pro části těla metody a automaticky `Task<IActionResult>` vytvořila vrácený objekt. `async`

* Návratový typ `Task<IActionResult>` představuje průběžnou práci s výsledkem typu `IActionResult`.

* `await` – Klíčové slovo způsobí, že kompilátor metodu rozdělit do dvou částí. První část končí operace, která se spustí asynchronně. Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.

* `ToListAsync` je asynchronní verze `ToList` – metoda rozšíření.

Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá Entity Framework:

* Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze. Který obsahuje `ToListAsync` `SingleOrDefaultAsync`například,, a `SaveChangesAsync`. Neobsahuje například příkazy, které pouze mění `IQueryable`, `var students = context.Students.Where(s => s.LastName == "Davolio")`například.

* Kontext EF není bezpečný pro přístup z více vláken: Nepokoušejte se souběžně provádět více operací. Při volání jakékoli asynchronní metody EF vždy použijte `await` klíčové slovo.

* Chcete-li využít výhod výkonu asynchronního kódu, zajistěte, aby všechny balíčky knihovny, které používáte (například pro stránkování), používaly také Async, pokud volají jakékoli Entity Framework metody, které způsobují odesílání dotazů do databáze.

Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/articles/standard/async).

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořená webová aplikace ASP.NET Core MVC
> * Nastavit styl lokality
> * Seznámili jste se s EF Core balíčky NuGet
> * Vytvoření datového modelu
> * Byl vytvořen kontext databáze.
> * Zaregistrováno rozhraní SchoolContext
> * Inicializovaná databáze s testovacími daty
> * Vytvořen kontroler a zobrazení
> * Zobrazení databáze

V následujícím kurzu se dozvíte, jak provádět základní operace CRUD (vytváření, čtení, aktualizace, odstranění).

Přejděte k dalšímu kurzu, kde se dozvíte, jak provádět základní operace CRUD (vytváření, čtení, aktualizace, odstranění).

> [!div class="nextstepaction"]
> [Implementace základní funkce CRUD](crud.md)

::: moniker-end
