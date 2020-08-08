---
title: 'Kurz: Začínáme s EF Core ve webové aplikaci ASP.NET MVC'
description: Toto je první v sérii kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University od začátku.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/intro
ms.openlocfilehash: 36b0c913db3c2b6c2c834d33b0ea8665f3e25814
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012965"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Kurz: Začínáme s EF Core ve webové aplikaci ASP.NET MVC

Tento **kurz se** neaktualizoval na ASP.NET Core 3,0. [ Razor Verze stránek](xref:data/ef-rp/intro) byla aktualizována. Většina změn kódu pro ASP.NET Core 3,0 a novější verzi tohoto kurzu:

* Jsou v souborech *Startup.cs* a *program.cs* .
* Lze najít ve [ Razor verzi stránky](xref:data/ef-rp/intro). 

Informace o tom, kdy je možné aktualizaci aktualizovat, najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/13920).

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Ukázková webová aplikace společnosti Contoso University ukazuje, jak vytvářet webové aplikace ASP.NET Core 2,2 MVC pomocí Entity Framework (EF) Core 2,2 a Visual Studio 2017 nebo 2019.

Ukázková aplikace je web pro fiktivní univerzitě společnosti Contoso. Zahrnuje funkce, jako je například využití studenta, vytváření kurzu a přiřazení instruktora. Toto je první v sérii kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University od začátku.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoření webové aplikace ASP.NET Core MVC
> * Nastavení stylu webu
> * Další informace o EF Core balíčcích NuGet
> * Vytvoření datového modelu
> * Vytvoření kontextu databáze
> * Registrace kontextu pro vkládání závislostí
> * Inicializace databáze s testovacími daty
> * Vytvoření kontroleru a zobrazení
> * Zobrazení databáze

## <a name="prerequisites"></a>Požadavky

* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s následujícími úlohami:
  * **ASP.NET a webové vývojové** úlohy
  * **Vývojová úloha .NET Core pro různé platformy**

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na problém, který nelze vyřešit, můžete řešení obecně najít porovnáním kódu s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final). Seznam běžných chyb a jejich řešení najdete v [části věnované řešení potíží v posledním kurzu v řadě](advanced.md#common-errors). Pokud tam nenajdete, co potřebujete, můžete odeslat otázku do StackOverflow.com pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Toto je série 10 kurzů, z nichž každá sestaví na tom, co se děje v předchozích kurzech. Zvažte uložení kopie projektu po každém úspěšném dokončení kurzu. Pak Pokud narazíte na problémy, můžete začít znovu z předchozího kurzu a nemusíte se vrátit na začátek celé řady.

## <a name="contoso-university-web-app"></a>Webová aplikace Contoso University

Aplikace, kterou budete sestavovat v těchto kurzech, je jednoduchý web na univerzitě.

Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech. Tady je několik obrazovek, které vytvoříte.

![Stránka indexu studentů](intro/_static/students-index.png)

![Stránka pro úpravy studentů](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Vytvoření webové aplikace

* Otevřete sadu Visual Studio.

* V nabídce **soubor** vyberte **Nový > projekt**.

* V levém podokně vyberte **nainstalované > Visual C# > web**.

* Vyberte šablonu projektu **ASP.NET Core webové aplikace** .

* Jako název zadejte **ContosoUniversity** a klikněte na **OK**.

  ![Dialogové okno Nový projekt](intro/_static/new-project2.png)

* Počkejte, než se zobrazí dialogové okno **nové webové aplikace ASP.NET Core** .

* Vyberte **.NET Core**, **ASP.NET Core 2,2** a šablonu **Webová aplikace (model-zobrazení-kontroler)** .

* Ujistěte se, že je **ověřování** nastaveno na **bez ověřování**.

* Vybrat **OK**

  ![Dialog nového projektu ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Nastavení stylu webu

V několika jednoduchých změnách se nastaví nabídka web, rozložení a Domovská stránka.

Otevřete *views/Shared/_Layout. cshtml* a proveďte následující změny:

* Změňte všechny výskyty "ContosoUniversity" na "contoso University". Existují tři výskyty.

* Přidejte položky **nabídky pro,** **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **osobních údajů** .

Změny jsou zvýrazněny.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

V *zobrazeních/Home/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Stisknutím kombinace kláves CTRL + F5 spusťte projekt nebo zvolte možnost **ladění > spustit bez ladění** z nabídky. Zobrazí se stránka domů s kartami pro stránky, které vytvoříte v těchto kurzech.

![Domovská stránka společnosti Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>Informace o EF Core balíčků NuGet

Chcete-li přidat do projektu podporu EF Core, nainstalujte poskytovatele databáze, na který chcete cílit. V tomto kurzu se používá SQL Server a balíček poskytovatele je [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Tento balíček je zahrnutý ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), takže nemusíte odkazovat na balíček.

Balíček EF SQL Server a jeho závislosti ( `Microsoft.EntityFrameworkCore` a `Microsoft.EntityFrameworkCore.Relational` ) poskytují podporu modulu runtime pro EF. Přidáte balíček nástrojů později v kurzu [migrace](migrations.md) .

Informace o dalších poskytovatelích databází, které jsou k dispozici pro Entity Framework Core, najdete v tématu [poskytovatelé databáze](/ef/core/providers/).

## <a name="create-the-data-model"></a>Vytvoření datového modelu

V dalším kroku vytvoříte třídy entit pro aplikaci Contoso University. Začnete s následujícími třemi entitami.

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

Mezi `Student` entitami a entitami existuje vztah 1: n `Enrollment` a mezi `Course` `Enrollment` entitami a entitami je vztah 1:1. Jinými slovy, student může být zaregistrovaný v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.

V následujících částech vytvoříte třídu pro každou z těchto entit.

### <a name="the-student-entity"></a>Entita studenta

![Diagram entity studenta](intro/_static/student-entity.png)

Ve složce *modely* vytvořte soubor třídy s názvem *student.cs* a nahraďte kód šablony následujícím kódem.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID`Vlastnost se změní na sloupec primárního klíče tabulky databáze, který odpovídá této třídě. Ve výchozím nastavení interpretuje Entity Framework vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.

`Enrollments`Vlastnost je [navigační vlastnost](/ef/core/modeling/relationships). Navigační vlastnosti obsahují další entity, které se vztahují k této entitě. V takovém případě `Enrollments` bude vlastnost objektu `Student entity` obsahovat všechny `Enrollment` entity, které se vztahují k dané `Student` entitě. Jinými slovy, pokud daný řádek studenta v databázi obsahuje dva související řádky zápisu (řádky, které obsahují hodnotu primárního klíče tohoto studenta ve sloupci StudentID Foreign Key), `Student` `Enrollments` vlastnost navigace této entity bude obsahovat tyto dvě `Enrollment` entity.

Pokud navigační vlastnost může obsahovat více entit (jako v relacích m:n nebo 1:1), musí se jednat o seznam, ve kterém lze přidávat, odstraňovat a aktualizovat položky, jako je například `ICollection<T>` . Můžete zadat `ICollection<T>` nebo typ jako `List<T>` nebo `HashSet<T>` . Pokud zadáte `ICollection<T>` , EF vytvoří `HashSet<T>` ve výchozím nastavení kolekci.

### <a name="the-enrollment-entity"></a>Entita registrace

![Diagram entity registrace](intro/_static/enrollment-entity.png)

Ve složce *modely* vytvořte *Enrollment.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID`Vlastnost bude primární klíč. Tato entita používá `classnameID` vzor místo toho, `ID` Jak jste viděli v `Student` entitě. Obvykle byste zvolili jeden model a používali ho v rámci svého datového modelu. V tomto příkladu variace znázorňuje, že můžete použít libovolný vzor. V [pozdějším kurzu](inheritance.md)uvidíte, jak použít ID bez ClassName, usnadňuje implementaci dědičnosti v datovém modelu.

`Grade`Vlastnost je `enum` . Otazník po `Grade` deklaraci typu označuje, že vlastnost může `Grade` mít hodnotu null. Hodnota, která je null, se liší od nulové třídy – hodnota null znamená, že se nejedná o známku nebo ještě není přiřazená.

`StudentID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student` . `Enrollment`Entita je přidružená k jedné `Student` entitě, takže vlastnost může uchovávat jenom jednu `Student` entitu (na rozdíl od `Student.Enrollments` vlastnosti navigace, kterou jste viděli dříve, která může obsahovat víc `Enrollment` entit).

`CourseID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course` . `Enrollment`Entita je přidružená k jedné `Course` entitě.

Entity Framework interpretuje vlastnost jako vlastnost cizího klíče, pokud má název `<navigation property name><primary key property name>` (například `StudentID` pro `Student` vlastnost navigace, protože `Student` primární klíč entity je `ID` ). Vlastnosti cizího klíče lze také pojmenovat jednoduše `<primary key property name>` (například vzhledem k tomu, že `CourseID` `Course` primární klíč entity je `CourseID` ).

### <a name="the-course-entity"></a>Entita kurzu

![Diagram entity kurzu](intro/_static/course-entity.png)

Ve složce *modely* vytvořte *Course.cs* a nahraďte existující kód následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments`Vlastnost je navigační vlastnost. `Course`Entita může souviset s libovolným počtem `Enrollment` entit.

V `DatabaseGenerated` [pozdějším kurzu](complex-data-model.md) v této sérii zaznamenáme Další informace o atributu. V podstatě vám tento atribut umožňuje zadat primární klíč pro kurz místo toho, aby ho databáze vygenerovala.

## <a name="create-the-database-context"></a>Vytvoření kontextu databáze

Hlavní třída, která koordinuje funkce Entity Framework pro daný datový model, je třída kontextu databáze. Tuto třídu vytvoříte odvozením z `Microsoft.EntityFrameworkCore.DbContext` třídy. Ve vašem kódu určíte, které entity budou zahrnuty v datovém modelu. Můžete také přizpůsobit určité chování Entity Framework. V tomto projektu je třída pojmenována `SchoolContext` .

Ve složce projektu vytvořte složku s názvem *data*.

Ve složce *data* vytvořte nový soubor třídy s názvem *SchoolContext.cs*a nahraďte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Tento kód vytvoří `DbSet` vlastnost pro každou sadu entit. V Entity Framework terminologii sada entit obvykle odpovídá tabulce databáze a entita odpovídá řádku v tabulce.

Mohli jste vynechat `DbSet<Enrollment>` `DbSet<Course>` příkazy a a to by fungovalo stejně. Entity Framework by je implicitně zahrnuly, protože entita odkazuje na entitu a odkazuje na entitu `Student` `Enrollment` `Enrollment` `Course` .

Při vytvoření databáze EF vytvoří tabulky, které mají názvy stejné jako `DbSet` názvy vlastností. Názvy vlastností pro kolekce jsou obvykle plural (Students spíše než student), ale vývojáři nesouhlasí, zda by měly být názvy tabulek v množném čísle. V těchto kurzech potlačíte výchozí chování zadáním názvů tabulek na jednotném čísle v DbContext. K tomu přidejte následující zvýrazněný kód za poslední vlastnost Negenerickými.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Zaregistrovat SchoolContext

ASP.NET Core implementuje [vkládání závislostí](../../fundamentals/dependency-injection.md) ve výchozím nastavení. Služby (jako kontext databáze EF) jsou registrovány pomocí injektáže závislosti během spuštění aplikace. Komponenty, které vyžadují tyto služby (například řadiče MVC), poskytují tyto služby prostřednictvím parametrů konstruktoru. V tomto kurzu se zobrazí kód konstruktoru kontroleru, který získá instanci kontextu později.

Pokud se chcete zaregistrovat `SchoolContext` jako služba, otevřete *Startup.cs*a přidejte zvýrazněné řádky do `ConfigureServices` metody.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

Název připojovacího řetězce je předán do kontextu voláním metody pro `DbContextOptionsBuilder` objekt. Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.

Přidejte `using` příkazy pro `ContosoUniversity.Data` a `Microsoft.EntityFrameworkCore` obory názvů a potom Sestavte projekt.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Otevřete *appsettings.jsv* souboru a přidejte připojovací řetězec, jak je znázorněno v následujícím příkladu.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Připojovací řetězec určuje SQL Server databázi LocalDB. LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace. Ve výchozím nastavení LocalDB vytvoří soubory databáze *. mdf* v `C:/Users/<user>` adresáři.

## <a name="initialize-db-with-test-data"></a>Inicializovat databázi s testovacími daty

Entity Framework pro vás vytvoří prázdnou databázi. V této části napíšete metodu, která je volána po vytvoření databáze, aby se naplnila testovacími daty.

Tady použijete `EnsureCreated` metodu k automatickému vytvoření databáze. V [pozdějším kurzu](migrations.md) se dozvíte, jak zpracovat změny modelu pomocí migrace Code First ke změně schématu databáze místo vyřazení a opětovnému vytvoření databáze.

Ve složce *data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a nahraďte kód šablony následujícím kódem, což způsobí, že se databáze vytvoří v případě potřeby a načte testovací data do nové databáze.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Kód kontroluje, zda jsou v databázi nějaké studenty, a pokud ne, předpokládá se, že je databáze nová a je nutné ji naplnit testovacími daty. Načte testovací data do polí `List<T>` , nikoli kolekce pro optimalizaci výkonu.

V *program.cs*upravte `Main` metodu tak, aby při spuštění aplikace provedete následující:

* Získá instanci kontextu databáze z kontejneru vkládání závislostí.
* Zavolejte metodu počáteční hodnoty a předejte jí kontext.
* Uvolněte kontext, pokud je metoda počáteční hodnoty hotová.

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Přidat `using` příkazy:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

Ve starších kurzech se můžete podívat na podobný kód v `Configure` metodě v *Startup.cs*. Tuto metodu doporučujeme použít `Configure` jenom k nastavení kanálu požadavků. Spouštěcí kód aplikace patří do `Main` metody.

Nyní při prvním spuštění aplikace se vytvoří databáze a dokončí se testovacími daty. Kdykoli změníte datový model, můžete databázi odstranit, aktualizovat metodu počáteční hodnoty a začít afresh s novou databází stejným způsobem. V dalších kurzech se dozvíte, jak změnit databázi při změně datového modelu, aniž byste ho museli odstranit a znovu vytvořit.

## <a name="create-controller-and-views"></a>Vytvořit kontroler a zobrazení

V dalším kroku použijete modul generování uživatelského rozhraní v aplikaci Visual Studio a přidáte kontroler MVC a zobrazení, které budou používat EF k dotazování a ukládání dat.

Automatické vytváření metod a zobrazení akcí CRUD se označuje jako generování uživatelského rozhraní. Generování uživatelského rozhraní se liší od generování kódu v tom, že generovaný kód je výchozím bodem, který lze upravit tak, aby vyhovoval vašim vlastním požadavkům, zatímco obvykle neupravujete generovaný kód. Pokud potřebujete přizpůsobit generovaný kód, použijete částečné třídy nebo znovu vygenerujete kód při změně.

* V **Průzkumník řešení** klikněte pravým tlačítkem na složku **řadiče** a vyberte **Přidat > nová vygenerovaná položka**.

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

Vložení závislostí ASP.NET Core se postará o předání instance `SchoolContext` do kontroleru. V souboru *Startup.cs* jste předtím nakonfigurovali.

Kontroler obsahuje `Index` metodu akce, která zobrazí všechny studenty v databázi. Metoda získá seznam studentů ze sady entit studentů načtením `Students` vlastnosti instance kontextu databáze:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

O asynchronních programovacích prvcích v tomto kódu se dozvíte později v tomto kurzu.

Zobrazení */studenty/index. cshtml* zobrazí tento seznam v tabulce:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Stisknutím kombinace kláves CTRL + F5 spusťte projekt nebo zvolte možnost **ladění > spustit bez ladění** z nabídky.

Kliknutím na kartu Students zobrazíte testovací data, která `DbInitializer.Initialize` Metoda vložila. V závislosti na tom, jak úzká je okno prohlížeče, uvidíte v `Students` horní části stránky odkaz na kartu nebo v pravém horním rohu klikněte na navigační ikonu, aby se odkaz zobrazil.

![Úzká Domovská stránka společnosti Contoso University](intro/_static/home-page-narrow.png)

![Stránka indexu studentů](intro/_static/students-index.png)

## <a name="view-the-database"></a>Zobrazení databáze

Při spuštění aplikace je `DbInitializer.Initialize` volána metoda `EnsureCreated` . EF zjistil, že neexistovala žádná databáze a proto byla vytvořena, a potom zbytek `Initialize` kódu metody naplní databázi daty. K zobrazení databáze v aplikaci Visual Studio můžete použít **Průzkumník objektů systému SQL Server** (SSOX).

Zavřete prohlížeč.

Pokud okno SSOX ještě není otevřené, vyberte ho v nabídce **zobrazení** v aplikaci Visual Studio.

V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze**a potom klikněte na položku pro název databáze, která se nachází v připojovacím řetězci v *appsettings.jsv* souboru.

Rozbalte uzel **tabulky** , aby se zobrazily tabulky v databázi.

![Tabulky v SSOX](intro/_static/ssox-tables.png)

Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte sloupce, které byly vytvořeny, a řádky, které byly vloženy do tabulky.

![Tabulka studenta v SSOX](intro/_static/ssox-student-table.png)

Soubory databáze *. mdf* a *. ldf* jsou ve složce *C:\Users \\ \<yourusername> * .

Vzhledem k tomu, že zavoláte `EnsureCreated` metodu inicializátoru, která se spouští při spuštění aplikace, můžete nyní provést změnu `Student` třídy, odstranit databázi, znovu spustit aplikaci a databáze se automaticky znovu vytvoří, aby odpovídala vaší změně. Například pokud přidáte `EmailAddress` vlastnost do `Student` třídy, zobrazí se nový `EmailAddress` sloupec v znovu vytvořené tabulce.

## <a name="conventions"></a>Konvence

Množství kódu, který jste museli zapsat, aby Entity Framework mohl vytvořit úplnou databázi, je minimální z důvodu použití konvencí nebo předpokladů, které Entity Framework provádí.

* Názvy `DbSet` vlastností se používají jako názvy tabulek. Pro entity, na které není odkazováno pomocí `DbSet` vlastnosti, se názvy tříd entit používají jako názvy tabulek.

* Názvy vlastností entit se používají pro názvy sloupců.

* Vlastnosti entity s názvem ID nebo classnameID jsou rozpoznány jako vlastnosti primárního klíče.

* Vlastnost je interpretována jako vlastnost cizího klíče, pokud má název *\<navigation property name>\<primary key property name>* (například `StudentID` pro `Student` vlastnost navigace, protože `Student` primární klíč entity je `ID` ). Vlastnosti cizího klíče lze také pojmenovat jednoduše *\<primary key property name>* (například vzhledem k tomu, že `EnrollmentID` `Enrollment` primární klíč entity je `EnrollmentID` ).

Konvenční chování se dá přepsat. Můžete například explicitně zadat názvy tabulek, jak jste viděli dříve v tomto kurzu. A můžete nastavit názvy sloupců a nastavit jakoukoli vlastnost jako primární klíč nebo cizí klíč, protože se v [pozdějším kurzu](complex-data-model.md) v této sérii zobrazí.

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.

Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna. Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna. Pomocí synchronního kódu může být mnoho vláken svázáno s tím, že ve skutečnosti neprovádí žádnou práci, protože čeká na dokončení vstupně-výstupních operací. V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků. Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server je povolen pro zpracování většího objemu dat bez prodlev.

Asynchronní kód zavádí malé množství režie za běhu, ale u situací s nízkým objemem provozu je dosaženo zanedbatelného výkonu, zatímco v případě vysoké situace v provozu je potenciální zlepšení výkonu značné.

V následujícím kódu `async` klíčové slovo, `Task<T>` vrácené hodnoty, `await` klíčové slovo a `ToListAsync` Metoda provede asynchronní spouštění kódu.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async`Klíčové slovo instruuje kompilátor, aby vygeneroval zpětná volání pro části těla metody a automaticky vytvořila `Task<IActionResult>` vrácený objekt.

* Návratový typ `Task<IActionResult>` představuje průběžnou práci s výsledkem typu `IActionResult` .

* `await`Klíčové slovo způsobí, že kompilátor rozdělí metodu do dvou částí. První část končí asynchronně spuštěnou operací. Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.

* `ToListAsync`je asynchronní verze `ToList` metody rozšíření.

Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá Entity Framework:

* Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze. Který obsahuje například,, `ToListAsync` `SingleOrDefaultAsync` a `SaveChangesAsync` . Neobsahuje například příkazy, které pouze mění `IQueryable` , například `var students = context.Students.Where(s => s.LastName == "Davolio")` .

* Kontext EF není bezpečný pro přístup z více vláken: Nepokoušejte se souběžně provádět více operací. Při volání jakékoli asynchronní metody EF vždy použijte `await` klíčové slovo.

* Chcete-li využít výhod výkonu asynchronního kódu, zajistěte, aby všechny balíčky knihovny, které používáte (například pro stránkování), používaly také Async, pokud volají jakékoli Entity Framework metody, které způsobují odesílání dotazů do databáze.

Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/articles/standard/async).

## <a name="get-the-code"></a>Získání kódu

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořená webová aplikace ASP.NET Core MVC
> * Nastavení stylu webu
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

