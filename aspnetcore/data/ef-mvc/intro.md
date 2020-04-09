---
title: 'Kurz: Začínáme s EF Core ve ASP.NET webové aplikaci MVC'
description: Toto je první z řady výukových programů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University od začátku.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: fca9fdc425506ec8b4eec5c609237208f4c0d7b5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511298"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Kurz: Začínáme s EF Core ve ASP.NET webové aplikaci MVC

Tento kurz **nebyl** aktualizován na ASP.NET Core 3.0. [Verze Razor Pages](xref:data/ef-rp/intro) byla aktualizována. Většina změn kódu pro ASP.NET Core 3.0 a novější verzi tohoto kurzu:

* Jsou v *Startup.cs* a *Program.cs* souborů.
* Lze nalézt ve [verzi Razor Pages](xref:data/ef-rp/intro). 

Informace o tom, kdy to může být aktualizován, naleznete [v tomto problému GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/13920).

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Ukázková webová aplikace Contoso University ukazuje, jak vytvořit ASP.NET webových aplikací Core 2.2 MVC pomocí core 2.2 rozhraní Entity Framework (EF) a Visual Studio 2017 nebo 2019.

Ukázková aplikace je webová stránka fiktivní univerzity Contoso. Zahrnuje funkce, jako je přijetí studentů, tvorba kurzů a úkoly instruktora. Toto je první z řady výukových programů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University od začátku.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoření webové aplikace ASP.NET Core MVC
> * Nastavení stylu webu
> * Další informace o balíčcích EF Core NuGet
> * Vytvoření datového modelu
> * Vytvoření kontextu databáze
> * Registrace kontextu pro vkládání závislostí
> * Inicializovat databázi testovacími daty
> * Vytvoření ovladače a zobrazení
> * Zobrazit databázi

## <a name="prerequisites"></a>Požadavky

* [Sada .NET Core SDK 2.2](https://dotnet.microsoft.com/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s následujícími úlohami:
  * **ASP.NET a pracovní zátěž pro vývoj webových** aplikací
  * Pracovní **vytížení napříč platformami .NET Core**

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na problém, který nelze vyřešit, můžete obecně najít řešení porovnáním kódu s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final). Seznam běžných chyb a jejich řešení naleznete [v části Poradce při potížích v posledním kurzu v řadě](advanced.md#common-errors). Pokud tam nenajdete to, co potřebujete, můžete odeslat dotaz, který StackOverflow.com pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Jedná se o sérii 10 výukových programů, z nichž každý staví na tom, co se děje v předchozích kurzech. Zvažte uložení kopie projektu po každém úspěšném dokončení kurzu. Pak, pokud narazíte na problémy, můžete začít znovu z předchozího kurzu namísto návratu na začátek celé série.

## <a name="contoso-university-web-app"></a>Webová aplikace Univerzity Contoso

Aplikace, kterou budete vytvářet v těchto kurzech, je jednoduchá univerzita webové stránky.

Uživatelé mohou zobrazit a aktualizovat informace o studentovi, kurzu a instruktorovi. Zde je několik obrazovek, které vytvoříte.

![Stránka Studentský index](intro/_static/students-index.png)

![Stránka Úpravy studentů](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Vytvoření webové aplikace

* Otevřete sadu Visual Studio.

* V nabídce **Soubor** vyberte **Nový > Project**.

* V levém podokně vyberte **možnost Nainstalováno > visual c# > web**.

* Vyberte šablonu projektu **základní webové aplikace ASP.NET.**

* Jako název zadejte **ContosoUniversity** a klepněte na tlačítko **OK**.

  ![Dialogové okno Nový projekt](intro/_static/new-project2.png)

* Počkejte, až se zobrazí dialogové okno **Nová ASP.NET základní webová aplikace.**

* Vyberte **.NET Core** **, ASP.NET Core 2.2** a **šablonu webové aplikace (Model-View-Controller).**

* Ujistěte **se,** že je možnost Ověřování nastavena na **možnost Žádné ověřování**.

* Vybrat **OK**

  ![Dialogové okno Nový ASP.NET základní projekt](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Nastavení stylu webu

Několik jednoduchých změn nastaví nabídku webu, rozložení a domovskou stránku.

Otevřete *zobrazení/Sdílené/_Layout.cshtml* a proveďte následující změny:

* Změňte každý výskyt "ContosoUniversity" na "Contoso University". Existují tři výskyty.

* Přidejte položky nabídky **Pro Informace**, **Studenti**, **Kurzy**, **Instruktoři**a **Oddělení**a odstraňte položku nabídky **Ochrana osobních údajů.**

Změny jsou zvýrazněny.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

V *části Zobrazení/Home/Index.cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Stisknutím kláves CTRL+F5 spusťte projekt nebo z nabídky zvolte **Ladění > Spustit bez ladění.** Zobrazí se domovská stránka s kartami pro stránky, které vytvoříte v těchto kurzech.

![Domovská stránka Univerzity Contoso](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>O balíčcích EF Core NuGet

Chcete-li přidat podporu EF Core do projektu, nainstalujte poskytovatele databáze, na kterého chcete cílit. Tento kurz používá SQL Server a balíček zprostředkovatele je [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Tento balíček je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), takže na balíček nemusíte odkazovat.

Balíček EF SQL Server a`Microsoft.EntityFrameworkCore` `Microsoft.EntityFrameworkCore.Relational`jeho závislosti ( a ) poskytují podporu modulu runtime pro EF. Balíček nástrojů přidáte později v kurzu [Migrace.](migrations.md)

Informace o jiných zprostředkovatelích databáze, které jsou k dispozici pro core entity frameworku, naleznete v [tématu zprostředkovatelé databáze](/ef/core/providers/).

## <a name="create-the-data-model"></a>Vytvoření datového modelu

Dále vytvoříte třídy entit pro aplikaci Contoso University. Začnete s následujícími třemi entitami.

![Diagram datového modelu zápisu do kurzu-studenta](intro/_static/data-model-diagram.png)

Existuje `Student` vztah 1:N mezi entitami `Enrollment` a existuje vztah 1:N mezi `Course` entitami. `Enrollment` Jinými slovy, student může být zapsán do libovolného počtu kurzů a kurz může mít libovolný počet studentů zapsaných v něm.

V následujících částech vytvoříte třídu pro každou z těchto entit.

### <a name="the-student-entity"></a>Studentská entita

![Diagram studentských entit](intro/_static/student-entity.png)

Ve složce *Modely* vytvořte soubor třídy s názvem *Student.cs* a nahraďte kód šablony následujícím kódem.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

Vlastnost `ID` se stane sloupcem primárního klíče databázové tabulky, který odpovídá této třídě. Ve výchozím nastavení interpretuje Entity Framework vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.

Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships). Navigační vlastnosti mají další entity, které souvisejí s touto entitou. V tomto případě `Enrollments` bude `Student entity` vlastnost a obsahovat všechny `Enrollment` entity, `Student` které jsou ve spojení s touto entitou. Jinými slovy, pokud daný řádek Student v databázi obsahuje dva související řádky zápisu (řádky, které obsahují `Student` hodnotu `Enrollments` primárního klíče studenta ve sloupci cizího klíče StudentID), navigační vlastnost této entity bude obsahovat tyto dvě `Enrollment` entity.

Pokud navigační vlastnost může obsahovat více entit (jako v relacích N:N nebo 1:N), musí být jejím typem `ICollection<T>`seznam, ve kterém lze položky přidávat, odstraňovat a aktualizovat, například . Můžete zadat `ICollection<T>` nebo typ, `List<T>` `HashSet<T>`například nebo . Pokud zadáte `ICollection<T>`, EF `HashSet<T>` vytvoří kolekci ve výchozím nastavení.

### <a name="the-enrollment-entity"></a>Entita Zápis

![Diagram entit zápisu](intro/_static/enrollment-entity.png)

Ve složce *Modely* vytvořte *Enrollment.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

Vlastnost `EnrollmentID` bude primárním klíčem; tato entita `classnameID` používá `ID` vzor místo sebe, `Student` jak jste viděli v entitě. Obvykle byste zvolit jeden vzor a použít jej v celém datovém modelu. Zde varianta ilustruje, že můžete použít buď vzor. V [pozdějším kurzu](inheritance.md)uvidíte, jak použití ID bez názvu třídy usnadňuje implementaci dědičnosti v datovém modelu.

Ubytovací `Grade` zařízení `enum`je . Otazník za `Grade` deklarací typu `Grade` označuje, že vlastnost je nullable. Stupeň, který je null se liší od nulové třídy - null znamená, že platová třída není známa nebo ještě nebyla přiřazena.

Vlastnost `StudentID` je cizí klíč a odpovídající navigační `Student`vlastnost je . `Enrollment` Entita je `Student` přidružena k jedné entitě, takže vlastnost může obsahovat pouze jednu `Student` entitu `Student.Enrollments` (na rozdíl od navigační vlastnosti, kterou jste viděli dříve, která může obsahovat více `Enrollment` entit).

Vlastnost `CourseID` je cizí klíč a odpovídající navigační `Course`vlastnost je . Entita `Enrollment` je `Course` přidružena k jedné entitě.

Entity Framework interpretuje vlastnost jako vlastnost cizího `<navigation property name><primary key property name>` klíče, `StudentID` pokud `Student` je pojmenována `Student` (například pro `ID`navigační vlastnost, protože je primární klíč entity ). Vlastnosti cizího klíče `<primary key property name>` lze také `CourseID` jednoduše `Course` pojmenovat (například `CourseID`vzhledem k tomu, že je primárníklíč entity).

### <a name="the-course-entity"></a>Entita Kurzu

![Diagram entity kurzu](intro/_static/course-entity.png)

Ve složce *Modely* vytvořte *Course.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

Vlastnost `Enrollments` je navigační vlastnost. Entita `Course` může souviset `Enrollment` s libovolným počtem entit.

O atributu `DatabaseGenerated` řekneme více v [pozdějším kurzu](complex-data-model.md) v této sérii. V podstatě tento atribut umožňuje zadat primární klíč pro kurz, nikoli databáze generovat.

## <a name="create-the-database-context"></a>Vytvoření kontextu databáze

Hlavní třída, která koordinuje entity framework funkce pro daný datový model je třída kontextu databáze. Tuto třídu vytvoříte odvozením `Microsoft.EntityFrameworkCore.DbContext` ze třídy. V kódu určíte, které entity budou zahrnuty do datového modelu. Můžete také přizpůsobit určité chování entity framework. V tomto projektu je `SchoolContext`třída pojmenována .

Ve složce projektu vytvořte složku s názvem *Data*.

Ve složce *Data* vytvořte nový soubor třídy s názvem *SchoolContext.cs*a nahraďte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Tento kód `DbSet` vytvoří vlastnost pro každou sadu entit. V terminologii entity Framework sada entit obvykle odpovídá databázové tabulce a entita odpovídá řádku v tabulce.

Mohli jste vynechat `DbSet<Enrollment>` a `DbSet<Course>` prohlášení a fungovalo by to stejně. Rámec entity by je implicitně `Student` zahrnoval, `Enrollment` protože účetní `Enrollment` jednotka odkazuje `Course` na účetní jednotku a účetní jednotka na účetní jednotku odkazuje.

Při vytvoření databáze EF vytvoří tabulky, které mají `DbSet` názvy stejné jako názvy vlastností. Názvy vlastností pro kolekce jsou obvykle množné číslo (Studenti spíše než Student), ale vývojáři nesouhlasí o tom, zda názvy tabulek by měly být pluralized nebo ne. Pro tyto kurzy budete přepsat výchozí chování zadáním názvů tabulek jednotného čísla v DbContext. Chcete-li to provést, přidejte následující zvýrazněný kód za poslední Vlastnost DbSet.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Zaregistrujte schoolcontext

ASP.NET Core implementuje [vkládání závislostí](../../fundamentals/dependency-injection.md) ve výchozím nastavení. Služby (například kontext databáze EF) jsou registrovány s vkládání závislostí při spuštění aplikace. Součásti, které vyžadují tyto služby (například řadiče MVC) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru. Uvidíte kód konstruktoru řadiče, který získá instanci kontextu dále v tomto kurzu.

Chcete-li se zaregistrovat `SchoolContext` jako služba, otevřete `ConfigureServices` *Startup.cs*a přidejte zvýrazněné řádky do metody.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

Název připojovacího řetězce je předán do kontextu `DbContextOptionsBuilder` voláním metody na objektu. Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*

Přidejte `using` `ContosoUniversity.Data` příkazy pro a `Microsoft.EntityFrameworkCore` obory názvů a pak vytvořte projekt.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Otevřete soubor *appsettings.json* a přidejte připojovací řetězec, jak je znázorněno v následujícím příkladu.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>Místní databáze SQL Server Express

Připojovací řetězec určuje databázi SQL Server LocalDB. LocalDB je odlehčená verze sql server express databázový stroj a je určen pro vývoj aplikací, nikoli produkční použití. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace. Ve výchozím nastavení localdb vytvoří *.mdf* databázové soubory v adresáři. `C:/Users/<user>`

## <a name="initialize-db-with-test-data"></a>Inicializovat DB s testovacími daty

Entity Framework vytvoří prázdnou databázi pro vás. V této části napíšete metodu, která je volána po vytvoření databáze, aby byla naplněna testovacími daty.

Zde budete používat `EnsureCreated` metodu k automatickému vytvoření databáze. V [pozdějším kurzu](migrations.md) uvidíte, jak zpracovat změny modelu pomocí migrace Code First změnit schéma databáze namísto uvolnění a opětovné vytvoření databáze.

Ve složce *Data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a nahraďte kód šablony následujícím kódem, který způsobí, že databáze bude v případě potřeby vytvořena a načte testovací data do nové databáze.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Kód zkontroluje, zda jsou v databázi nějaké studenty, a pokud ne, předpokládá, že databáze je nová a musí být nasazena s testovacími daty. Načte testovací data do `List<T>` polí, nikoli do kolekcí pro optimalizaci výkonu.

V *Program.cs*upravte metodu `Main` tak, aby při spuštění aplikace učinila následující kroky:

* Získejte instanci kontextu databáze z kontejneru vkládání závislostí.
* Volání metody osiva, předávání k ní kontextu.
* Dispose kontextu po dokončení metody osiva.

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Přidat `using` příkazy:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

Ve starších kurzech, můžete vidět `Configure` podobný kód v metodě v *Startup.cs*. Doporučujeme použít metodu `Configure` pouze k nastavení kanálu požadavku. Kód spuštění aplikace `Main` patří do metody.

Nyní při prvním spuštění aplikace, bude databáze vytvořena a nasazena s testovacími daty. Kdykoli změníte datový model, můžete odstranit databázi, aktualizovat metodu osiva a začít znovu s novou databází stejným způsobem. V pozdějších kurzech uvidíte, jak upravit databázi při změně datového modelu, aniž byste ji museli snímat a znovu vytvářet.

## <a name="create-controller-and-views"></a>Vytvoření ovladače a zobrazení

Dále budete používat modul generování uživatelského a uživatelského čítaču v sadě Visual Studio k přidání řadiče MVC a zobrazení, která budou používat EF k dotazování a ukládání dat.

Automatické vytváření metod a zobrazení akce CRUD se označuje jako generování uživatelského líačku. Generování uživatelského rozhraní se liší od generování kódu v tom, že kód generování generování generování uživatelského rozhraní je výchozím bodem, který můžete upravit tak, aby vyhovoval vašim vlastním požadavkům, zatímco obvykle neupravujete generovaný kód. Když potřebujete přizpůsobit generovaný kód, použijte částečné třídy nebo znovu vygenerovat kód při změně věci.

* Klepněte pravým tlačítkem myši na složku **Řadiče** v **Průzkumníku řešení** a vyberte **přidat > novou položku scaffolded item**.

* V dialogovém okně **Přidat kancovací okno:**

  * Vyberte **řadič MVC se zobrazeními pomocí entity Framework**.

  * Klikněte na **Přidat**. Zobrazí se dialogové **okno Přidat řadič MVC se zobrazeními pomocí entity Framework.**

    ![Lešení Student](intro/_static/scaffold-student2.png)

  * Ve **třídě Model** vyberte možnost **Student**.

  * V **datovém kontextu třídy** vyberte **SchoolContext**.

  * Přijmout výchozí **StudentsController** jako název.

  * Klikněte na **Přidat**.

  Po klepnutí na tlačítko **Přidat**vytvoří modul zaslechnutí pomocí sady uživatelského zařízení *sady StudentsController.cs* soubor a sadu zobrazení (*soubory CSHTML),* které pracují s řadičem.

(Modul zaschlovacího systému můžete také vytvořit kontext databáze pro vás, pokud nechcete vytvořit ručně nejprve jako dříve pro tento kurz. Novou třídu kontextu můžete zadat v poli **Přidat kontroleru** klepnutím na znaménko plus napravo od **třídy kontextu Dat**.  Visual Studio pak `DbContext` vytvoří třídu, stejně jako řadič a zobrazení.)

Všimněte si, že řadič `SchoolContext` bere jako parametr konstruktoru.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET vkládání závislostí jádra se `SchoolContext` postará o předání instance do řadiče. Nakonfiguroval jste to v *Startup.cs* souboru dříve.

Kontrolid `Index` obsahuje metodu akce, která zobrazuje všechny studenty v databázi. Metoda získá seznam studentů z entity Students nastavenou čtením vlastnosti `Students` instance kontextu databáze:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

O asynchronních programovacích prvcích v tomto kódu se dozvíte dále v kurzu.

*Zobrazení/Studenti/Index.cshtml* zobrazí tento seznam v tabulce:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Stisknutím kláves CTRL+F5 spusťte projekt nebo z nabídky zvolte **Ladění > Spustit bez ladění.**

Kliknutím na kartu Studenti zobrazíte `DbInitializer.Initialize` testovací data, která metoda vložila. V závislosti na tom, jak je okno `Students` prohlížeče úzké, uvidíte odkaz na kartu v horní části stránky nebo budete muset kliknout na ikonu navigace v pravém horním rohu, abyste viděli odkaz.

![Domovská stránka Univerzity Contoso úzká](intro/_static/home-page-narrow.png)

![Stránka Studentský index](intro/_static/students-index.png)

## <a name="view-the-database"></a>Zobrazit databázi

Při spuštění aplikace `DbInitializer.Initialize` volání `EnsureCreated`metody . EF viděl, že neexistuje žádná databáze, a tak `Initialize` ji vytvořil, pak zbytek kódu metody naplnil databázi daty. K zobrazení databáze v sadě Visual Studio můžete použít **Průzkumník objektů serveru SQL Server** (SSOX).

Zavřete prohlížeč.

Pokud okno SSOX ještě není otevřené, vyberte ho z nabídky **Zobrazení** v sadě Visual Studio.

Ve ssoxu klikněte na **(localdb)\MSSQLLocalDB > Databases**a potom klikněte na položku názvu databáze, která je v připojovacím řetězci v souboru *appsettings.json.*

Rozbalte uzel **Tabulky,** abyste viděli tabulky v databázi.

![Tabulky v SSOX](intro/_static/ssox-tables.png)

Klikněte pravým tlačítkem myši na tabulku **Student** a kliknutím na **Zobrazit data** zobrazte sloupce, které byly vytvořeny, a řádky, které byly vloženy do tabulky.

![Studentský stůl ve SSOX](intro/_static/ssox-student-table.png)

Soubory databáze *MDF* a *LDF* jsou ve složce *C:\Users, ve\\\<které je vaše uživatelské jméno>.*

Vzhledem k `EnsureCreated` tomu, že voláte metodu initializer, která běží `Student` při spuštění aplikace, můžete nyní provést změnu třídy, odstranit databázi, znovu spustit aplikaci a databáze by byla automaticky znovu vytvořena tak, aby odpovídala vaší změně. Pokud například přidáte `EmailAddress` vlastnost do `Student` třídy, zobrazí se `EmailAddress` v znovu vytvořené tabulce nový sloupec.

## <a name="conventions"></a>Zásady

Množství kódu, který jste museli napsat, aby rozhraní Entity Framework bylo možné vytvořit úplnou databázi pro vás je minimální z důvodu použití konvencí nebo předpoklady, které umožňuje entity framework.

* Názvy `DbSet` vlastností se používají jako názvy tabulek. Pro entity, na které `DbSet` není odkazováno vlastností, se názvy tříd entit používají jako názvy tabulek.

* Názvy vlastností entity se používají pro názvy sloupců.

* Vlastnosti entity, které jsou pojmenovány ID nebo classnameID jsou rozpoznány jako vlastnosti primárního klíče.

* Vlastnost je interpretována jako vlastnost cizího klíče, `StudentID` pokud je pojmenována `Student` `Student` `ID` * \<název vlastnosti navigace>\<název vlastnosti primárního klíče>* (například pro vlastnost navigace od doby, kdy je primární klíč entity ). Vlastnosti cizího klíče lze také pojmenovat jednoduše `EnrollmentID` `Enrollment` `EnrollmentID` * \<název vlastnosti primárního klíče>* (například vzhledem k tomu, že primární klíč entity je ).

Konvenční chování může být přepsáno. Můžete například explicitně zadat názvy tabulek, jak jste viděli dříve v tomto kurzu. A můžete nastavit názvy sloupců a nastavit libovolnou vlastnost jako primární klíč nebo cizí klíč, jak uvidíte v [pozdějším kurzu](complex-data-model.md) v této sérii.

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.

Webový server má omezený počet vláken k dispozici a v situacích s vysokým zatížením všechny dostupné podprocesy mohou být používány. V takovém případě server nemůže zpracovat nové požadavky, dokud vlákna jsou uvolněny. Pomocí synchronního kódu může být mnoho vláken svázáno, zatímco ve skutečnosti nepracují, protože čekají na dokončení vstupně-up. S asynchronním kódem, když proces čeká na dokončení vstupně-va, jeho vlákno je uvolněno pro server pro zpracování jiných požadavků. V důsledku toho asynchronní kód umožňuje efektivnější použití prostředků serveru a server je povolen pro zpracování většího provozu bez zpoždění.

Asynchronní kód zavést malé množství režie za běhu, ale pro situace s nízkým provozem je přístupů k výkonu zanedbatelný, zatímco pro vysoké dopravní situace potenciální zlepšení výkonu je podstatné.

V následujícím kódu `async` klíčové `Task<T>` slovo, `await` vrácená `ToListAsync` hodnota, klíčové slovo a metoda provést spuštění kódu asynchronně.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* Klíčové `async` slovo říká kompilátoru generovat zpětná volání pro části `Task<IActionResult>` těla metody a automaticky vytvořit objekt, který je vrácen.

* Návratový `Task<IActionResult>` typ představuje probíhající práci `IActionResult`s výsledkem typu .

* Klíčové `await` slovo způsobí, že kompilátor rozdělit metodu do dvou částí. První část končí operací, která byla spuštěna asynchronně. Druhá část je umístěn a callback metoda, která je volána po dokončení operace.

* `ToListAsync`je asynchronní verze metody `ToList` rozšíření.

Některé věci, které je třeba znát při psaní asynchronního kódu, který používá entity framework:

* Pouze příkazy, které způsobují dotazy nebo příkazy, které mají být odeslány do databáze jsou prováděny asynchronně. To zahrnuje například `ToListAsync` `SingleOrDefaultAsync`, `SaveChangesAsync`, a . Neobsahuje například příkazy, které pouze `IQueryable`mění , `var students = context.Students.Where(s => s.LastName == "Davolio")`například .

* Kontext EF není bezpečné pro přístup z více vláken: nepokoušejte se provést více operací paralelně. Při volání jakékoli asynchronní metody `await` EF vždy použijte klíčové slovo.

* Pokud chcete využít výhod výkonu asynchronního kódu, ujistěte se, že všechny balíčky knihovny, které používáte (například pro stránkování), také použít async, pokud volají všechny entity Framework metody, které způsobují dotazy, které mají být odeslány do databáze.

Další informace o asynchronním programování v rozhraní .NET naleznete v [tématu Async Overview](/dotnet/articles/standard/async).

## <a name="get-the-code"></a>Získání kódu

[Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořeno ASP.NET základní webové aplikace MVC
> * Nastavení stylu webu
> * Informace o balíčcích EF Core NuGet
> * Vytvoření datového modelu
> * Vytvoření kontextu databáze
> * Zaregistrováno SchoolContext
> * Inicializovaná DB s testovacími daty
> * Vytvořený ovladač a zobrazení
> * Zobrazení databáze

V následujícím kurzu se dozvíte, jak provádět základní operace CRUD (vytvářet, číst, aktualizovat, odstraňovat).

Přejdete k dalšímu kurzu, kde se dozvíte, jak provádět základní operace CRUD (vytvářet, číst, aktualizovat, odstraňovat).

> [!div class="nextstepaction"]
> [Implementace základních funkcí CRUD](crud.md)

