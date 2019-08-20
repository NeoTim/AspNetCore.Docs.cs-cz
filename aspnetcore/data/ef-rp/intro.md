---
title: Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8
author: tdykstra
description: Ukazuje, jak vytvořit aplikaci pro stránky Razor pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 07/22/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 5e81b58ebec2d9a50784facd5425f0e7c3524e10
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583440"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8

Podle [Petr Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Toto je první v sérii kurzů, které ukazují, jak používat Entity Framework (EF) jádro v aplikaci ASP.NET Core Razor Pages. Kurzy vytvářejí web pro fiktivní univerzitě společnosti Contoso. Tato lokalita obsahuje funkce, jako je například využití studenta, vytváření kurzů a přiřazení instruktorů.

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Požadavky

* Pokud s Razor Pages teprve začínáte, Projděte si kurz [Začínáme s Razor Pagesm](xref:tutorials/razor-pages/razor-pages-start) , než začnete s tímto.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Databázové moduly

Pokyny pro Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), verzi SQL Server Express, která se spouští jenom v systému Windows.

Pokyny pro Visual Studio Code používají [](https://www.sqlite.org/)Nástroj pro databázový stroj pro různé platformy.

Pokud se rozhodnete použít SQLite, Stáhněte a nainstalujte nástroj třetí strany pro správu a zobrazení databáze SQLite, jako je například [prohlížeč databáze pro SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na problém, který nemůžete vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Dobrý způsob, jak získat pomoc, je odeslání otázky do StackOverflow.com s použitím [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>Ukázková aplikace

Aplikace vytvořené v těchto kurzech je webová stránka základní university. Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem. Tady je několik obrazovek vytvořili v kurzu.

![Studenti indexová stránka](intro/_static/students-index30.png)

![Stránky pro úpravu studentů](intro/_static/student-edit30.png)

Styl uživatelského rozhraní tohoto webu je založen na předdefinovaných šablonách projektů. V tomto kurzu se naučíte, jak používat EF Core, nikoli způsob přizpůsobení uživatelského rozhraní.

Pomocí odkazu v horní části stránky Získejte zdrojový kód dokončeného projektu. Složka *cu30* obsahuje kód pro verzi kurzu ASP.NET Core 3,0. Soubory, které reflektují stav kódu pro kurzy 1-7, najdete ve složce *cu30snapshots* .

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Spuštění aplikace po stažení dokončeného projektu:

* Odstraňte tři soubory a jednu složku, která má v názvu název *SQLite* .
* Sestavte projekt.
* V konzole správce balíčků (PMC) spusťte následující příkaz:

  ```powershell
  Update-Database
  ```

* Spusťte projekt k osazení databáze.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spuštění aplikace po stažení dokončeného projektu:

* Odstraňte *ContosoUniversity. csproj*a přejmenujte *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.
* Odstraňte *Startup.cs*a přejmenujte *StartupSQLite.cs* na *Startup.cs*.
* Odstraňte *appSettings. JSON*a přejmenujte *appSettingsSQLite.* JSON na *appSettings. JSON*.
* Odstraňte složku *migrace* a přejmenujte *MigrationsSQL* na *migrace*.
* Sestavte projekt.
* Na příkazovém řádku ve složce projektu spusťte následující příkazy:

  ```console
  dotnet tool install --global dotnet-ef --version 3.0.0-*
  dotnet ef database update
  ```

* V nástroji SQLite spusťte následující příkaz SQL:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Spusťte projekt k osazení databáze.

---

## <a name="create-the-web-app-project"></a>Vytvoření projektu webové aplikace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.
* Vyberte **webová aplikace ASP.NET Core**.
* Pojmenujte projekt *ContosoUniversity*. Je důležité použít tento přesný název, včetně velkých a malých písmen, aby se obory názvů shodovaly při zkopírování a vložení kódu.
* V rozevíracích seznamech vyberte **.NET Core** a **ASP.NET Core 3,0** a potom vyberte **Webová aplikace**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V terminálu přejděte do složky, ve které by měla být vytvořena složka projektu.

* Spuštěním následujících příkazů vytvořte projekt Razor Pages a `cd` do nové složky projektu:

  ```console
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Nastavit styl lokality

Pomocí aktualizace *stránek/Shared/_Layout. cshtml*nastavte záhlaví webu, zápatí a nabídku.

* Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso". Existují tři výskyty.

* Odstraňte položky nabídky **Domů** a **Ochrana osobních údajů** a přidejte záznamy **o o** **studentech**, **kurzech**, **instruktorech**a **odděleních**.

Změny jsou zvýrazněné.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET Core textem této aplikace:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Spusťte aplikaci, abyste ověřili, že se zobrazí domovská stránka.

## <a name="the-data-model"></a>Datový model

V následujících částech se vytvoří datový model:

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

Student se může zaregistrovat v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.

## <a name="the-student-entity"></a>Entita studenta

![Diagram entity studenta](intro/_static/student-entity.png)

* Vytvořte složku *modely* ve složce projektu. 

* Vytvořte *modely/studenta. cs* pomocí následujícího kódu:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

`ID` Vlastnost se zobrazí jako sloupec primárního klíče tabulky databáze, která odpovídá této třídě. Ve výchozím nastavení interpretuje EF Core vlastnost s názvem `ID` nebo `classnameID` jako primární klíč. Proto je `Student` `StudentID`alternativní automaticky rozpoznaný název pro primární klíč třídy.

`Enrollments` Je vlastnost [navigační vlastnost](/ef/core/modeling/relationships). Navigační vlastnosti obsahují další entity, které se vztahují k této entitě. V takovém případě `Enrollments` vlastnost `Student` `Enrollment` entity obsahuje všechny entity, které se vztahují k danému studentovi. Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě entity registrace. 

V databázi se řádek registrace vztahuje k řádku studenta, pokud jeho sloupec StudentID obsahuje hodnotu ID studenta. Předpokládejme například, že řádek studenta má ID = 1. Související řádky registrace budou mít StudentID = 1. StudentID je *cizí klíč* v tabulce zápisu. 

Vlastnost je definována tak, `ICollection<Enrollment>` že může existovat více souvisejících entit zápisu. `Enrollments` Můžete použít jiné typy kolekce, například `List<Enrollment>` nebo. `HashSet<Enrollment>` Když `ICollection<Enrollment>` je používají, vytvoří EF Core `HashSet<Enrollment>` kolekcí ve výchozím nastavení.

## <a name="the-enrollment-entity"></a>Registrace entity

![Diagram entity registrace](intro/_static/enrollment-entity.png)

Vytvořte *modely/registrace. cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

Vlastnost je primární klíč. Tato entita `classnameID` používá vzor namísto `ID` samotného. `EnrollmentID` Pro model produkčních dat vyberte jeden vzor a používejte ho konzistentně. Tento kurz používá pouze k ilustraci toho, jak funguje. Použití `ID` bez `classname` toho usnadňuje implementaci některých druhů změn datového modelu.

`Grade` Vlastnost je `enum`. Otazník po `Grade` deklaraci typu označuje, že vlastnost může `Grade` [mít hodnotu null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/). Třídu, která má hodnotu null, se liší od nulové&mdash;třídy null znamená, že známka není známa nebo ještě nebyla přiřazena.

`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`. `Enrollment` Entita je přidružený nejméně k jednomu `Student` entity, tak vlastnost obsahuje jediný `Student` entity.

`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`. `Enrollment` Entita je přidružený nejméně k jednomu `Course` entity.

EF Core interpretuje vlastnost jako cizí klíč, pokud je název `<navigation property name><primary key property name>`. Například`StudentID` je cizí klíč `Student` pro `Student` navigační vlastnost, protože primární klíč entity je `ID`. Vlastnosti cizího klíče může mít také název `<primary key property name>`. Například `CourseID` vzhledem k tomu, `Course` je primární klíč entity `CourseID`.

## <a name="the-course-entity"></a>Kurz entity

![Diagram kurzu entity](intro/_static/course-entity.png)

Vytvořte *modely/Course. cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

`Enrollments` Je navigační vlastnost. A `Course` entit může souviset s libovolným počtem `Enrollment` entity.

`DatabaseGenerated` Atribut umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.

Sestavte projekt, aby se ověřilo, že nejsou k dispozici žádné chyby kompilátoru.

## <a name="scaffold-student-pages"></a>Stránky studenta pro generování uživatelského rozhraní

V této části použijete nástroj ASP.NET Core pro generování uživatelského rozhraní k vygenerování:

* Třída *kontextu* EF Core. Kontext je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model. Je odvozen z `Microsoft.EntityFrameworkCore.DbContext` třídy.
* Stránky Razor, které zpracovávají operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro `Student` entitu.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vytvořte složku *Students* ve složce *stránky* .
* V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* a vyberte **Přidat** > **novou vygenerované položky**.
* V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **stránky Razor pomocí Entity Frameworku (CRUD)** > **přidat**.
* V dialogovém okně **přidat Razor Pages pomocí Entity Framework (CRUD)** :
  * V **třída modelu** rozevíracího seznamu, vyberte **Student (ContosoUniversity.Models)** .
  * V řádku **třídy kontextu dat** vyberte **+** znaménko (plus).
  * Změňte název kontextu dat z *ContosoUniversity. Models. ContosoUniversityContext* na *ContosoUniversity. data. SchoolContext*.
  * Vyberte **Přidat**.

Automaticky se nainstalují tyto balíčky:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Spuštěním následujících příkazů .NET Core CLI nainstalujte požadované balíčky NuGet:

  ```console
  dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools --version 3.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
  dotnet add package Microsoft.Extensions.Logging.Debug --version 3.0.0-*
  ```

  Pro generování uživatelského rozhraní je vyžadován balíček Microsoft. VisualStudio. Web. strategii. Design. I když aplikace nebude používat SQL Server, nástroj pro generování uživatelského rozhraní potřebuje balíček SQL Server.

* Vytvořte složku *stránky/studenty* .

* Spusťte následující příkaz pro instalaci nástroje pro [generování uživatelského rozhraní ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```console
  dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
  ```

* Spusťte následující příkaz pro generování uživatelského rozhraní stránek studenta.

  **Ve Windows**

  ```console
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **V systému macOS nebo Linux**

  ```console
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Pokud máte problém s předchozím krokem, sestavte projekt a opakujte krok generování uživatelského rozhraní.

Proces generování uživatelského rozhraní:

* Vytvoří stránky Razor ve složce *Pages/Students* :
  * *Vytvoření. cshtml* a *Create.cshtml.cs*
  * *Odstranění. cshtml* a *DELETE.cshtml.cs*
  * *Podrobnosti. cshtml* a *Details.cshtml.cs*
  * *Upravit. cshtml* a *Edit.cshtml.cs*
  * *Index. cshtml* a *index.cshtml.cs*
* Vytvoří *data/SchoolContext. cs*.
* Přidá kontext do injektáže závislosti v *Startup.cs*.
* Přidá připojovací řetězec databáze do souboru *appSettings. JSON*.

## <a name="database-connection-string"></a>Připojovací řetězec databáze

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí. Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* v `C:/Users/<user>` adresáři.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Změňte připojovací řetězec tak, aby odkazoval na soubor databáze SQLite s názvem *cu. DB*:

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Aktualizuje třídu kontextu databáze.

Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze. Kontext je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext určuje, které entity jsou zahrnuty v datovém modelu. V tomto projektu je s názvem třídy `SchoolContext`.

Aktualizace *SchoolContext.cs* následujícím kódem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Zvýrazněný kód vytvoří [DbSet\<TEntity >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastností pro každou sadu entit. V terminologii EF Core:

* Sada entit obvykle odpovídá databázové tabulce.
* Entita odpovídající řádek v tabulce.

Vzhledem k tomu, že sada entit obsahuje více entit, musí mít vlastnosti Negenerickými plurální názvy. Vzhledem k tomu, že nástroj pro`Student` generování uživatelského rozhraní vytvořil negenerickými, tento krok `Students`změny změní na plural. 

Chcete-li, aby kód Razor Pages odpovídal novému názvu negenerickými, proveďte globální změnu v celém projektu `_context.Student` na. `_context.Students`  K dispozici je 8 výskytů.

Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilátoru.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection). Služby (například kontext databáze EF Core) jsou registrovány pomocí injektáže závislosti při spuštění aplikace. Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru. Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.

Nástroj pro generování uživatelského rozhraní automaticky zaregistroval třídu kontextu pomocí kontejneru vkládání závislostí.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V `ConfigureServices`byly zvýrazněné řádky přidány pomocí generátoru:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V `ConfigureServices`nástroji se ujistěte, že kód přidaný voláním `UseSqlite`uživatelského rozhraní.

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu. Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.

## <a name="create-the-database"></a>Vytvoření databáze

Aktualizujte *program.cs* , aby se vytvořila databáze, pokud neexistuje:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) neprovede žádnou akci, pokud existuje databáze pro kontext. Pokud žádná databáze neexistuje, vytvoří databázi a schéma. `EnsureCreated`povolí následující pracovní postup pro zpracování změn datového modelu:

* Odstraňte databázi. Všechna existující data budou ztracena.
* Změňte datový model. Například přidejte `EmailAddress` pole.
* Spusťte aplikaci.
* `EnsureCreated`vytvoří databázi s novým schématem.

Tento pracovní postup funguje dobře v rané fázi vývoje, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data. Tato situace se liší v případě, že data, která byla zadána do databáze, musí být zachována. V takovém případě použijte migrace.

Později v rámci série kurzů odstraníte databázi, kterou vytvořil `EnsureCreated` , a místo toho použijete migrace. Databázi vytvořenou nástrojem `EnsureCreated` nelze aktualizovat pomocí migrací.

### <a name="test-the-app"></a>Testování aplikace

* Spusťte aplikaci.
* Vyberte **studenty** propojení a potom **vytvořit nový**.
* Otestujte upravit, podrobnosti a odkazy odstranit.

## <a name="seed-the-database"></a>Přidání dat do databáze

`EnsureCreated` Metoda vytvoří prázdnou databázi. V této části se přidá kód, který naplní databázi testovacími daty.

Vytvořte *data/DbInitializer. cs* pomocí následujícího kódu:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Kód kontroluje, zda v databázi existují studenti. Pokud neexistují studenti, přidá testovací data do databáze. Vytvoří testovací data v polích, nikoli `List<T>` kolekce pro optimalizaci výkonu.

* V *program.cs*nahraďte `EnsureCreated` volání `DbInitializer.Initialize` voláním:

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ````

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Spusťte aplikaci, odstraňte všechny záznamy studentů, které jste vytvořili dříve, a zastavte aplikaci.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .

---

* Restartujte aplikaci.

* Vyberte stránku Students a zobrazte si dosazený údaj.

## <a name="view-the-database"></a>Zobrazení databáze

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Otevřít **Průzkumník objektů systému SQL Server** (SSOX) z **zobrazení** nabídky v sadě Visual Studio.
* V SSOX vyberte **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}** . Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.
* Rozbalte **tabulky** uzlu.
* Klikněte pravým tlačítkem na **Student** tabulky a klikněte na tlačítko **Data zobrazení** vytvořené sloupce a řádky vložené do tabulky.
* Kliknutím pravým tlačítkem na tabulku **student** a kliknutím na **Zobrazit kód** zjistíte `Student` , jak se model `Student` mapuje na schéma tabulky.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Použijte nástroj SQLite k zobrazení schématu databáze a dat osazených daty. Databázový soubor má název *cu. DB* a je umístěn ve složce projektu.

---

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.

Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán. Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna. Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny. Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků. Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovávat více provozu bez prodlev.

Asynchronní kód zavést malé množství režie v době běhu. V situacích s nízkým provozem stiskněte výkonu je zanedbatelný, při vysoké návštěvnosti situacích, potenciálních zlepšení výkonu je důležitým.

V následujícím kódu [asynchronní](/dotnet/csharp/language-reference/keywords/async) – klíčové slovo, `Task<T>` návratovou hodnotu, `await` – klíčové slovo, a `ToListAsync` metoda změňte kód spustit asynchronně.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* `async` – Klíčové slovo instruuje kompilátor, aby:
  * Generovat zpětná volání pro části tělo metody.
  * Vytvořte vrácený objekt [úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) .
* `Task<T>` Návratový typ představuje probíhající práci.
* `await` – Klíčové slovo způsobí, že kompilátor metodu rozdělit do dvou částí. První část končí operace, která se spustí asynchronně. Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.
* `ToListAsync` je asynchronní verze `ToList` – metoda rozšíření.

Některé co je potřeba mít na paměti při zápisu asynchronního kódu, který používá EF Core:

* Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze. To zahrnuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`a .`SaveChangesAsync` Neměl by zahrnovat příkazy, které stačí změnit `IQueryable`, jako například `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Objekt context EF Core není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně.
* Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.

Další informace o asynchronním programování v rozhraní .NET najdete v tématu [asynchronní přehled](/dotnet/standard/async) a [asynchronní programování pomocí modifikátoru async a operátoru await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Další postup

> [!div class="step-by-step"]
> [Další kurz](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí Entity Framework (EF) Core.

Ukázková aplikace je webovou stránku pro fiktivní společnosti Contoso University. Zahrnuje funkce, jako student přijetí, kurz vytvoření a přiřazení instruktorem. Tato stránka je první ze série kurzů, které vysvětlují, jak vytvořit ukázková aplikace Contoso University.

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Znalost [stránky Razor](xref:razor-pages/index). By se měla dokončit programátory [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) před zahájením této řady.

## <a name="troubleshooting"></a>Poradce při potížích

Pokud narazíte na problém nevyřešíte sami, můžete najít řešení obvykle porovnáním kódu [dokončený projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Je dobrým způsobem, jak získat pomoc tím, že publikuje dotaz do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

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

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```CLI
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

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

* V **třída modelu** rozevíracího seznamu, vyberte **Student (ContosoUniversity.Models)** .
* V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a změňte vygenerovaný název, aby **ContosoUniversity.Models.SchoolContext**.
* V **třída kontextu dat** rozevíracího seznamu, vyberte **ContosoUniversity.Models.SchoolContext**
* Vyberte **Přidat**.

![Dialogové okno CRUD](intro/_static/s1.png)

Zobrazit [generování uživatelského rozhraní modelu film](xref:tutorials/razor-pages/model#scaffold-the-movie-model) Pokud máte potíže s předchozím kroku.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte následující příkazy k modelu studentů.

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Proces vygenerované uživatelské rozhraní vytvořit a změnit následující soubory:

### <a name="files-created"></a>Soubory vytvořené

* *Stránky/studenty* vytvoření, odstranění, podrobností, úpravy, Index.
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>Aktualizace souboru

* *Startup.cs* : Změny v tomto souboru jsou podrobně popsané v další části.
* *appsettings.json* : Přidaný připojovací řetězec použitý k připojení k místní databázi.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Prozkoumání kontextu registrovaný pomocí vkládání závislostí

ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace. Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru. Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.

Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.

Zkontrolujte `ConfigureServices` metoda ve *Startup.cs*. Zvýrazněný řádek byl přidán modulem scaffolder:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=5-6)]

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

Poznámka: Předchozí kód používá `Models` pro obor názvů (`namespace ContosoUniversity.Models`) spíše než `Data`. `Models`je konzistentní s kódem generovaným pomocí generování uživatelského rozhraní. Další informace najdete v [tomto problému s vygenerováním uživatelského rozhraní GitHub](https://github.com/aspnet/Scaffolding/issues/822).

Kód kontroluje, jestli na databáze nejsou všechny studenty. Pokud v databázi nejsou žádní studenti, databáze je inicializována s testovací data. Načte testovací data do pole spíše než `List<T>` kolekce za účelem optimalizace výkonu.

`EnsureCreated` Metoda automaticky vytvoří databáze pro kontext databáze. Pokud existuje databáze `EnsureCreated` vrátí beze změny databáze.

V *Program.cs*, upravte `Main` metodu chce volat `Initialize`:

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

Odstranit záznamy, které studenty a restartujte aplikaci. Pokud databáze není inicializován, nastavte zarážky `Initialize` a Diagnostikujte problém.

## <a name="view-the-db"></a>Zobrazení databáze

Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID. Proto bude název databáze "SchoolContext-{GUID}". Identifikátor GUID bude pro každého uživatele odlišný.
Otevřít **Průzkumník objektů systému SQL Server** (SSOX) z **zobrazení** nabídky v sadě Visual Studio.
V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}** .

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



## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Next](xref:data/ef-rp/crud)

::: moniker-end
