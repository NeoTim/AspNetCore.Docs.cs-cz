---
title: Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit aplikaci pro stránky Razor pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 94783aa9014aef4c5f775fc8f36a2c3a7715e4b6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656819"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Stránky Razor pomocí Entity Framework Core v ASP.NET Core – kurz 1 z 8

[Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Toto je první v sérii kurzů, které ukazují, jak používat Entity Framework (EF) jádro v aplikaci [ASP.NET Core Razor Pages](xref:razor-pages/index) . Kurzy vytvářejí web pro fiktivní univerzitě společnosti Contoso. Tato lokalita obsahuje funkce, jako je například využití studenta, vytváření kurzů a přiřazení instruktorů. Kurz používá první přístup ke kódu. Informace o tomto kurzu s použitím databáze prvního přístupu najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/16897).

[Stažení nebo zobrazení dokončené aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Předpoklady

* Pokud s Razor Pages teprve začínáte, Projděte si kurz [Začínáme s Razor Pagesm](xref:tutorials/razor-pages/razor-pages-start) , než začnete s tímto.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Databázové moduly

Pokyny pro Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), verzi SQL Server Express, která se spouští jenom v systému Windows.

Pokyny pro [Visual Studio Code používají nástroj](https://www.sqlite.org/)pro databázový stroj pro různé platformy.

Pokud se rozhodnete použít SQLite, Stáhněte a nainstalujte nástroj třetí strany pro správu a zobrazení databáze SQLite, jako je například [prohlížeč databáze pro SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na problém, který nemůžete vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Dobrý způsob, jak získat pomoc, je odeslání otázky do StackOverflow.com s použitím [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>Ukázková aplikace

Aplikace vytvořené v těchto kurzech je webová stránka základní university. Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem. Tady je několik obrazovek vytvořili v kurzu.

![Studenti indexová stránka](intro/_static/students-index30.png)

![Stránky pro úpravu studentů](intro/_static/student-edit30.png)

Styl uživatelského rozhraní tohoto webu je založen na předdefinovaných šablonách projektů. V tomto kurzu se naučíte, jak používat EF Core, nikoli způsob přizpůsobení uživatelského rozhraní.

Pomocí odkazu v horní části stránky Získejte zdrojový kód dokončeného projektu. Složka *cu30* obsahuje kód pro verzi kurzu ASP.NET Core 3,0. Soubory, které reflektují stav kódu pro kurzy 1-7, najdete ve složce *cu30snapshots* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Spuštění aplikace po stažení dokončeného projektu:

* Odstraňte tři soubory a jednu složku, která má v názvu název *SQLite* .
* Sestavte projekt.
* V konzole správce balíčků (PMC) spusťte následující příkaz:

  ```powershell
  Update-Database
  ```

* Spusťte projekt k osazení databáze.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spuštění aplikace po stažení dokončeného projektu:

* Odstraňte *ContosoUniversity. csproj*a přejmenujte *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.
* Odstraňte *Startup.cs*a přejmenujte *StartupSQLite.cs* na *Startup.cs*.
* Odstraňte *appSettings. JSON*a přejmenujte *appSettingsSQLite.* JSON na *appSettings. JSON*.
* Odstraňte složku *migrace* a přejmenujte *MigrationsSQL* na *migrace*.
* Sestavte projekt.
* Na příkazovém řádku ve složce projektu spusťte následující příkazy:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* V nástroji SQLite spusťte následující příkaz SQL:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Spusťte projekt k osazení databáze.

---

## <a name="create-the-web-app-project"></a>Vytvoření projektu webové aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**.
* Vyberte **ASP.NET Core webové aplikace**.
* Pojmenujte projekt *ContosoUniversity*. Je důležité použít tento přesný název, včetně velkých a malých písmen, aby se obory názvů shodovaly při zkopírování a vložení kódu.
* V rozevíracích seznamech vyberte **.NET Core** a **ASP.NET Core 3,0** a potom vyberte **Webová aplikace**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V terminálu přejděte do složky, ve které by měla být vytvořena složka projektu.

* Spuštěním následujících příkazů vytvořte projekt Razor Pages a `cd` do nové složky projektu:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Nastavit styl lokality

Pomocí aktualizace *stránek/Shared/_Layout. cshtml*nastavte záhlaví webu, zápatí a nabídku. cshtml:

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

Vlastnost `ID` se zobrazí jako sloupec primárního klíče tabulky databáze, který odpovídá této třídě. Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč. Proto je `StudentID`automaticky rozpoznaný název primárního klíče `Student` třídy.

Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships). Navigační vlastnosti obsahují další entity, které se vztahují k této entitě. V tomto případě obsahuje vlastnost `Enrollments` `Student` entity všechny `Enrollment` entit, které souvisejí s tímto studentem. Například pokud má řádek studenta v databázi dva související řádky registrace, navigační vlastnost `Enrollments` obsahuje tyto dvě entity registrace. 

V databázi se řádek registrace vztahuje k řádku studenta, pokud jeho sloupec StudentID obsahuje hodnotu ID studenta. Předpokládejme například, že řádek studenta má ID = 1. Související řádky registrace budou mít StudentID = 1. StudentID je *cizí klíč* v tabulce zápisu. 

Vlastnost `Enrollments` je definována jako `ICollection<Enrollment>`, protože může existovat více souvisejících entit zápisu. Můžete použít jiné typy kolekce, například `List<Enrollment>` nebo `HashSet<Enrollment>`. Při použití `ICollection<Enrollment>` EF Core ve výchozím nastavení vytvoří kolekci `HashSet<Enrollment>`.

## <a name="the-enrollment-entity"></a>Registrace entity

![Diagram entity registrace](intro/_static/enrollment-entity.png)

Vytvořte *modely/registrace. cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

Vlastnost `EnrollmentID` je primárním klíčem; Tato entita používá `classnameID` vzor místo `ID` sám sebou. Pro model produkčních dat vyberte jeden vzor a používejte ho konzistentně. Tento kurz používá pouze k ilustraci toho, jak funguje. Použití `ID` bez `classname` usnadňuje implementaci některých druhů změn datového modelu.

Vlastnost `Grade` je `enum`. Otazník po deklaraci typu `Grade` označuje, že vlastnost `Grade` může [mít hodnotu null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/). Nulová hodnota, která je null, se liší od nulové třídy&mdash;hodnota null znamená, že se neznámí nebo ještě není přiřazená žádná ze stupňů.

Vlastnost `StudentID` je cizí klíč a odpovídající navigační vlastnost je `Student`. Entita `Enrollment` je přidružená k jedné entitě `Student`, takže tato vlastnost obsahuje jednu entitu `Student`.

Vlastnost `CourseID` je cizí klíč a odpovídající navigační vlastnost je `Course`. Entita `Enrollment` je přidružená k jedné entitě `Course`.

EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>`. `StudentID` je například cizí klíč pro vlastnost `Student` navigace, protože je `ID`primární klíč entity `Student`. Vlastnosti cizího klíče mohou být také pojmenovány `<primary key property name>`. `CourseID` například, protože je primární klíč entity `Course` `CourseID`.

## <a name="the-course-entity"></a>Kurz entity

![Diagram kurzu entity](intro/_static/course-entity.png)

Vytvořte *modely/Course. cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

Vlastnost `Enrollments` je navigační vlastnost. Entita `Course` může souviset s libovolným počtem entit `Enrollment`.

Atribut `DatabaseGenerated` umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.

Sestavte projekt, aby se ověřilo, že nejsou k dispozici žádné chyby kompilátoru.

## <a name="scaffold-student-pages"></a>Stránky studenta pro generování uživatelského rozhraní

V této části použijete nástroj ASP.NET Core pro generování uživatelského rozhraní k vygenerování:

* Třída *kontextu* EF Core. Kontext je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model. Je odvozena z třídy `Microsoft.EntityFrameworkCore.DbContext`.
* Stránky Razor, které zpracovávají operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro entitu `Student`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vytvořte složku *Students* ve složce *stránky* .
* V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* a vyberte **Přidat** > **Nová vygenerovaná položka**.
* V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.
* V dialogovém okně **přidat Razor Pages pomocí Entity Framework (CRUD)** :
  * V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)** .
  * V řádku **Třída kontextu dat** vyberte symbol **+** (plus).
  * Změňte název kontextu dat z *ContosoUniversity. Models. ContosoUniversityContext* na *ContosoUniversity. data. SchoolContext*.
  * Vyberte **Přidat**.

Automaticky se nainstalují tyto balíčky:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Spuštěním následujících příkazů .NET Core CLI nainstalujte požadované balíčky NuGet:
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  Pro generování uživatelského rozhraní je vyžadován balíček Microsoft. VisualStudio. Web. strategii. Design. I když aplikace nebude používat SQL Server, nástroj pro generování uživatelského rozhraní potřebuje balíček SQL Server.

* Vytvořte složku *stránky/studenty* .

* Spusťte následující příkaz pro instalaci nástroje pro [generování uživatelského rozhraní ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Spusťte následující příkaz pro generování uživatelského rozhraní stránek studenta.

  **Ve Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **V systému macOS nebo Linux**

  ```dotnetcli
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

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí. Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* v adresáři `C:/Users/<user>`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Změňte připojovací řetězec tak, aby odkazoval na soubor databáze SQLite s názvem *cu. DB*:

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Aktualizuje třídu kontextu databáze.

Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze. Kontext je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext určuje, které entity jsou zahrnuty v datovém modelu. V tomto projektu je třída pojmenována `SchoolContext`.

*SchoolContext.cs* aktualizujte pomocí následujícího kódu:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Zvýrazněný kód vytvoří vlastnost [negenerickými\<TEntity >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit. V terminologii EF Core:

* Sada entit obvykle odpovídá databázové tabulce.
* Entita odpovídající řádek v tabulce.

Vzhledem k tomu, že sada entit obsahuje více entit, musí mít vlastnosti Negenerickými plurální názvy. Vzhledem k tomu, že nástroj pro generování uživatelského rozhraní vytvořil`Student` Negenerickými, tento krok ho změní na plural `Students`. 

Chcete-li, aby kód Razor Pages odpovídal novému názvu Negenerickými, proveďte globální změnu v celém projektu `_context.Student` na `_context.Students`.  K dispozici je 8 výskytů.

Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilátoru.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection). Služby (například kontext databáze EF Core) jsou registrovány pomocí injektáže závislosti při spuštění aplikace. Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru. Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.

Nástroj pro generování uživatelského rozhraní automaticky zaregistroval třídu kontextu pomocí kontejneru vkládání závislostí.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V `ConfigureServices`byly zvýrazněné řádky přidány pomocí nástroje pro generování uživatelského rozhraní:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V `ConfigureServices`se ujistěte, že kód přidaný generátorem volá `UseSqlite`.

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .

## <a name="create-the-database"></a>Vytvoření databáze

Aktualizujte *program.cs* , aby se vytvořila databáze, pokud neexistuje:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) neprovede žádnou akci, pokud existuje databáze pro kontext. Pokud žádná databáze neexistuje, vytvoří databázi a schéma. `EnsureCreated` povolí následující pracovní postup pro zpracování změn datového modelu:

* Odstraňte databázi. Všechna existující data budou ztracena.
* Změňte datový model. Přidejte například pole `EmailAddress`.
* Spusťte aplikaci.
* `EnsureCreated` vytvoří databázi s novým schématem.

Tento pracovní postup funguje dobře v rané fázi vývoje, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data. Tato situace se liší v případě, že data, která byla zadána do databáze, musí být zachována. V takovém případě použijte migrace.

Později v rámci série kurzů odstraníte databázi vytvořenou `EnsureCreated` a místo toho použijete migrace. Databázi vytvořenou pomocí `EnsureCreated` nelze aktualizovat pomocí migrace.

### <a name="test-the-app"></a>Otestování aplikace

* Spusťte aplikaci.
* Vyberte odkaz **Students** a pak **vytvořte nový**.
* Otestujte upravit, podrobnosti a odkazy odstranit.

## <a name="seed-the-database"></a>Přidání dat do databáze

Metoda `EnsureCreated` vytvoří prázdnou databázi. V této části se přidá kód, který naplní databázi testovacími daty.

Vytvořte *data/DbInitializer. cs* pomocí následujícího kódu:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Kód kontroluje, zda v databázi existují studenti. Pokud neexistují studenti, přidá testovací data do databáze. Vytvoří testovací data v polích místo `List<T>` kolekce pro optimalizaci výkonu.

* V *program.cs*nahraďte volání `EnsureCreated` voláním `DbInitializer.Initialize`:

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .

---

* Restartujte aplikaci.

* Vyberte stránku Students a zobrazte si dosazený údaj.

## <a name="view-the-database"></a>Zobrazení databáze

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.
* V SSOX vyberte **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}** . Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.
* Rozbalte uzel **tabulky** .
* Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.
* Kliknutím pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit kód** zjistíte, jak se `Student` model mapuje na schéma `Student` tabulky.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Použijte nástroj SQLite k zobrazení schématu databáze a dat osazených daty. Databázový soubor má název *cu. DB* a je umístěn ve složce projektu.

---

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.

Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán. Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna. Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny. Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků. Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovávat více provozu bez prodlev.

Asynchronní kód zavést malé množství režie v době běhu. V situacích s nízkým provozem stiskněte výkonu je zanedbatelný, při vysoké návštěvnosti situacích, potenciálních zlepšení výkonu je důležitým.

V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota `await` klíčová slova a metoda `ToListAsync` provede asynchronní spouštění kódu.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* Klíčové slovo `async` instruuje kompilátor na:
  * Generovat zpětná volání pro části tělo metody.
  * Vytvořte vrácený objekt [úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) .
* Návratový typ `Task<T>` představuje probíhající práci.
* Klíčové slovo `await` způsobí, že kompilátor rozdělí metodu do dvou částí. První část končí operace, která se spustí asynchronně. Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.
* `ToListAsync` je asynchronní verze `ToList` metody rozšíření.

Některé co je potřeba mít na paměti při zápisu asynchronního kódu, který používá EF Core:

* Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze. To zahrnuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`a `SaveChangesAsync`. Neobsahuje příkazy, které pouze mění `IQueryable`, například `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Objekt context EF Core není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně.
* Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.

Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Další kroky

> [!div class="step-by-step"]
> [Další kurz](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí Entity Framework (EF) Core.

Ukázková aplikace je webovou stránku pro fiktivní společnosti Contoso University. Zahrnuje funkce, jako student přijetí, kurz vytvoření a přiřazení instruktorem. Tato stránka je první ze série kurzů, které vysvětlují, jak vytvořit ukázková aplikace Contoso University.

[Stažení nebo zobrazení dokončené aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Předpoklady

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Znalost [Razor Pages](xref:razor-pages/index). Noví Programátori by měli před spuštěním této série dokončit [Začínáme s Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na problém, který nelze vyřešit, můžete řešení obecně najít porovnáním kódu s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Dobrým způsobem, jak získat pomoc, je odeslání otázky do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>Webové aplikace Contoso University

Aplikace vytvořené v těchto kurzech je webová stránka základní university.

Uživatelé mohou zobrazit a aktualizovat Všichni studenti, kurz a informace instruktorem. Tady je několik obrazovek vytvořili v kurzu.

![Studenti indexová stránka](intro/_static/students-index.png)

![Stránky pro úpravu studentů](intro/_static/student-edit.png)

Styl uživatelského rozhraní tohoto webu se blíží co je generována pomocí integrovaných šablon. Kurz zaměřuje se na EF Core se stránkami Razor, uživatelské rozhraní ne.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Vytvoření webové aplikace stránky Razor ContosoUniversity

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**.
* Vytvořte novou webovou aplikaci ASP.NET Core. Pojmenujte projekt **ContosoUniversity**. Je důležité pojmenovat projekt *ContosoUniversity* , aby se obory názvů shodovaly, když je kód zkopírován/vložen.
* V rozevíracím seznamu vyberte **ASP.NET Core 2,1** a potom vyberte možnost **Webová aplikace**.

Obrázky předchozích kroků najdete v tématu [Vytvoření webové aplikace Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Spusťte aplikaci.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Nastavit styl lokality

Několik změn nastavení v nabídce webu, rozložení a domovské stránky. Update *Pages/Shared/_Layout. cshtml* s následujícími změnami:

* Změňte všechny výskyty "ContosoUniversity" na "University společnosti Contoso". Existují tři výskyty.

* Přidejte položky nabídky pro **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **kontakt** .

Změny jsou zvýrazněné. (Všechny *značky se nezobrazí* .)

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Vytvoření datového modelu

Vytvoření tříd entit pro aplikaci Contoso University. Začněte s následující tři entity:

![Kurz – registrace – studentech modelového diagramu](intro/_static/data-model-diagram.png)

Mezi entitami `Student` a `Enrollment` existuje vztah 1: n. Mezi entitami `Course` a `Enrollment` existuje vztah 1: n. Student může zaregistrovat libovolný počet kurzů. Kurz můžete mít libovolný počet studentů zaregistrovaná do něj.

V následujících částech je vytvořená třída pro každou z těchto entit.

### <a name="the-student-entity"></a>Entita studenta

![Diagram entity studenta](intro/_static/student-entity.png)

Vytvořte složku *modelů* . Ve složce *modely* vytvořte soubor třídy s názvem *student.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

Vlastnost `ID` se zobrazí jako sloupec primárního klíče tabulky databáze (DB), která odpovídá této třídě. Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč. V `classnameID``classname` je název třídy. Alternativní automaticky rozpoznaný primární klíč je `StudentID` v předchozím příkladu.

Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships). Vlastnosti navigace propojit s dalšími subjekty, které se vztahují k této entity. V tomto případě vlastnost `Enrollments` `Student entity` uchovává všechny `Enrollment` entity, které se vztahují k tomuto `Student`. Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě entity `Enrollment`. Související `Enrollment` řádek je řádek, který obsahuje hodnotu primárního klíče studenta ve sloupci `StudentID`. Předpokládejme například, že student s ID = 1 má dva řádky v tabulce `Enrollment`. Tabulka `Enrollment` má dva řádky s `StudentID` = 1. `StudentID` je cizí klíč v tabulce `Enrollment`, která určuje studenta v tabulce `Student`.

Pokud navigační vlastnost může obsahovat více entit, musí být vlastnost navigace typu seznam, například `ICollection<T>`. lze zadat `ICollection<T>` nebo typ jako `List<T>` nebo `HashSet<T>`. Při použití `ICollection<T>` EF Core ve výchozím nastavení vytvoří kolekci `HashSet<T>`. Navigační vlastnosti, které obsahují více entit, pocházejí z many-to-many a jeden mnoho relací.

### <a name="the-enrollment-entity"></a>Registrace entity

![Diagram entity registrace](intro/_static/enrollment-entity.png)

Ve složce *modely* vytvořte *Enrollment.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

Vlastnost `EnrollmentID` je primární klíč. Tato entita používá `classnameID` vzor místo `ID` jako entita `Student`. Vývojáři obvykle zvolte jeden model a použít ho v rámci datového modelu. V pozdějších kurzech pomocí ID bez classname zobrazí zjednodušit implementaci dědičnosti v datovém modelu.

Vlastnost `Grade` je `enum`. Otazník po deklaraci typu `Grade` označuje, že vlastnost `Grade` může mít hodnotu null. Na podnikové úrovni, který má hodnotu null se liší od nulové třída – null znamená, že známku vyjádřenou není znám nebo ještě nebyly přiřazeny.

Vlastnost `StudentID` je cizí klíč a odpovídající navigační vlastnost je `Student`. Entita `Enrollment` je přidružená k jedné entitě `Student`, takže tato vlastnost obsahuje jednu entitu `Student`. Entita `Student` se liší od `Student.Enrollments` navigační vlastnost, která obsahuje více entit `Enrollment`.

Vlastnost `CourseID` je cizí klíč a odpovídající navigační vlastnost je `Course`. Entita `Enrollment` je přidružená k jedné entitě `Course`.

EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>`. Například`StudentID` pro vlastnost `Student` navigace, protože je `ID`primární klíč entity `Student`. Vlastnosti cizího klíče mohou být také pojmenovány `<primary key property name>`. `CourseID` například, protože je primární klíč entity `Course` `CourseID`.

### <a name="the-course-entity"></a>Kurz entity

![Diagram kurzu entity](intro/_static/course-entity.png)

Ve složce *modely* vytvořte *Course.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

Vlastnost `Enrollments` je navigační vlastnost. Entita `Course` může souviset s libovolným počtem entit `Enrollment`.

Atribut `DatabaseGenerated` umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.

## <a name="scaffold-the-student-model"></a>Vygenerované uživatelské rozhraní modelu studenta

V této části je automaticky generovaný model studentů. To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model studentů.

* Sestavte projekt.
* Vytvořte složku *stránky/studenty* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *stránky/Students* > **Přidat** > **novou vygenerované položky**.
* V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.

Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :

* V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)** .
* V řádku **Třída kontextu dat** vyberte symbol **+** (plus) a změňte vygenerovaný název na **ContosoUniversity. Models. SchoolContext**.
* V rozevíracím seznamu **Třída kontextu dat** vyberte **ContosoUniversity. Models. SchoolContext.**
* Vyberte **Přidat**.

![Dialogové okno CRUD](intro/_static/s1.png)

Pokud máte problém s předchozím krokem, podívejte [se do části vygenerované modely filmů](xref:tutorials/razor-pages/model#scaffold-the-movie-model) .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte následující příkazy k modelu studentů.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Proces vygenerované uživatelské rozhraní vytvořit a změnit následující soubory:

### <a name="files-created"></a>Soubory vytvořené

* *Stránky/studenty* Vytvořit, odstranit, podrobnosti, upravit, index.
* *Data/SchoolContext. cs*

### <a name="file-updates"></a>Aktualizace souboru

* *Startup.cs* : změny v tomto souboru jsou podrobně popsané v další části.
* *appSettings. JSON* : Přidal se připojovací řetězec použitý k připojení k místní databázi.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Prozkoumání kontextu registrovaný pomocí vkládání závislostí

ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace. Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru. Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.

Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.

Projděte si metodu `ConfigureServices` v *Startup.cs*. Zvýrazněný řádek byl přidán modulem scaffolder:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .

## <a name="update-main"></a>Aktualizovat hlavní

V *program.cs*upravte metodu `Main` a proveďte následující:

* Instance kontextu databáze získáte z kontejneru pro vkládání závislostí.
* Zavolejte [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Vyřazení kontextu při dokončení metody `EnsureCreated`.

Následující kód ukazuje aktualizovaný soubor *program.cs* .

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` zajišťuje, aby databáze pro kontext existovala. Pokud existuje, nedojde k žádné akci. Pokud neexistuje, pak se vytvoří databázi a její schéma. `EnsureCreated` nepoužívá k vytvoření databáze migrace. Databázi vytvořenou pomocí `EnsureCreated` nelze později aktualizovat pomocí migrace.

`EnsureCreated` se volá při spuštění aplikace, což umožňuje následující pracovní postup:

* Odstranění databáze.
* Změňte schéma databáze (například přidejte pole `EmailAddress`).
* Spusťte aplikaci.
* `EnsureCreated` vytvoří databázi se sloupcem`EmailAddress`.

`EnsureCreated` je v brzké době ve vývoji pohodlná, když se schéma rychle vyvíjí. Později v tomto kurzu se odstraní databáze a používají se migrace.

### <a name="test-the-app"></a>Otestování aplikace

Spusťte aplikaci a přijmout zásady souboru cookie. Tato aplikace nemá uchovává osobní údaje. Informace o zásadách souborů cookie najdete v tématu [Podpora pro EU obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).

* Vyberte odkaz **Students** a pak **vytvořte nový**.
* Otestujte upravit, podrobnosti a odkazy odstranit.

## <a name="examine-the-schoolcontext-db-context"></a>Prozkoumání kontextu SchoolContext DB

Hlavní třída, která koordinuje EF Core funkce pro daný datový model je třídy kontextu databáze. Kontext dat je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje entit, které jsou zahrnuty v datovém modelu. V tomto projektu je třída pojmenována `SchoolContext`.

*SchoolContext.cs* aktualizujte pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Zvýrazněný kód vytvoří vlastnost [negenerickými\<TEntity >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit. V terminologii EF Core:

* Obvykle sadu entit odpovídá Databázové tabulce.
* Entita odpovídající řádek v tabulce.

`DbSet<Enrollment>` a `DbSet<Course>` mohou být vynechány. EF Core je implicitně obsahuje, protože entita `Student` odkazuje na entitu `Enrollment` a entita `Enrollment` odkazuje na entitu `Course`. V tomto kurzu ponechte `DbSet<Enrollment>` a `DbSet<Course>` v `SchoolContext`.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB je Odlehčená verze SQL serveru Express Database Engine a je určená pro vývoj aplikací, není použití v produkčním prostředí. LocalDB spustí na vyžádání a běží v uživatelském režimu, takže není bez složité konfigurace. Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* DB v adresáři `C:/Users/<user>`.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Přidejte kód pro inicializaci databáze se testovací data

EF Core vytvoří prázdná databáze. V této části je zapsána `Initialize` metoda pro naplnění testovacích dat.

Ve složce *data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a přidejte následující kód:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Poznámka: předchozí kód používá `Models` pro obor názvů (`namespace ContosoUniversity.Models`) místo `Data`. `Models` je konzistentní s kódem generovaným modulem generování uživatelského rozhraní. Další informace najdete v [tomto problému s vygenerováním uživatelského rozhraní GitHub](https://github.com/aspnet/Scaffolding/issues/822).

Kód kontroluje, jestli na databáze nejsou všechny studenty. Pokud v databázi nejsou žádní studenti, databáze je inicializována s testovací data. Načítá testovací data do polí místo `List<T>` kolekcí za účelem optimalizace výkonu.

Metoda `EnsureCreated` automaticky vytvoří databázi pro kontext databáze. Pokud databáze existuje, `EnsureCreated` se vrátí beze změny databáze.

V *program.cs*upravte metodu `Main` pro volání `Initialize`:

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .

---

## <a name="view-the-db"></a>Zobrazení databáze

Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID. Proto bude název databáze "SchoolContext-{GUID}". Identifikátor GUID bude pro každého uživatele odlišný.
Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.
V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}** .

Rozbalte uzel **tabulky** .

Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozím režimem pro ASP.NET Core a EF Core.

Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán. Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna. Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny. Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků. V důsledku toho asynchronního kódu umožňuje prostředky serveru použije efektivněji a aby zvládla větší provoz bez zpoždění je povoleno na serveru.

Asynchronní kód zavést malé množství režie v době běhu. V situacích s nízkým provozem stiskněte výkonu je zanedbatelný, při vysoké návštěvnosti situacích, potenciálních zlepšení výkonu je důležitým.

V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota `await` klíčová slova a metoda `ToListAsync` provede asynchronní spouštění kódu.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Klíčové slovo `async` instruuje kompilátor na:
  * Generovat zpětná volání pro části tělo metody.
  * Automaticky vytvořte vrácený objekt [úkolu](/dotnet/api/system.threading.tasks.task) . Další informace najdete v tématu [návratový typ úlohy](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Implicitní návratový typ `Task` představuje probíhající práci.
* Klíčové slovo `await` způsobí, že kompilátor rozdělí metodu do dvou částí. První část končí operace, která se spustí asynchronně. Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.
* `ToListAsync` je asynchronní verze `ToList` metody rozšíření.

Některé co je potřeba mít na paměti při zápisu asynchronního kódu, který používá EF Core:

* Jenom příkazy, které způsobují dotazy nebo příkazy k odeslání do databáze se provedl asynchronně. To zahrnuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`a `SaveChangesAsync`. Neobsahuje příkazy, které pouze mění `IQueryable`, například `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Objekt context EF Core není bezpečné pro vlákna: nedoporučujeme provádět více operací paralelně.
* Abyste mohli využívat výkony těží z asynchronní kód, ověřte, že balíčků knihovny (například pro stránkování) používat asynchronní, pokud volání metody EF Core, které odesílání dotazů do databáze.

Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).

V dalším kurzu, základní CRUD (vytváření, čtení, aktualizace nebo odstranění) jsou zkoumány operace.



## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Next](xref:data/ef-rp/crud)

::: moniker-end
