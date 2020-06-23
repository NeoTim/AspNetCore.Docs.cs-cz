---
title: RazorStránky s Entity Framework Core v ASP.NET Core – kurz 1 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit Razor aplikaci Pages pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/intro
ms.openlocfilehash: a6915da23124b7ed4bfaa982692635f9fc75f96a
ms.sourcegitcommit: 726b8c5cf92e6f6a4d0205787b19307e889d6240
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "82967507"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>RazorStránky s Entity Framework Core v ASP.NET Core – kurz 1 z 8

[Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Toto je první v sérii kurzů, které ukazují, jak používat Entity Framework (EF) jádro v aplikaci [ASP.NET Core Razor Pages](xref:razor-pages/index) . Kurzy vytvářejí web pro fiktivní univerzitě společnosti Contoso. Tato lokalita obsahuje funkce, jako je například využití studenta, vytváření kurzů a přiřazení instruktorů. Kurz používá první přístup ke kódu. Informace o tomto kurzu s použitím databáze prvního přístupu najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/16897).

[Stažení nebo zobrazení dokončené aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Požadavky

* Pokud se Razor stránkami teprve začínáte, Projděte si řadu kurzů [Začínáme se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) , než začnete s tímto.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Databázové moduly

Pokyny pro Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), verzi SQL Server Express, která se spouští jenom v systému Windows.

Pokyny pro [Visual Studio Code používají nástroj](https://www.sqlite.org/)pro databázový stroj pro různé platformy.

Pokud se rozhodnete použít SQLite, Stáhněte a nainstalujte nástroj třetí strany pro správu a zobrazení databáze SQLite, jako je například [prohlížeč databáze pro SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Odstraňování potíží

Pokud narazíte na problém, který nemůžete vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Dobrý způsob, jak získat pomoc, je odeslání otázky do StackOverflow.com s použitím [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>Ukázková aplikace

Aplikace sestavená v těchto kurzech je základním webem na univerzitě. Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech. Tady je několik obrazovek vytvořených v tomto kurzu.

![Stránka indexu studentů](intro/_static/students-index30.png)

![Stránka pro úpravy studentů](intro/_static/student-edit30.png)

Styl uživatelského rozhraní tohoto webu je založen na předdefinovaných šablonách projektů. V tomto kurzu se naučíte, jak používat EF Core, nikoli způsob přizpůsobení uživatelského rozhraní.

Pomocí odkazu v horní části stránky Získejte zdrojový kód dokončeného projektu. Složka *cu30* obsahuje kód pro verzi kurzu ASP.NET Core 3,0. Soubory, které reflektují stav kódu pro kurzy 1-7, najdete ve složce *cu30snapshots* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Spuštění aplikace po stažení dokončeného projektu:

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
* Odstraňte *appSettings.jsna*a přejmenujte *appSettingsSQLite.jsna* na *appSettings.jszapnuto*.
* Odstraňte složku *migrace* a přejmenujte *MigrationsSQL* na *migrace*.
* Proveďte globální hledání `#if SQLiteVersion` a odeberte `#if SQLiteVersion` příkaz a přidružený `#endif` příkaz.
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

* V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* Vyberte **ASP.NET Core webové aplikace**.
* Pojmenujte projekt *ContosoUniversity*. Je důležité použít tento přesný název, včetně velkých a malých písmen, aby se obory názvů shodovaly při zkopírování a vložení kódu.
* V rozevíracích seznamech vyberte **.NET Core** a **ASP.NET Core 3,0** a potom vyberte **Webová aplikace**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V terminálu přejděte do složky, ve které by měla být vytvořena složka projektu.

* Spuštěním následujících příkazů vytvořte Razor projekt stránky a `cd` do nové složky projektu:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Nastavení stylu webu

Pomocí aktualizace *stránek/Shared/_Layout. cshtml*nastavte záhlaví webu, zápatí a nabídku. cshtml:

* Změňte všechny výskyty "ContosoUniversity" na "contoso University". Existují tři výskyty.

* Odstraňte položky nabídky **Domů** a **Ochrana osobních údajů** a přidejte záznamy **o o** **studentech**, **kurzech**, **instruktorech**a **odděleních**.

Změny jsou zvýrazněny.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET Core textem této aplikace:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Spusťte aplikaci, abyste ověřili, že se zobrazí domovská stránka.

## <a name="the-data-model"></a>Datový model

V následujících částech se vytvoří datový model:

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

Student se může zaregistrovat v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.

## <a name="the-student-entity"></a>Entita studenta

![Diagram entity studenta](intro/_static/student-entity.png)

* Vytvořte složku *modely* ve složce projektu. 

* Vytvořte *modely/studenta. cs* pomocí následujícího kódu:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

`ID`Vlastnost se zobrazí jako sloupec primárního klíče tabulky databáze, která odpovídá této třídě. Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč. Proto je alternativní automaticky rozpoznaný název pro `Student` primární klíč třídy `StudentID` . Další informace najdete v tématu [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).

`Enrollments`Vlastnost je [navigační vlastnost](/ef/core/modeling/relationships). Navigační vlastnosti obsahují další entity, které se vztahují k této entitě. V takovém případě `Enrollments` vlastnost `Student` entity obsahuje všechny `Enrollment` entity, které se vztahují k danému studentovi. Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě entity registrace. 

V databázi se řádek registrace vztahuje k řádku studenta, pokud jeho sloupec StudentID obsahuje hodnotu ID studenta. Předpokládejme například, že řádek studenta má ID = 1. Související řádky registrace budou mít StudentID = 1. StudentID je *cizí klíč* v tabulce zápisu. 

`Enrollments`Vlastnost je definována tak, `ICollection<Enrollment>` že může existovat více souvisejících entit zápisu. Můžete použít jiné typy kolekce, například `List<Enrollment>` nebo `HashSet<Enrollment>` . Při `ICollection<Enrollment>` použití EF Core vytvoří `HashSet<Enrollment>` ve výchozím nastavení kolekci.

## <a name="the-enrollment-entity"></a>Entita registrace

![Diagram entity registrace](intro/_static/enrollment-entity.png)

Vytvořte *modely/registrace. cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

`EnrollmentID`Vlastnost je primární klíč. Tato entita používá `classnameID` vzor namísto `ID` samotného. Pro model produkčních dat vyberte jeden vzor a používejte ho konzistentně. Tento kurz používá pouze k ilustraci toho, jak funguje. Použití `ID` bez toho usnadňuje `classname` implementaci některých druhů změn datového modelu.

`Grade`Vlastnost je `enum` . Otazník po `Grade` deklaraci typu označuje, že vlastnost může `Grade` [mít hodnotu null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/). Třídu, která má hodnotu null, se liší od nulové třídy &mdash; null znamená, že známka není známa nebo ještě nebyla přiřazena.

`StudentID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student` . `Enrollment`Entita je přidružená k jedné `Student` entitě, takže vlastnost obsahuje jednu `Student` entitu.

`CourseID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course` . `Enrollment`Entita je přidružená k jedné `Course` entitě.

EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>` . Například `StudentID` je cizí klíč pro `Student` navigační vlastnost, protože `Student` primární klíč entity je `ID` . Lze také pojmenovat vlastnosti cizího klíče `<primary key property name>` . Například `CourseID` vzhledem k tomu, že `Course` primární klíč entity je `CourseID` .

## <a name="the-course-entity"></a>Entita kurzu

![Diagram entity kurzu](intro/_static/course-entity.png)

Vytvořte *modely/Course. cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

`Enrollments`Vlastnost je navigační vlastnost. `Course`Entita může souviset s libovolným počtem `Enrollment` entit.

`DatabaseGenerated`Atribut umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.

Sestavte projekt, aby se ověřilo, že nejsou k dispozici žádné chyby kompilátoru.

## <a name="scaffold-student-pages"></a>Stránky studenta pro generování uživatelského rozhraní

V této části použijete nástroj ASP.NET Core pro generování uživatelského rozhraní k vygenerování:

* Třída *kontextu* EF Core. Kontext je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model. Je odvozen z `Microsoft.EntityFrameworkCore.DbContext` třídy.
* Razorstránky, které zpracovávají operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro danou `Student` entitu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vytvořte složku *Students* ve složce *stránky* .
* V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* a vyberte **Přidat** > **novou vygenerované položky**.
* V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte ** Razor stránky pomocí Entity Framework (CRUD)** > **Přidat**.
* V dialogovém okně **Přidat Razor stránky pomocí Entity Framework (CRUD)** :
  * V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)**.
  * V řádku **třídy kontextu dat** vyberte **+** znaménko (plus).
  * Změňte název kontextu dat z *ContosoUniversity. Models. ContosoUniversityContext* na *ContosoUniversity. data. SchoolContext*.
  * Vyberte možnost **Přidat**.

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

  **V systému Windows**

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

* Vytvoří Razor stránky ve složce *Pages/Students* :
  * *Vytvoření. cshtml* a *Create.cshtml.cs*
  * *Odstranění. cshtml* a *DELETE.cshtml.cs*
  * *Podrobnosti. cshtml* a *Details.cshtml.cs*
  * *Upravit. cshtml* a *Edit.cshtml.cs*
  * *Index. cshtml* a *index.cshtml.cs*
* Vytvoří *data/SchoolContext. cs*.
* Přidá kontext do injektáže závislosti v *Startup.cs*.
* Přidá připojovací řetězec databáze, do kterého se má *appsettings.js*.

## <a name="database-connection-string"></a>Připojovací řetězec databáze

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí. Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* v `C:/Users/<user>` adresáři.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Změňte připojovací řetězec tak, aby odkazoval na soubor databáze SQLite s názvem *cu. DB*:

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Aktualizuje třídu kontextu databáze.

Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze. Kontext je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext určuje, které entity jsou zahrnuty v datovém modelu. V tomto projektu je třída pojmenována `SchoolContext` .

*SchoolContext.cs* aktualizujte pomocí následujícího kódu:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Zvýrazněný kód vytvoří vlastnost [negenerickými \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit. V terminologii EF Core:

* Sada entit obvykle odpovídá databázové tabulce.
* Entita odpovídá řádku v tabulce.

Vzhledem k tomu, že sada entit obsahuje více entit, musí mít vlastnosti Negenerickými plurální názvy. Vzhledem k tomu, že nástroj pro generování uživatelského rozhraní vytvořil `Student` negenerickými, tento krok změny změní na plural `Students` . 

Chcete-li Razor , aby kód stránky odpovídal novému negenerickými názvu, proveďte globální změnu v celém projektu `_context.Student` na `_context.Students` .  K dispozici je 8 výskytů.

Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilátoru.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection). Služby (například kontext databáze EF Core) jsou registrovány pomocí injektáže závislosti při spuštění aplikace. Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.

Nástroj pro generování uživatelského rozhraní automaticky zaregistroval třídu kontextu pomocí kontejneru vkládání závislostí.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V `ConfigureServices` byly zvýrazněné řádky přidány pomocí generátoru:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V nástroji se ujistěte, `ConfigureServices` že kód přidaný voláním uživatelského rozhraní `UseSqlite` .

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.

## <a name="create-the-database"></a>Vytvoření databáze

Aktualizujte *program.cs* , aby se vytvořila databáze, pokud neexistuje:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) neprovede žádnou akci, pokud existuje databáze pro kontext. Pokud žádná databáze neexistuje, vytvoří databázi a schéma. `EnsureCreated`povolí následující pracovní postup pro zpracování změn datového modelu:

* Odstraňte databázi. Všechna existující data budou ztracena.
* Změňte datový model. Například přidejte `EmailAddress` pole.
* Spusťte aplikaci.
* `EnsureCreated`vytvoří databázi s novým schématem.

Tento pracovní postup funguje dobře v rané fázi vývoje, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data. Tato situace se liší v případě, že data, která byla zadána do databáze, musí být zachována. V takovém případě použijte migrace.

Později v rámci série kurzů odstraníte databázi, kterou vytvořil, `EnsureCreated` a místo toho použijete migrace. Databázi vytvořenou nástrojem `EnsureCreated` nelze aktualizovat pomocí migrací.

### <a name="test-the-app"></a>Otestování aplikace

* Spusťte aplikaci.
* Vyberte odkaz **Students** a pak **vytvořte nový**.
* Otestujte odkazy Upravit, Podrobnosti a Odstranit.

## <a name="seed-the-database"></a>Dosazení databáze

`EnsureCreated`Metoda vytvoří prázdnou databázi. V této části se přidá kód, který naplní databázi testovacími daty.

Vytvořte *data/DbInitializer. cs* pomocí následujícího kódu:
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Kód kontroluje, zda v databázi existují studenti. Pokud neexistují studenti, přidá testovací data do databáze. Vytvoří testovací data v polích, nikoli `List<T>` kolekce pro optimalizaci výkonu.

* V *program.cs*nahraďte `EnsureCreated` volání `DbInitializer.Initialize` voláním:

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
* V SSOX vyberte **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}**. Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.
* Rozbalte uzel **tabulky** .
* Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.
* Kliknutím pravým tlačítkem na tabulku **student** a kliknutím na **Zobrazit kód** zjistíte, jak se `Student` model mapuje na `Student` schéma tabulky.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Použijte nástroj SQLite k zobrazení schématu databáze a dat osazených daty. Databázový soubor má název *cu. DB* a je umístěn ve složce projektu.

---

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.

Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna. Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna. Pomocí synchronního kódu může být mnoho vláken svázáno s tím, že ve skutečnosti neprovádí žádnou práci, protože čeká na dokončení vstupně-výstupních operací. V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků. Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovávat více provozu bez prodlev.

Asynchronní kód zavádí v době běhu malé množství režie. V situacích s nízkým provozem je dosaženo zanedbatelného výkonu, zatímco v situacích vysokého provozu je potenciální zlepšení výkonu značné.

V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota, `await` klíčové slovo a `ToListAsync` Metoda provede asynchronní spouštění kódu.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* `async`Klíčové slovo dává kompilátoru následující informace:
  * Vygenerujte zpětná volání pro části těla metody.
  * Vytvořte vrácený objekt [úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) .
* `Task<T>`Návratový typ představuje probíhající práci.
* `await`Klíčové slovo způsobí, že kompilátor rozdělí metodu do dvou částí. První část končí asynchronně spuštěnou operací. Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.
* `ToListAsync`je asynchronní verze `ToList` metody rozšíření.

Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá EF Core:

* Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze. To zahrnuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` a `SaveChangesAsync` . Neobsahuje příkazy, které mění pouze `IQueryable` , například `var students = context.Students.Where(s => s.LastName == "Davolio")` .
* Kontext EF Core není bezpečný pro přístup z více vláken: Nepokoušejte se současně provést více operací.
* Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.

Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Další kroky

> [!div class="step-by-step"]
> [Další kurz](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ukázková webová aplikace společnosti Contoso University ukazuje, jak vytvořit Razor aplikaci ASP.NET Core Pages s využitím jádra Entity Framework (EF).

Ukázková aplikace je web pro fiktivní univerzitě společnosti Contoso. Zahrnuje funkce, jako je například využití studenta, vytváření kurzu a přiřazení instruktora. Tato stránka je první v sérii kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University.

[Stažení nebo zobrazení dokončené aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Znalost [ Razor stránek](xref:razor-pages/index). Noví Programátori by měli před spuštěním této série dokončit [Začínáme se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) .

## <a name="troubleshooting"></a>Odstraňování potíží

Pokud narazíte na problém, který nelze vyřešit, můžete řešení obecně najít porovnáním kódu s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Dobrým způsobem, jak získat pomoc, je odeslání otázky do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>Webová aplikace společnosti Contoso University

Aplikace sestavená v těchto kurzech je základním webem na univerzitě.

Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech. Tady je několik obrazovek vytvořených v tomto kurzu.

![Stránka indexu studentů](intro/_static/students-index.png)

![Stránka pro úpravy studentů](intro/_static/student-edit.png)

Styl uživatelského rozhraní tohoto webu je blízko toho, co vygenerovaly předdefinované šablony. Tento kurz se zaměřuje na EF Core se Razor stránkami, nikoli v uživatelském rozhraní.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Vytvoření Razor webové aplikace ContosoUniversity Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* Vytvořte novou ASP.NET Core webovou aplikaci. Pojmenujte projekt **ContosoUniversity**. Je důležité pojmenovat projekt *ContosoUniversity* , aby se obory názvů shodovaly, když je kód zkopírován/vložen.
* V rozevíracím seznamu vyberte **ASP.NET Core 2,1** a potom vyberte možnost **Webová aplikace**.

Obrázky předchozích kroků najdete v tématu [vytvoření Razor webové aplikace](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Spusťte aplikaci.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Nastavení stylu webu

Několik změn nastaví nabídku webu, rozložení a domovskou stránku. Update *Pages/Shared/_Layout. cshtml* s následujícími změnami:

* Změňte všechny výskyty "ContosoUniversity" na "contoso University". Existují tři výskyty.

* Přidejte položky nabídky pro **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **kontakt** .

Změny jsou zvýrazněny. (Všechny *značky se nezobrazí* .)

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Vytvoření datového modelu

Vytvořte třídy entit pro aplikaci Contoso University. Začněte následujícími třemi entitami:

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

Mezi `Student` entitami a entitami je vztah 1: n `Enrollment` . Mezi `Course` entitami a entitami je vztah 1: n `Enrollment` . Student se může zaregistrovat v jakémkoli počtu kurzů. Kurz může mít zaregistrovaný libovolný počet studentů.

V následujících oddílech je vytvořena třída pro každou z těchto entit.

### <a name="the-student-entity"></a>Entita studenta

![Diagram entity studenta](intro/_static/student-entity.png)

Vytvořte složku *modelů* . Ve složce *modely* vytvořte soubor třídy s názvem *student.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

`ID`Vlastnost se zobrazí jako sloupec primárního klíče tabulky databáze (DB), která odpovídá této třídě. Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč. V `classnameID` `classname` je název třídy. Alternativní automaticky rozpoznaný primární klíč je `StudentID` v předchozím příkladu.

`Enrollments`Vlastnost je [navigační vlastnost](/ef/core/modeling/relationships). Vlastnosti navigace odkazují na další entity, které se vztahují k této entitě. V tomto případě `Enrollments` vlastnost `Student entity` obsahuje všechny `Enrollment` entity, které jsou v relaci `Student` . Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě `Enrollment` entity. Související `Enrollment` řádek je řádek, který obsahuje hodnotu primárního klíče tohoto studenta ve `StudentID` sloupci. Předpokládejme například, že student s ID = 1 má dva řádky v `Enrollment` tabulce. `Enrollment`Tabulka má dva řádky s `StudentID` = 1. `StudentID`je cizí klíč v `Enrollment` tabulce, který určuje studenta v `Student` tabulce.

Pokud navigační vlastnost může obsahovat více entit, musí být vlastnost navigace typu seznam, například `ICollection<T>` . `ICollection<T>`lze zadat nebo typ jako `List<T>` nebo `HashSet<T>` . Při `ICollection<T>` použití EF Core vytvoří `HashSet<T>` ve výchozím nastavení kolekci. Navigační vlastnosti, které obsahují více entit, přicházejí ze vztahů m:n a 1: n.

### <a name="the-enrollment-entity"></a>Entita registrace

![Diagram entity registrace](intro/_static/enrollment-entity.png)

Ve složce *modely* vytvořte *Enrollment.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID`Vlastnost je primární klíč. Tato entita používá `classnameID` vzor místo `ID` podobně jako `Student` entita. Vývojáři obvykle volí jeden model a používají ho v rámci datového modelu. V pozdějším kurzu se zobrazuje použití ID bez ClassName, které usnadňuje implementaci dědičnosti v datovém modelu.

`Grade`Vlastnost je `enum` . Otazník po `Grade` deklaraci typu označuje, že vlastnost může `Grade` mít hodnotu null. Hodnota, která je null, se liší od nulové třídy – hodnota null znamená, že se nejedná o známku nebo ještě není přiřazená.

`StudentID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student` . `Enrollment`Entita je přidružená k jedné `Student` entitě, takže vlastnost obsahuje jednu `Student` entitu. `Student`Entita se liší od `Student.Enrollments` navigační vlastnosti, která obsahuje více `Enrollment` entit.

`CourseID`Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course` . `Enrollment`Entita je přidružená k jedné `Course` entitě.

EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>` . Například `StudentID` pro `Student` vlastnost navigace, protože `Student` primární klíč entity je `ID` . Lze také pojmenovat vlastnosti cizího klíče `<primary key property name>` . Například `CourseID` vzhledem k tomu, že `Course` primární klíč entity je `CourseID` .

### <a name="the-course-entity"></a>Entita kurzu

![Diagram entity kurzu](intro/_static/course-entity.png)

Ve složce *modely* vytvořte *Course.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

`Enrollments`Vlastnost je navigační vlastnost. `Course`Entita může souviset s libovolným počtem `Enrollment` entit.

`DatabaseGenerated`Atribut umožňuje aplikaci zadat primární klíč, a ne vytvořit databázi.

## <a name="scaffold-the-student-model"></a>Generování uživatelského rozhraní modelu studenta

V této části je model studenta vygenerovaný jako generátor. To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model studenta.

* Sestavte projekt.
* Vytvořte složku *stránky/studenty* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* > **Přidat** > **novou vygenerované položky**.
* V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte ** Razor stránky pomocí Entity Framework (CRUD)** > **Přidat**.

Dokončete dialog **Přidat Razor stránky pomocí Entity Framework (CRUD)** :

* V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)**.
* V řádku **třídy kontextu dat** vyberte **+** znaménko (plus) a změňte vygenerovaný název na **ContosoUniversity. Models. SchoolContext**.
* V rozevíracím seznamu **Třída kontextu dat** vyberte **ContosoUniversity. Models. SchoolContext.**
* Vyberte možnost **Přidat**.

![Dialogové okno CRUD](intro/_static/s1.png)

Pokud máte problém s předchozím krokem, podívejte [se do části vygenerované modely filmů](xref:tutorials/razor-pages/model#scaffold-the-movie-model) .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte následující příkazy pro generování uživatelského rozhraní modelu studenta.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Proces generování uživatelského rozhraní byl vytvořen a změnil následující soubory:

### <a name="files-created"></a>Vytvořené soubory

* *Stránky/studenty* Vytvořit, odstranit, podrobnosti, upravit, index.
* *Data/SchoolContext. cs*

### <a name="file-updates"></a>Aktualizace souborů

* *Startup.cs* : změny v tomto souboru jsou podrobně popsané v další části.
* *appsettings.js* : připojovací řetězec použitý k připojení k místní databázi je přidán.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Kontrola kontextu zaregistrovaného vkládáním závislostí

ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace. Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.

Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.

Projděte si `ConfigureServices` metodu v *Startup.cs*. Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.

## <a name="update-main"></a>Aktualizace Main

V *program.cs*upravte `Main` metodu a proveďte následující:

* Získá instanci kontextu databáze z kontejneru vkládání závislostí.
* Zavolejte [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Vyřazení kontextu po `EnsureCreated` dokončení metody.

Následující kód ukazuje aktualizovaný soubor *program.cs* .

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated`zajišťuje, aby databáze pro kontext existovala. Pokud existuje, není provedena žádná akce. Pokud neexistuje, vytvoří se databáze a všechny její schéma. `EnsureCreated`nepoužívá k vytvoření databáze migrace. Databázi, která je vytvořená pomocí, se `EnsureCreated` nedá později aktualizovat pomocí migrace.

`EnsureCreated`se volá při spuštění aplikace, což umožňuje následující pracovní postup:

* Odstraňte databázi.
* Změňte schéma databáze (například přidat `EmailAddress` pole).
* Spusťte aplikaci.
* `EnsureCreated`vytvoří databázi se `EmailAddress` sloupcem.

`EnsureCreated`je pohodlná v brzké fázi vývoje, když se schéma rychle vyvíjí. Později v kurzu se databáze odstraní a použijí se migrace.

### <a name="test-the-app"></a>Otestování aplikace

Spusťte aplikaci a přijměte zásady souborů cookie. Tato aplikace neuchovává osobní údaje. Informace o zásadách souborů cookie najdete v tématu [Podpora pro EU obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).

* Vyberte odkaz **Students** a pak **vytvořte nový**.
* Otestujte odkazy Upravit, Podrobnosti a Odstranit.

## <a name="examine-the-schoolcontext-db-context"></a>Projděte si kontext SchoolContext DB.

Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze. Kontext dat je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje, které entity jsou zahrnuty v datovém modelu. V tomto projektu je třída pojmenována `SchoolContext` .

*SchoolContext.cs* aktualizujte pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Zvýrazněný kód vytvoří vlastnost [negenerickými \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit. V terminologii EF Core:

* Sada entit obvykle odpovídá tabulce databáze.
* Entita odpovídá řádku v tabulce.

`DbSet<Enrollment>`a `DbSet<Course>` může být vynecháno. EF Core je implicitně obsahuje, protože `Student` entita odkazuje na `Enrollment` entitu a `Enrollment` entita odkazuje na `Course` entitu. V tomto kurzu ponechejte `DbSet<Enrollment>` a `DbSet<Course>` v `SchoolContext` .

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace. Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* DB v `C:/Users/<user>` adresáři.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Přidejte kód pro inicializaci databáze s testovacími daty

EF Core vytvoří prázdnou databázi. V této části `Initialize` je zapsána metoda pro naplnění testovacích dat.

Ve složce *data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a přidejte následující kód:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Poznámka: předchozí kód používá `Models` pro obor názvů ( `namespace ContosoUniversity.Models` ) spíše než `Data` . `Models`je konzistentní s kódem generovaným pomocí generování uživatelského rozhraní. Další informace najdete v [tomto problému s vygenerováním uživatelského rozhraní GitHub](https://github.com/aspnet/Scaffolding/issues/822).

Kód zkontroluje, jestli v databázi existují studenti. Pokud databáze neobsahuje žádné studenty, databáze se inicializuje s testovacími daty. Načte testovací data do polí `List<T>` , nikoli kolekce pro optimalizaci výkonu.

`EnsureCreated`Metoda automaticky vytvoří databázi pro kontext databáze. Pokud databáze existuje, `EnsureCreated` vrátí se beze změny databáze.

V *program.cs*upravte `Main` metodu pro volání `Initialize` :

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
V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}**.

Rozbalte uzel **tabulky** .

Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.

Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna. Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna. Pomocí synchronního kódu může být mnoho vláken svázáno s tím, že ve skutečnosti neprovádí žádnou práci, protože čeká na dokončení vstupně-výstupních operací. V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků. Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server je povolen pro zpracování většího objemu dat bez prodlev.

Asynchronní kód zavádí v době běhu malé množství režie. V situacích s nízkým provozem je dosaženo zanedbatelného výkonu, zatímco v situacích vysokého provozu je potenciální zlepšení výkonu značné.

V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota, `await` klíčové slovo a `ToListAsync` Metoda provede asynchronní spouštění kódu.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* `async`Klíčové slovo dává kompilátoru následující informace:
  * Vygenerujte zpětná volání pro části těla metody.
  * Automaticky vytvořte vrácený objekt [úkolu](/dotnet/api/system.threading.tasks.task) . Další informace najdete v tématu [návratový typ úlohy](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Implicitní návratový typ `Task` představuje probíhající práci.
* `await`Klíčové slovo způsobí, že kompilátor rozdělí metodu do dvou částí. První část končí asynchronně spuštěnou operací. Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.
* `ToListAsync`je asynchronní verze `ToList` metody rozšíření.

Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá EF Core:

* Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze. To zahrnuje, `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` a `SaveChangesAsync` . Neobsahuje příkazy, které mění pouze `IQueryable` , například `var students = context.Students.Where(s => s.LastName == "Davolio")` .
* Kontext EF Core není bezpečný pro přístup z více vláken: Nepokoušejte se současně provést více operací.
* Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.

Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).

V dalším kurzu se přezkoumávají základní operace CRUD (vytváření, čtení, aktualizace, odstranění).



## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Další](xref:data/ef-rp/crud)

::: moniker-end
