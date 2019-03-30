---
title: 'Kurz: Začínáme s EF Core ve webové aplikaci ASP.NET MVC'
description: Toto je první ze série kurzů, které vysvětlují, jak vytvořit ukázková aplikace Contoso University úplně od začátku.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: 282af56eb911aea53a6ce945e7c1177c158fc342
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750585"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Kurz: Začínáme s EF Core ve webové aplikaci ASP.NET MVC

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvářet webové aplikace ASP.NET Core 2.2 MVC pomocí Entity Framework (EF) Core 2.2 a sady Visual Studio 2017 nebo 2019.

Ukázková aplikace je webovou stránku pro fiktivní společnosti Contoso University. Zahrnuje funkce, jako student přijetí, kurz vytvoření a přiřazení instruktorem. Toto je první ze série kurzů, které vysvětlují, jak vytvořit ukázková aplikace Contoso University úplně od začátku.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření webové aplikace ASP.NET Core MVC
> * Nastavit styl lokality
> * Další informace o balíčcích EF Core NuGet
> * Vytvoření datového modelu
> * Vytvořte kontext databáze
> * Zaregistrovat kontext pro vkládání závislostí
> * Inicializace databáze s testovací data
> * Vytvoření kontroleru a zobrazení
> * Zobrazení databáze

## <a name="prerequisites"></a>Požadavky

* [.NET Core SDK 2.2](https://www.microsoft.com/net/download)
* [Visual Studio 2017 nebo 2019](https://visualstudio.microsoft.com/downloads/) s následujícími sadami funkcí:
    * **Vývoj pro ASP.NET a web** pracovního vytížení
    * **Vývoj pro různé platformy .NET core** pracovního vytížení

## <a name="troubleshooting"></a>Poradce při potížích

Pokud narazíte na problém nevyřešíte sami, můžete najít řešení obvykle porovnáním kódu [dokončený projekt](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final). Seznam běžných chyb a jak je vyřešit, najdete v části [části Poradce při potížích s posledním dílem série](advanced.md#common-errors). Pokud jste nenašli, co potřebujete existuje, můžete odeslat dotaz do StackOverflow.com pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Toto je série 10 kurzů, z nichž každý je založena na co se provádí v předchozích kurzech. Zvažte možnost uložení kopie projektu po každé úspěšné dokončení tohoto kurzu. A pokud narazíte na problémy, můžete začít z předchozí kurz o službě místo přechodu zpět na začátek celou řadu.

## <a name="contoso-university-web-app"></a>Contoso University webové aplikace

Aplikace, kterou je budete vytvářet v těchto kurzech je webová stránka jednoduché university.

Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem. Tady je několik obrazovek, které vytvoříte.

![Studenti indexová stránka](intro/_static/students-index.png)

![Stránky pro úpravu studentů](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Vytvoření webové aplikace

* Otevřít Visual Studio.

* Z **souboru** nabídce vyberte možnost **nový > projekt**.

* V levém podokně vyberte **nainstalováno > Visual C# > Web**.

* Vyberte **webové aplikace ASP.NET Core** šablony projektu.

* Zadejte **ContosoUniversity** jako název a klikněte na **OK**.

  ![Dialogové okno nového projektu](intro/_static/new-project2.png)

* Počkejte **nová webová aplikace ASP.NET Core** zobrazit dialogové okno.

* Vyberte **.NET Core**, **2.2 technologie ASP.NET Core** a **webové aplikace (Model-View-Controller)** šablony.

* Ujistěte se, že **ověřování** je nastavena na **bez ověřování**.

* Vyberte **OK**

  ![Dialogové okno Nový projekt ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Nastavit styl lokality

Několik jednoduchých změn se nastavit v nabídce webu, rozložení a domovské stránky.

Otevřít *Views/Shared/_Layout.cshtml* a proveďte následující změny:

* Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso". Existují tři výskyty.

* Přidání položek nabídky **o**, **studenty**, **kurzy**, **Instruktoři**, a **oddělení**, a Odstranit **ochrany osobních údajů** položku nabídky.

Změny jsou zvýrazněné.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,37-48,63)]

V *Views/Home/Index.cshtml*, nahraďte obsah souboru následující kód, který nahradí text o ASP.NET a MVC o této aplikaci:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Stisknutím kláves CTRL + F5 ke spuštění projektu nebo zvolte **ladit > Spustit bez ladění** z nabídky. Zobrazí domovská stránka s kartami pro stránky, které vytvoříte v těchto kurzech.

![Domovská stránka vysoké školy contoso](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>Informace o balíčcích EF Core NuGet

Do projektu přidat podporu EF Core, nainstalujte poskytovatele databáze, kterou chcete cílit. Tento kurz používá systém SQL Server a je balíček zprostředkovatele [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Tento balíček je součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), takže není nutné chcete odkázat na balíček.

Balíček EF SQL serveru a jeho závislosti (`Microsoft.EntityFrameworkCore` a `Microsoft.EntityFrameworkCore.Relational`) poskytují podporu runtime pro EF. Přidejte balíček nástroje v pozdější [migrace](migrations.md) kurzu.

Informace o dalších poskytovatelů databáze, které jsou dostupné pro Entity Framework Core najdete v tématu [databáze poskytovatelé](/ef/core/providers/).

## <a name="create-the-data-model"></a>Vytvoření datového modelu

Dále vytvoříte tříd entit pro aplikaci Contoso University. Začnete s následující tři entity.

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

Existuje vztah jeden mnoho mezi `Student` a `Enrollment` entity, a existuje vztah jeden mnoho mezi `Course` a `Enrollment` entity. Jinými slovy student možné zaregistrovat libovolný počet kurzy a kurzu může mít libovolný počet studentů zaregistrovaná do něj.

V následujících částech vytvoříte třídu pro každou z těchto entit.

### <a name="the-student-entity"></a>Entita studenta

![Diagram entity studenta](intro/_static/student-entity.png)

V *modely* složce vytvořte soubor třídy *Student.cs* a nahraďte kód šablony následujícím kódem.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID` Vlastnost se stane sloupec primárního klíče tabulky databáze, která odpovídá této třídy. Ve výchozím nastavení interpretuje Entity Framework vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.

`Enrollments` Je vlastnost [navigační vlastnost](/ef/core/modeling/relationships). Vlastnosti navigace podržte dalšími subjekty, které se vztahují k této entity. V takovém případě `Enrollments` vlastnost `Student entity` bude obsahovat všechny `Enrollment` entity, které se vztahují k, které `Student` entity. Jinými slovy, pokud daný řádek studentů v databázi má dva související řádky registrace (řádky, které obsahují hodnotu primárního klíče student získal v jejich StudentID sloupec cizího klíče), který `Student` entity `Enrollments` navigační vlastnost bude obsahovat tyto dvě `Enrollment` entity.

Pokud vlastnost navigace může obsahovat více entit (jako v relace m: n nebo 1 n), jeho typ musí být seznam, ve kterém položky lze přidávat, odstranit a aktualizovat, například `ICollection<T>`. Můžete zadat `ICollection<T>` nebo typu jako `List<T>` nebo `HashSet<T>`. Pokud zadáte `ICollection<T>`, vytvoří EF `HashSet<T>` kolekcí ve výchozím nastavení.

### <a name="the-enrollment-entity"></a>Registrace entity

![Diagram entity registrace](intro/_static/enrollment-entity.png)

V *modely* složku, vytvořte *Enrollment.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID` Bude mít vlastnost primárního klíče; tato entita používá `classnameID` vzorku místo `ID` samostatně jako jste viděli v `Student` entity. Obvykle by zvolte jeden model a použít ho v rámci datového modelu. Tady variantu ukazuje, které můžete použít buď vzor. V [pozdějších kurzech](inheritance.md), uvidíte, jak pomocí ID bez classname usnadňuje implementaci dědičnosti v datovém modelu.

`Grade` Vlastnost je `enum`. Otazník po `Grade` deklarace typu znamená, že `Grade` vlastnost může mít hodnotu Null. Na podnikové úrovni, který má hodnotu null se liší od nulové třída – null znamená, že známku vyjádřenou není znám nebo ještě nebyly přiřazeny.

`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`. `Enrollment` Entita je přidružený nejméně k jednomu `Student` entity, tak vlastnost může obsahovat pouze jeden `Student` entity (na rozdíl od `Student.Enrollments` navigační vlastnost předchozímu příkladu, který může obsahovat více `Enrollment` entity).

`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`. `Enrollment` Entita je přidružený nejméně k jednomu `Course` entity.

Nastavení interpretuje Entity Framework vlastnost jako vlastnost cizího klíče Pokud je název `<navigation property name><primary key property name>` (například `StudentID` pro `Student` navigační vlastnost, protože `Student` je primární klíč entity `ID`). Vlastnosti cizího klíče může nazývat také jednoduše `<primary key property name>` (například `CourseID` od `Course` je primární klíč entity `CourseID`).

### <a name="the-course-entity"></a>Kurz entity

![Diagram kurzu entity](intro/_static/course-entity.png)

V *modely* složku, vytvořte *Course.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments` Je navigační vlastnost. A `Course` entit může souviset s libovolným počtem `Enrollment` entity.

Informace o kliknu `DatabaseGenerated` atribut v [pozdějších kurzech](complex-data-model.md) v této sérii. V podstatě tento atribut umožňuje zadat primární klíč pro kurz namísto nutnosti databáze jeho vygenerování.

## <a name="create-the-database-context"></a>Vytvořte kontext databáze

Hlavní třída, která koordinuje funkce Entity Framework pro daný datový model je třídy kontextu databáze. Vytvoření této třídy odvozené z `Microsoft.EntityFrameworkCore.DbContext` třídy. V kódu určíte entit, které jsou zahrnuty v datovém modelu. Můžete také přizpůsobit chování určité Entity Framework. V tomto projektu je s názvem třídy `SchoolContext`.

Ve složce projektu vytvořte složku s názvem *Data*.

V *Data* složku vytvořte nový soubor třídy *SchoolContext.cs*a nahraďte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Tento kód vytvoří `DbSet` vlastností pro každou sadu entit. V terminologii Entity Framework obvykle sadu entit odpovídá databázové tabulky a entity odpovídající řádek v tabulce.

Mohli jste byl vynechán `DbSet<Enrollment>` a `DbSet<Course>` příkazy a bude fungovat stejně. Entity Framework bude zahrnovat je implicitně protože `Student` odkazy na entity `Enrollment` entity a `Enrollment` odkazy na entity `Course` entity.

Když se vytvoří databáze, EF vytvoří tabulky, které mají názvy, stejně jako `DbSet` názvy vlastností. Pro kolekce v názvech vlastností se obvykle množném čísle (studenti spíše než Student), ale vývojáři Nesouhlasím o tom, jestli by měl názvy tabulek pluralized nebo ne. Pro tyto kurzy přepíšete výchozí chování tak, že zadáte názvy singulární tabulek v uvolněn objekt DbContext. K tomu, přidejte následující zvýrazněný kód za poslední DbSet vlastnost.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Zaregistrujte SchoolContext

ASP.NET Core implementuje [injektáž závislostí](../../fundamentals/dependency-injection.md) ve výchozím nastavení. Služby (například v kontextu databáze EF) jsou registrované pomocí vkládání závislostí při spuštění aplikace. Komponenty, které vyžadují tyto služby (jako jsou řadiče MVC) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru. Zobrazí se vám kód konstruktoru kontroler, který získá instance kontextu později v tomto kurzu.

K registraci `SchoolContext` jako služby, otevřete *Startup.cs*a přidejte zvýrazněné řádky a `ConfigureServices` metody.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

Název připojovacího řetězce je předán v rámci voláním metody na `DbContextOptionsBuilder` objektu. Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.

Přidat `using` příkazy pro `ContosoUniversity.Data` a `Microsoft.EntityFrameworkCore` obory názvů a pak sestavte projekt.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Otevřít *appsettings.json* a přidejte připojovací řetězec, jak je znázorněno v následujícím příkladu.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Připojovací řetězec Určuje databázi SQL Server LocalDB. LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí. LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace. Ve výchozím nastavení LocalDB vytvoří *.mdf* databázové soubory v `C:/Users/<user>` adresáře.

## <a name="initialize-db-with-test-data"></a>Inicializace databáze s testovací data

Entity Framework pro vytvoření prázdné databáze. V této části napíšete metodu, která je volána po vytvoření databáze, aby bylo možné naplnit ho daty testu.

Zde použijete `EnsureCreated` metoda automaticky vytvořit databázi. V [pozdějších kurzech](migrations.md) uvidíte, jak zpracovat změny modelu pomocí migrace Code First pro změnu schématu databáze místo vyřadit a znovu vytvořit databázi.

V *Data* složku, vytvořte nový soubor třídy *DbInitializer.cs* a nahraďte kód šablony následujícím kódem, což způsobí, že databáze má být vytvořen v případě potřeby a načte testovací data do nové databáze.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Kód kontroluje, jestli jsou všechny studenty v databázi a pokud ne, předpokládá se nové databáze a musí být nasazený s testovací data. Načte testovací data do pole spíše než `List<T>` kolekce za účelem optimalizace výkonu.

V *Program.cs*, změnit `Main` metoda na spuštění aplikace, postupujte takto:

* Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.
* Volejte metodu počáteční hodnoty předání kontextu.
* Kontext Dispose po dokončení počáteční hodnoty metody.

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Přidat `using` příkazy:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

V kurzech starší se může zobrazit podobný kód v `Configure` metoda *Startup.cs*. Doporučujeme použít `Configure` metodu pouze k vytvoření kanálu požadavku. Spouštěcímu kódu aplikace, do kterých patří `Main` metody.

Nyní při prvním spuštění aplikace, databáze bude vytvoří a nasadí se testovací data. Pokaždé, když změníte datový model, můžete odstranit databázi, aktualizovat vaše seed – metoda a začít znovu s novou databázi stejným způsobem. V budoucích kurzech uvidíte, jak upravit databázi, když datového modelu změny, bez odstranění a vytvoříte ho znovu.

## <a name="create-controller-and-views"></a>Vytvoření kontroleru a zobrazení

V dalším kroku použijete modul generování uživatelského rozhraní v sadě Visual Studio přidat kontroler MVC a zobrazení, která bude používat EF pro dotazování a uložit data.

Automatické vytváření metody akcí CRUD a zobrazení se označuje jako generování uživatelského rozhraní. Generování uživatelského rozhraní se liší od vytváření kódu v tom, že automaticky generovaný kód je výchozím bodem, který můžete upravit tak, aby vyhovoval vašim požadavkům, zatímco obvykle neupravujte generovaného kódu. Když budete potřebovat pro vygenerovaný kód upravit, použijte částečné třídy nebo při změně věci se znovu vygenerovat kód.

* Klikněte pravým tlačítkem myši **řadiče** složky v **Průzkumníka řešení** a vyberte **Přidat > novou vygenerovanou položku**.

* V **přidat vygenerované uživatelské rozhraní** dialogové okno:

  * Vyberte **kontroler MVC se zobrazeními pomocí Entity Frameworku**.

  * Klikněte na **Přidat**. **Přidat kontroler MVC se zobrazeními, používá nástroj Entity Framework** zobrazí se dialogové okno.

    ![Student vygenerované uživatelské rozhraní](intro/_static/scaffold-student2.png)

  * V **třída modelu** vyberte **Student**.

  * V **třída kontextu dat** vyberte **SchoolContext**.

  * Přijměte výchozí nastavení **StudentsController** jako název.

  * Klikněte na **Přidat**.

  Po kliknutí na **přidat**, vytvoří modul generování uživatelského rozhraní sady Visual Studio *StudentsController.cs* souboru a nastavte zobrazení (*.cshtml* soubory), které fungují s kontrolerem.

(Modul generování uživatelského rozhraní můžete také vytvořit kontext databáze za vás Pokud nechcete vytvořit ručně nejprve, jako jste to udělali dříve v tomto kurzu. Můžete zadat novou třídu v kontextu **přidat kontroler** otevřete ho kliknutím na znaménko plus vedle **třída kontextu dat**.  Visual Studio vytvoří vaše `DbContext` třídy a také kontroler a zobrazení.)

Všimněte si, že trvá kontroleru `SchoolContext` jako parametr konstruktoru.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

Injektáž závislostí ASP.NET Core se postará o předáním instance `SchoolContext` do kontroleru. Jste nakonfigurovali, že *Startup.cs* soubor výše.

Obsahuje kontroler `Index` metodě akce, která zobrazuje všechny studenty v databázi. Metoda získá seznam studentů z entity studenty ve čtení nastavení `Students` vlastnost instance kontextu databáze:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Později v tomto kurzu získáte informace o asynchronní programovací prvky v tomto kódu.

*Views/Students/Index.cshtml* zobrazení seznamu v tabulce:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Stisknutím kláves CTRL + F5 ke spuštění projektu nebo zvolte **ladit > Spustit bez ladění** z nabídky.

Klikněte na kartu studenty a zobrazit testovací data, která `DbInitializer.Initialize` metoda vložen. V závislosti na tom, jak úzké okno prohlížeče, je, zobrazí se vám `Students` odkaz kartě v horní části stránky nebo je budete muset kliknout na navigační ikonu v pravém horním rohu na odkaz zobrazíte.

![Domovská stránka Contoso University úzký](intro/_static/home-page-narrow.png)

![Studenti indexová stránka](intro/_static/students-index.png)

## <a name="view-the-database"></a>Zobrazení databáze

Při spuštění aplikace, `DbInitializer.Initialize` volání metody `EnsureCreated`. EF viděli, že se žádná databáze a proto vytvoří jeden a zbytek `Initialize` kódu metody naplnit databázi daty. Můžete použít **Průzkumník objektů systému SQL Server** (SSOX) Chcete-li zobrazit databáze v sadě Visual Studio.

Zavřete prohlížeč.

Pokud okno SSOX ještě není otevřeno, vyberte ho z **zobrazení** nabídky v sadě Visual Studio.

V SSOX, klikněte na tlačítko **\MSSQLLocalDB (localdb) > databáze**a potom klikněte na položku pro název databáze, která je v připojovacím řetězci v vaše *appsettings.json* souboru.

Rozbalte **tabulky** uzel zobrazíte tabulky v databázi.

![Tabulky v SSOX](intro/_static/ssox-tables.png)

Klikněte pravým tlačítkem na **Student** tabulky a klikněte na tlačítko **Data zobrazení** zobrazit sloupce, které byly vytvořeny a řádky, které byly vloženy do tabulky.

![Tabulka Student v SSOX](intro/_static/ssox-student-table.png)

*.Mdf* a *.ldf* databázové soubory jsou v *C:\Users\\\<uživatelské_jméno >* složky.

Protože voláte `EnsureCreated` v inicializační metoda, která se spustí při spuštění aplikace, může teď provedete změnu `Student` třídy, odstraňte databázi, spusťte aplikaci znovu spustit a databáze bude automaticky znovu vytvořit tak, aby odpovídaly změny. Například, pokud chcete přidat `EmailAddress` vlastnost `Student` třídy, zobrazí se vám nový `EmailAddress` sloupec v tabulce znovu vytvořit.

## <a name="conventions"></a>Konvence

Protože se používá konvence nebo předpokladů, které díky rozhraní Entity Framework je minimální množství kódu, kterou jste používali pro zápis v pořadí pro Entity Framework, abyste mohli vytvořit kompletní databáze za vás.

* Názvy `DbSet` vlastnosti slouží jako názvy tabulek. Pro entity není odkazuje `DbSet` vlastnost entity třída názvy jsou použity jako názvy tabulek.

* Názvy vlastností entity se používají pro názvy sloupců.

* Vlastnosti entity, které jsou pojmenovány ID nebo classnameID jsou rozpoznány jako vlastnosti primárního klíče.

* Vlastnost je interpretován jako vlastnost cizího klíče, pokud je název  *\<název navigační vlastnosti >\<vlastnost primárního klíče name >* (například `StudentID` pro `Student` navigace Vlastnost od `Student` je primární klíč entity `ID`). Vlastnosti cizího klíče může nazývat také jednoduše  *\<vlastnost primárního klíče název >* (například `EnrollmentID` od `Enrollment` je primární klíč entity `EnrollmentID`).

Konvenční chování můžete přepsat. Například můžete explicitně určit názvy tabulek, protože jste viděli dříve v tomto kurzu. A můžete nastavit názvy sloupců a nastavte libovolnou vlastnost jako primární klíč, cizí klíč, nebo jak uvidíte v [pozdějších kurzech](complex-data-model.md) této série.

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.

Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán. Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna. Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny. Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků. V důsledku toho asynchronního kódu umožňuje prostředky serveru použije efektivněji a aby zvládla větší provoz bez zpoždění je povoleno na serveru.

Asynchronní kód v době běhu zavést malé množství režie, ale v situacích s nízkým provozem výkonu přístupů je zanedbatelný, při vysoké návštěvnosti situacích, je možné zlepšení výkonu podstatné.

V následujícím kódu `async` – klíčové slovo, `Task<T>` návratovou hodnotu, `await` – klíčové slovo, a `ToListAsync` metoda změňte kód spustit asynchronně.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async` – Klíčové slovo instruuje kompilátor generovat zpětná volání pro části tělo metody a automaticky vytvářet `Task<IActionResult>` vráceného objektu.

* Návratový typ `Task<IActionResult>` představuje probíhající práci s výsledkem typu `IActionResult`.

* `await` – Klíčové slovo způsobí, že kompilátor metodu rozdělit do dvou částí. První část končí operace, která se spustí asynchronně. Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.

* `ToListAsync` je asynchronní verze `ToList` – metoda rozšíření.

Je potřeba vědět při psaní asynchronního kódu, který používá Entity Framework pár věcí:

* Jenom příkazy, které způsobují dotazy nebo příkazy, které se odesílají do databáze se provedl asynchronně. Který obsahuje, například `ToListAsync`, `SingleOrDefaultAsync`, a `SaveChangesAsync`. Neměl by zahrnovat, například příkazy, které stačí změnit `IQueryable`, jako například `var students = context.Students.Where(s => s.LastName == "Davolio")`.

* Objekt context EF není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně. Při volání asynchronní metody EF, vždy používejte `await` – klíčové slovo.

* Pokud chcete využít výhod výkony těží z asynchronní kód, ujistěte se, že všechny knihovny balíčky, které používáte (například stránkování), asynchronní použijte i v případě volají všechny Entity Framework metody, které způsobují dotazů k odeslání do databáze.

Další informace o asynchronním programování v rozhraní .NET najdete v tématu [asynchronní přehled](/dotnet/articles/standard/async).

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořenou webovou aplikaci ASP.NET Core MVC
> * Nastavit styl lokality
> * Dozvěděli jste se o balíčcích EF Core NuGet
> * Vytvoření datového modelu
> * Vytvoří kontext databáze
> * Registrovaný SchoolContext
> * Inicializované databáze se testovací data
> * Vytvořený kontroler a zobrazení
> * Zobrazení databáze

V následujícím kurzu se dozvíte jak provést základní CRUD (vytváření, čtení, aktualizace nebo odstranění) operace.

Pokračujte k dalšímu kurzu, kde se naučíte, jak provést základní CRUD (vytváření, čtení, aktualizace nebo odstranění) operace.

> [!div class="nextstepaction"]
> [Implementace základních funkcí CRUD](crud.md)
