---
title: Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit aplikaci pro stránky Razor pomocí Entity Framework Core
ms.author: riande
ms.custom: seodec18
ms.date: 11/22/2018
uid: data/ef-rp/intro
ms.openlocfilehash: 0c12aa983f01285e27c10bba4e622b2d2ae0a1f2
ms.sourcegitcommit: 3c2ba9a0d833d2a096d9d800ba67a1a7f9491af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854442"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

Podle [Petr Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí Entity Framework (EF) Core.

Ukázková aplikace je webovou stránku pro fiktivní společnosti Contoso University. Zahrnuje funkce, jako student přijetí, kurz vytvoření a přiřazení instruktorem. Tato stránka je první ze série kurzů, které vysvětlují, jak vytvořit ukázková aplikace Contoso University.

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

[!INCLUDE [](~/includes/2.1-SDK.md)]

------

Znalost [stránky Razor](xref:razor-pages/index). By se měla dokončit programátory [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) před zahájením této řady.

## <a name="troubleshooting"></a>Poradce při potížích

Pokud narazíte na problém nevyřešíte sami, můžete najít řešení obvykle porovnáním kódu [dokončený projekt](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Je dobrým způsobem, jak získat pomoc tím, že publikuje dotaz do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>Webové aplikace Contoso University

Aplikace vytvořené v těchto kurzech je webová stránka základní university.

Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem. Tady je několik obrazovek vytvořili v kurzu.

![Studenti indexová stránka](intro/_static/students-index.png)

![Stránky pro úpravu studentů](intro/_static/student-edit.png)

Styl uživatelského rozhraní tohoto webu se blíží co je generována pomocí integrovaných šablon. Kurz zaměřuje se na EF Core se stránkami Razor, uživatelské rozhraní ne.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Vytvoření webové aplikace stránky Razor ContosoUniversity

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.
* Vytvořte novou webovou aplikaci ASP.NET Core. Pojmenujte projekt **ContosoUniversity**. Je důležité projekt pojmenujte *ContosoUniversity* tak obory názvů případy, kdy kód je zkopírované a vložené.
* Vyberte **ASP.NET Core 2.1** v rozevíracím seznamu a pak vyberte **webovou aplikaci**.

Obrázky v předchozích krocích, naleznete v tématu [vytvořit webová aplikace Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Spusťte aplikaci.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```CLI
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

------

## <a name="set-up-the-site-style"></a>Nastavit styl lokality

Několik změn nastavení v nabídce webu, rozložení a domovské stránky. Aktualizace *Pages/Shared/_Layout.cshtml* s následujícími změnami:

* Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso". Existují tři výskyty.

* Přidání položek nabídky **studenty**, **kurzy**, **Instruktoři**, a **oddělení**a odstranit **kontakt** položku nabídky.

Změny jsou zvýrazněné. (Všechny značky je *není* zobrazit.)

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

V *Pages/Index.cshtml*, nahraďte obsah souboru následující kód, který nahradí text o ASP.NET a MVC o této aplikaci:

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Vytvoření datového modelu

Vytvoření tříd entit pro aplikaci Contoso University. Začněte s následující tři entity:

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

Existuje vztah jeden mnoho mezi `Student` a `Enrollment` entity. Existuje vztah jeden mnoho mezi `Course` a `Enrollment` entity. Student může zaregistrovat libovolný počet kurzů. Kurz můžete mít libovolný počet studentů zaregistrovaná do něj.

V následujících částech je vytvořená třída pro každou z těchto entit.

### <a name="the-student-entity"></a>Entita studenta

![Diagram entity studenta](intro/_static/student-entity.png)

Vytvoření *modely* složky. V *modely* složce vytvořte soubor třídy *Student.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

`ID` Vlastnost se stane sloupec primárního klíče tabulky databáze (databáze), který odpovídá této třídy. Ve výchozím nastavení interpretuje EF Core vlastnost s názvem `ID` nebo `classnameID` jako primární klíč. V `classnameID`, `classname` je název třídy. Alternativním automaticky rozpozná, primární klíč je `StudentID` v předchozím příkladu.

`Enrollments` Je vlastnost [navigační vlastnost](/ef/core/modeling/relationships). Vlastnosti navigace propojit s dalšími subjekty, které se vztahují k této entity. V takovém případě `Enrollments` vlastnost `Student entity` obsahuje všechny `Enrollment` entity, které se vztahují k, které `Student`. Například pokud Student řádků v databázi má dva související řádky registrace `Enrollments` navigační vlastnost obsahuje tyto dvě `Enrollment` entity. Se souvisejícím `Enrollment` řádek je řádek, který obsahuje hodnotu primárního klíče student získal v `StudentID` sloupce. Předpokládejme například, studenta s ID = 1 obsahuje dva řádky `Enrollment` tabulky. `Enrollment` Tabulka obsahuje dva řádky s `StudentID` = 1. `StudentID` je cizí klíč v `Enrollment` tabulka, která určuje studentů v `Student` tabulky.

Pokud vlastnost navigace může obsahovat více entit, navigační vlastnost jako musí být typu seznamu `ICollection<T>`. `ICollection<T>` můžete zadat, nebo typu jako `List<T>` nebo `HashSet<T>`. Když `ICollection<T>` je používají, vytvoří EF Core `HashSet<T>` kolekcí ve výchozím nastavení. Navigační vlastnosti, které obsahují více entit, pocházejí z many-to-many a jeden mnoho relací.

### <a name="the-enrollment-entity"></a>Registrace entity

![Diagram entity registrace](intro/_static/enrollment-entity.png)

V *modely* složku, vytvořte *Enrollment.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID` Vlastnost představuje primární klíč. Tato entita používá `classnameID` vzorku místo `ID` stejně jako `Student` entity. Vývojáři obvykle zvolte jeden model a použít ho v rámci datového modelu. V pozdějších kurzech pomocí ID bez classname zobrazí zjednodušit implementaci dědičnosti v datovém modelu.

`Grade` Vlastnost je `enum`. Otazník po `Grade` deklarace typu znamená, že `Grade` vlastnost může mít hodnotu Null. Na podnikové úrovni, který má hodnotu null se liší od nulové třída – null znamená, že známku vyjádřenou není znám nebo ještě nebyly přiřazeny.

`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`. `Enrollment` Entita je přidružený nejméně k jednomu `Student` entity, tak vlastnost obsahuje jediný `Student` entity. `Student` Entity se liší od `Student.Enrollments` navigační vlastnost, která obsahuje více `Enrollment` entity.

`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`. `Enrollment` Entita je přidružený nejméně k jednomu `Course` entity.

EF Core interpretuje vlastnost jako cizí klíč, pokud je název `<navigation property name><primary key property name>`. Například`StudentID` pro `Student` navigační vlastnost, protože `Student` je primární klíč entity `ID`. Vlastnosti cizího klíče může mít také název `<primary key property name>`. Například `CourseID` vzhledem k tomu, `Course` je primární klíč entity `CourseID`.

### <a name="the-course-entity"></a>Kurz entity

![Diagram kurzu entity](intro/_static/course-entity.png)

V *modely* složku, vytvořte *Course.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

`Enrollments` Je navigační vlastnost. A `Course` entit může souviset s libovolným počtem `Enrollment` entity.

`DatabaseGenerated` Atribut umožňuje aplikacím určit primární klíč, místo databáze s jeho vygenerování.

## <a name="scaffold-the-student-model"></a>Vygenerované uživatelské rozhraní modelu studenta

V této části je automaticky generovaný model studentů. To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model studentů.

* Sestavte projekt.
* Vytvořte *stránek/studenty* složky.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumníka řešení**, klikněte pravým tlačítkem na *stránek/studenty* složky > **přidat** > **novou vygenerovanou položku**.
* V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **stránky Razor pomocí Entity Frameworku (CRUD)** > **přidat**.

Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:

* V **třída modelu** rozevíracího seznamu, vyberte **Student (ContosoUniversity.Models)**.
* V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a změňte vygenerovaný název, aby **ContosoUniversity.Models.SchoolContext**.
* V **třída kontextu dat** rozevíracího seznamu, vyberte **ContosoUniversity.Models.SchoolContext**
* Vyberte **Přidat**.

![Dialogové okno CRUD](intro/_static/s1.png)

Zobrazit [generování uživatelského rozhraní modelu film](xref:tutorials/razor-pages/model#scaffold-the-movie-model) Pokud máte potíže s předchozím kroku.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Spusťte následující příkazy k modelu studentů.

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
```
------

Proces vygenerované uživatelské rozhraní vytvořit a změnit následující soubory:

### <a name="files-created"></a>Soubory vytvořené

* *Stránky/studenty* vytvoření, odstranění, podrobností, úpravy, Index.
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>Aktualizace souboru

* *Startup.cs* : Změny tohoto souboru jsou podrobně popsané v další části.
* *appsettings.json* : Připojovací řetězec použitý pro připojení k místní databázi se přidá.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Prozkoumání kontextu registrovaný pomocí vkládání závislostí

ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace. Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru. Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.

Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.

Zkontrolujte `ConfigureServices` metoda ve *Startup.cs*. Zvýrazněný řádek byl přidán modulem scaffolder:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu. Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.

## <a name="update-main"></a>Aktualizovat hlavní

V *Program.cs*, změnit `Main` metoda můžete provádět následující:

* Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.
* Volání [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Uvolnění kontextu při `EnsureCreated` dokončení metody.

Následující kód ukazuje aktualizovaný *Program.cs* souboru.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` zajišťuje, že existuje databáze pro daný kontext. Pokud existuje, nedojde k žádné akci. Pokud neexistuje, pak se vytvoří databázi a její schéma. `EnsureCreated` k vytvoření databáze nepoužívá migrace. Databázi, která se vytvoří s `EnsureCreated` nelze později aktualizovat pomocí migrace.

`EnsureCreated` je volána při spuštění aplikace, která umožňuje následující pracovní postup:

* Odstranění databáze.
* Změna schématu DB (například přidat `EmailAddress` pole).
* Spusťte aplikaci.
* `EnsureCreated` Databáze se vytvoří`EmailAddress` sloupce.

`EnsureCreated` Pokud schéma je rychle se vyvíjejícími je vhodné v rané fázi vývoje. Později v tomto kurzu se odstraní databáze a používají se migrace.

### <a name="test-the-app"></a>Testování aplikace

Spusťte aplikaci a přijmout zásady souboru cookie. Tato aplikace nemá uchovává osobní údaje. Informace o souboru cookie zásadami [EU obecného Regulation (GDPR) podporu](xref:security/gdpr).

* Vyberte **studenty** propojení a potom **vytvořit nový**.
* Otestujte upravit, podrobnosti a odkazy odstranit.

## <a name="examine-the-schoolcontext-db-context"></a>Prozkoumání kontextu SchoolContext DB

Hlavní třída, která koordinuje EF Core funkce pro daný datový model je třídy kontextu databáze. Kontext dat je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje entit, které jsou zahrnuty v datovém modelu. V tomto projektu je s názvem třídy `SchoolContext`.

Aktualizace *SchoolContext.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Zvýrazněný kód vytvoří [DbSet\<TEntity >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastností pro každou sadu entit. V terminologii EF Core:

* Obvykle sadu entit odpovídá Databázové tabulce.
* Entita odpovídající řádek v tabulce.

`DbSet<Enrollment>` a `DbSet<Course>` může vynechat. EF Core je obsahuje implicitně protože `Student` odkazy na entity `Enrollment` entity a `Enrollment` odkazy na entity `Course` entity. Pro účely tohoto kurzu ponechte `DbSet<Enrollment>` a `DbSet<Course>` v `SchoolContext`.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí. LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace. Ve výchozím nastavení LocalDB vytvoří *.mdf* DB soubory `C:/Users/<user>` adresáře.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Přidejte kód pro inicializaci databáze se testovací data

EF Core vytvoří prázdná databáze. V této části `Initialize` metoda je zapsána do naplnit ho daty testu.

V *Data* složku, vytvořte nový soubor třídy *DbInitializer.cs* a přidejte následující kód:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Poznámka: Předchozí kód používá `Models` pro obor názvů (`namespace ContosoUniversity.Models`) spíše než `Data`. `Models` je v souladu s generované generátor kódu. Další informace najdete v tématu [tento problém generování uživatelského rozhraní Githubu](https://github.com/aspnet/Scaffolding/issues/822).

Kód kontroluje, jestli na databáze nejsou všechny studenty. Pokud v databázi nejsou žádní studenti, databáze je inicializována s testovací data. Načte testovací data do pole spíše než `List<T>` kolekce za účelem optimalizace výkonu.

`EnsureCreated` Metoda automaticky vytvoří databáze pro kontext databáze. Pokud existuje databáze `EnsureCreated` vrátí beze změny databáze.

V *Program.cs*, upravte `Main` metodu chce volat `Initialize`:

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

Odstranit záznamy, které studenty a restartujte aplikaci. Pokud databáze není inicializován, nastavte zarážky `Initialize` a Diagnostikujte problém.

## <a name="view-the-db"></a>Zobrazení databáze

Otevřít **Průzkumník objektů systému SQL Server** (SSOX) z **zobrazení** nabídky v sadě Visual Studio.
V SSOX, klikněte na tlačítko **\MSSQLLocalDB (localdb) > databáze > ContosoUniversity1**.

Rozbalte **tabulky** uzlu.

Klikněte pravým tlačítkem na **Student** tabulky a klikněte na tlačítko **Data zobrazení** vytvořené sloupce a řádky vložené do tabulky.

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.

Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán. Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna. Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny. Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků. V důsledku toho asynchronního kódu umožňuje prostředky serveru použije efektivněji a aby zvládla větší provoz bez zpoždění je povoleno na serveru.

Asynchronní kód zavést malé množství režie v době běhu. V situacích s nízkým provozem stiskněte výkonu je zanedbatelný, při vysoké návštěvnosti situacích, potenciálních zlepšení výkonu je důležitým.

V následujícím kódu [asynchronní](/dotnet/csharp/language-reference/keywords/async) – klíčové slovo, `Task<T>` návratovou hodnotu, `await` – klíčové slovo, a `ToListAsync` metoda změňte kód spustit asynchronně.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* `async` – Klíčové slovo instruuje kompilátor, aby:
  * Generovat zpětná volání pro části tělo metody.
  * Automaticky vytvořit [úloh](/dotnet/api/system.threading.tasks.task) vráceného objektu. Další informace najdete v tématu [návratový typ úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Implicitní návratový typ `Task` představuje probíhající práci.
* `await` – Klíčové slovo způsobí, že kompilátor metodu rozdělit do dvou částí. První část končí operace, která se spustí asynchronně. Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.
* `ToListAsync` je asynchronní verze `ToList` – metoda rozšíření.

Některé co je potřeba mít na paměti při zápisu asynchronního kódu, který používá EF Core:

* Jenom příkazy, které způsobují dotazy nebo příkazy k odeslání do databáze se provedl asynchronně. Který obsahuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, a `SaveChangesAsync`. Neměl by zahrnovat příkazy, které stačí změnit `IQueryable`, jako například `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Objekt context EF Core není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně.
* Abyste mohli využívat výkony těží z asynchronní kód, ověřte, že balíčků knihovny (například pro stránkování) používat asynchronní, pokud volání metody EF Core, které odesílání dotazů do databáze.

Další informace o asynchronním programování v rozhraní .NET najdete v tématu [asynchronní přehled](/dotnet/standard/async) a [asynchronní programování pomocí modifikátoru async a operátoru await](/dotnet/csharp/programming-guide/concepts/async/).

V dalším kurzu, základní CRUD (vytváření, čtení, aktualizace nebo odstranění) jsou zkoumány operace.

::: moniker-end

> [!div class="step-by-step"]
> [Next](xref:data/ef-rp/crud)
