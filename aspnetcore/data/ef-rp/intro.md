---
title: Razor stránky s jádrem entity framework u ASP.NET jádra - výuka 1 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit aplikaci Razor Pages pomocí entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 94783aa9014aef4c5f775fc8f36a2c3a7715e4b6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656819"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Razor stránky s jádrem entity framework u ASP.NET jádra - výuka 1 z 8

Tom [Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Toto je první z řady kurzů, které ukazují, jak používat Core entity Framework (EF) v [aplikaci ASP.NET Core Razor Pages.](xref:razor-pages/index) Výukové programy vytvořit webové stránky pro fiktivní Contoso University. Stránka obsahuje funkce, jako je přijetí studentů, tvorba kurzů a úkoly instruktora. Kurz používá první přístup kódu. Informace o následujícím kurzu pomocí prvního přístupu databáze naleznete v [tomto problému Github](https://github.com/dotnet/AspNetCore.Docs/issues/16897).

[Stáhněte si nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Stáhnout pokyny](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Požadavky

* Pokud jste s Razor Pages nováčkem, projděte si před spuštěním této série série [Začínáme s břitvou stránek.](xref:tutorials/razor-pages/razor-pages-start)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Databázové stroje

Pokyny sady Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), což je verze sql serveru Express, která je spuštěna pouze v systému Windows.

Visual Studio kód pokyny použít [SQLite](https://www.sqlite.org/), databázový stroj pro více platforem.

Pokud se rozhodnete použít SQLite, stáhněte a nainstalujte nástroj třetí strany pro správu a prohlížení databáze SQLite, například [DB Browser pro SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na problém, který nelze vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Dobrým způsobem, jak získat pomoc, je odeslání otázky do StackOverflow.com pomocí [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>Ukázková aplikace

Aplikace postavená v těchto kurzech je základní univerzitní web. Uživatelé mohou zobrazit a aktualizovat informace o studentovi, kurzu a instruktorovi. Zde je několik obrazovek vytvořených v tutoriálu.

![Stránka Studentský index](intro/_static/students-index30.png)

![Stránka Úpravy studentů](intro/_static/student-edit30.png)

Styl uživatelského uživatelského nastavení tohoto webu je založen na předdefinovaných šablonách projektu. Kurz se zaměřuje na to, jak používat EF Core, ne jak přizpůsobit ui.

Chcete-li získat zdrojový kód dokončeného projektu, postupujte podle odkazu v horní části stránky. Složka *cu30* má kód pro ASP.NET verzi výukového programu Core 3.0. Soubory, které odrážejí stav kódu pro výukové programy 1-7 lze nalézt ve složce *cu30snapshots.*

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Spuštění aplikace po stažení dokončeného projektu:

* Odstraňte tři soubory a jednu složku, které mají *SQLite* v názvu.
* Sestavte projekt.
* V konzole Správce balíčků (PMC) spusťte následující příkaz:

  ```powershell
  Update-Database
  ```

* Spusťte projekt osiva databáze.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spuštění aplikace po stažení dokončeného projektu:

* Odstraňte *ContosoUniversity.csproj*a přejmenujte *ContosoUniversitySQLite.csproj* na *ContosoUniversity.csproj*.
* Odstraňte *Startup.cs*a přejmenujte *StartupSQLite.cs* na *Startup.cs*.
* Odstraňte *soubor appSettings.json*a přejmenujte *aplikaci SettingsSQLite.json* na *appSettings.json*.
* Odstraňte složku *Migrace* a přejmenujte *aplikaci MigrationsSQL* na *Migrace*.
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

* Spusťte projekt osiva databáze.

---

## <a name="create-the-web-app-project"></a>Vytvoření projektu webové aplikace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* Vyberte **ASP.NET základní webovou aplikaci**.
* Název projektu *ContosoUniversity*. Je důležité použít tento přesný název včetně velkých písmen, takže obory názvů odpovídají kopírování a vložení kódu.
* V rozevíracích souborech vyberte položku **.NET Core** a **ASP.NET Core 3.0** a pak vyberte **možnost Webová aplikace**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V terminálu přejděte do složky, ve které má být vytvořena složka projektu.

* Spusťte následující příkazy a vytvořte projekt Razor Pages a `cd` do nové složky projektu:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Nastavení stylu webu

Nastavení záhlaví webu, zápatí a nabídky můžete nastavit aktualizací *stránek/Sdílené/_Layout.cshtml*:

* Změňte každý výskyt "ContosoUniversity" na "Contoso University". Existují tři výskyty.

* Odstraňte položky nabídky **Domů** a **Soukromí** a přidejte položky **pro informace**, **studenty**, **kurzy**, **instruktory**a **oddělení**.

Změny jsou zvýrazněny.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

V *pages/Index.cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET Core textem o této aplikaci:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Spusťte aplikaci a ověřte, zda se zobrazí domovská stránka.

## <a name="the-data-model"></a>Datový model

V následujících částech se vytvoří datový model:

![Diagram datového modelu zápisu do kurzu-studenta](intro/_static/data-model-diagram.png)

Student se může zapsat do libovolného počtu kurzů a v kurzu může být zapsán libovolný počet studentů.

## <a name="the-student-entity"></a>Studentská entita

![Diagram studentských entit](intro/_static/student-entity.png)

* Vytvořte složku *Modely* ve složce projektu. 

* Vytvořit *modely/Student.cs* s následujícím kódem:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

Vlastnost `ID` se stane sloupcem primárního klíče databázové tabulky, který odpovídá této třídě. Ve výchozím nastavení EF Core interpretuje `ID` `classnameID` vlastnost, která je pojmenována nebo jako primární klíč. Alternativní automaticky rozpoznaný `Student` název primárního klíče třídy je `StudentID`tedy .

Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships). Navigační vlastnosti mají další entity, které souvisejí s touto entitou. V tomto případě `Enrollments` vlastnost `Student` entity obsahuje všechny `Enrollment` entity, které souvisejí s tímto studentem. Například pokud Student řádek v databázi obsahuje dva `Enrollments` související Zápis řádky, navigační vlastnost obsahuje tyto dvě entity Zápisu. 

V databázi řádek Registrace souvisí s řádkem Student, pokud jeho sloupec ID studenta obsahuje hodnotu ID studenta. Předpokládejme například, že řádek Student má ID=1. Související řádky zápisu budou mít ID studenta = 1. StudentID je *cizí klíč* v tabulce Registrace. 

Vlastnost `Enrollments` je definována jako, `ICollection<Enrollment>` protože může existovat více souvisejících entit zápisu. Můžete použít jiné typy kolekcí, například `List<Enrollment>` nebo `HashSet<Enrollment>`. Při `ICollection<Enrollment>` použití EF Core `HashSet<Enrollment>` vytvoří kolekci ve výchozím nastavení.

## <a name="the-enrollment-entity"></a>Entita Zápis

![Diagram entit zápisu](intro/_static/enrollment-entity.png)

Vytvořte *model/enrollment.cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

Vlastnost `EnrollmentID` je primární klíč; tato entita `classnameID` používá `ID` vzor místo sebe. Pro model produkčních dat zvolte jeden vzor a používejte jej konzistentně. Tento kurz používá jak jen pro ilustraci, že obě práce. Použití `ID` `classname` bez usnadňuje implementaci některé druhy změn datového modelu.

Ubytovací `Grade` zařízení `enum`je . Otazník za `Grade` deklarací typu `Grade` označuje, že vlastnost je [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/). Stupeň, který je null se&mdash;liší od nulové třídy null znamená, že platová třída není známa nebo ještě nebyla přiřazena.

Vlastnost `StudentID` je cizí klíč a odpovídající navigační `Student`vlastnost je . Entita `Enrollment` je `Student` přidružena k jedné entitě, takže vlastnost obsahuje jednu `Student` entitu.

Vlastnost `CourseID` je cizí klíč a odpovídající navigační `Course`vlastnost je . Entita `Enrollment` je `Course` přidružena k jedné entitě.

EF Core interpretuje vlastnost jako cizí klíč, pokud je pojmenována `<navigation property name><primary key property name>`. Například`StudentID` je cizí klíč `Student` pro navigační vlastnost, protože je primární klíč `Student` entity `ID`. Vlastnosti cizího klíče `<primary key property name>`lze také pojmenovat . Například `CourseID` vzhledem `Course` k tomu, `CourseID`že primární klíč entity je .

## <a name="the-course-entity"></a>Entita Kurzu

![Diagram entity kurzu](intro/_static/course-entity.png)

Vytvořit *modely / Course.cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

Vlastnost `Enrollments` je navigační vlastnost. Entita `Course` může souviset `Enrollment` s libovolným počtem entit.

Atribut `DatabaseGenerated` umožňuje aplikaci určit primární klíč, nikoli databázi, která jej generuje.

Sestavení projektu k ověření, že neexistují žádné chyby kompilátoru.

## <a name="scaffold-student-pages"></a>Stránky studentů lešení

V této části použijete nástroj ASP.NET generování generování pomocí nástroje generování pomocí ASP.NET základního lešení:

* Ef Core *kontextové* třídy. Kontext je hlavní třída, která koordinuje entity framework funkce pro daný datový model. Je odvozen z `Microsoft.EntityFrameworkCore.DbContext` třídy.
* Holicí strojek stránky, které zpracovávají vytvořit, číst, aktualizovat a odstranit (CRUD) operace pro entitu. `Student`

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vytvořte složku *Studenti* ve složce *Stránky.*
* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku *Stránky/Studenti* a vyberte **přidat** > **novou scaffolded item**.
* V dialogovém okně **Přidat písmo** vyberte **Razor Pages using Entity Framework (CRUD)** > **ADD**.
* V dialogovém **okně Přidat žiletky pomocí entity frameworku (CRUD)** :
  * V rozevíracím seznamu **Model třídy** vyberte **Student (ContosoUniversity.Models)**.
  * V řádku **třídy Kontext** **+** dat vyberte znaménko (plus).
  * Změňte název kontextu dat z *ContosoUniversity.Models.ContosoUniversityContext* na *ContosoUniversity.Data.SchoolContext*.
  * Vyberte **Přidat**.

Automaticky se instalují následující balíčky:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Spusťte následující příkazy rozhraní PŘÍKAZOVÉHO PŘÍKAZU .NET Core k instalaci požadovaných balíčků NuGet:
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

  Balíček Microsoft.VisualStudio.Web.CodeGeneration.Design je vyžadován pro generování uživatelského rozhraní. Přestože aplikace nebude používat SQL Server, nástroj generování uživatelského terminálu potřebuje balíček SQL Server.

* Vytvořte složku *Stránky/Studenti.*

* Spuštěním následujícího příkazu nainstalujte [nástroj lešení generátoru aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Spusťte následující příkaz pro studentské stránky uživatelského lístí.

  **V systému Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **Na macOS nebo Linuxu**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Pokud máte problém s předchozím krokem, vytvořte projekt a opakujte krok sešitu.

Proces lešení:

* Vytvoří stránky Razor ve složce *Stránky/Studenti:*
  * *Create.cshtml* a *Create.cshtml.cs*
  * *Odstranit.cshtml* a *Delete.cshtml.cs*
  * *Details.cshtml* a *Details.cshtml.cs*
  * *Edit.cshtml* a *Edit.cshtml.cs*
  * *Index.cshtml* a *Index.cshtml.cs*
* Vytvoří *data/SchoolContext.cs*.
* Přidá kontext vkládání závislostí v *Startup.cs*.
* Přidá připojovací řetězec databáze do *souboru appsettings.json*.

## <a name="database-connection-string"></a>Připojovací řetězec databáze

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Připojovací řetězec určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB je odlehčená verze sql server express databázový stroj a je určen pro vývoj aplikací, nikoli produkční použití. Ve výchozím nastavení localdb vytvoří soubory `C:/Users/<user>` *MDF* v adresáři.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Změňte připojovací řetězec tak, aby přectol na databázový soubor SQLite s názvem *CU.db*:

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Aktualizace třídy kontextu databáze

Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze. Kontext je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext určuje, které entity jsou zahrnuty v datovém modelu. V tomto projektu je `SchoolContext`třída pojmenována .

Aktualizujte *SchoolContext.cs* pomocí následujícího kódu:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Zvýrazněný kód vytvoří [vlastnost\<DbSet TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit. V ef core terminologii:

* Sada entit obvykle odpovídá databázové tabulce.
* Entita odpovídá řádku v tabulce.

Vzhledem k tomu, že sada entit obsahuje více entit, měly by být vlastnosti DBSet plurálním jménem. Vzhledem k tomu, že`Student` nástroj lešení vytvořil `Students`sadu DBSet, tento krok jej změní na množné číslo . 

Chcete-li, aby kód Razor Pages odpovídal novému názvu DBSet, proveďte globální změnu v celém projektu `_context.Student` na . `_context.Students`  Existuje 8 výskytů.

Sestavení projektu k ověření, že neexistují žádné chyby kompilátoru.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core je postaven s [vstřikování závislostí](xref:fundamentals/dependency-injection). Služby (například kontext databáze EF Core) jsou registrovány s vkládání závislostí při spuštění aplikace. Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu databáze je zobrazen dále v kurzu.

Nástroj pro vytváření uživatelského funkce automaticky zaregistroval třídu kontextu s kontejnerem vkládání závislostí.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V `ConfigureServices`aplikace byly zvýrazněné řádky přidány lešením:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V `ConfigureServices`oblasti se ujistěte, že kód `UseSqlite`přidaný voláním lešení .

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu. Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*

## <a name="create-the-database"></a>Vytvoření databáze

Aktualizace *Program.cs* a vytvořte databázi, pokud neexistuje:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) Metoda neprovede žádnou akci, pokud existuje databáze pro kontext. Pokud neexistuje žádná databáze, vytvoří databázi a schéma. `EnsureCreated`umožňuje následující pracovní postup pro zpracování změn datového modelu:

* Odstraňte databázi. Všechna existující data budou ztracena.
* Změňte datový model. Přidejte například `EmailAddress` pole.
* Spusťte aplikaci.
* `EnsureCreated`vytvoří databázi s novým schématem.

Tento pracovní postup funguje dobře brzy ve vývoji, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data. Situace se liší, když je třeba zachovat data, která byla zadána do databáze. V takovém případě použijte migrace.

Později v sérii kurzů odstraníte databázi, která byla `EnsureCreated` vytvořena, a místo toho použijete migrace. Databázi, která `EnsureCreated` je vytvořena, nelze aktualizovat pomocí migrace.

### <a name="test-the-app"></a>Otestování aplikace

* Spusťte aplikaci.
* Vyberte odkaz **Studenti** a pak **Vytvořit nový**.
* Otestujte odkazy Upravit, Podrobnosti a Odstranit.

## <a name="seed-the-database"></a>Osivo databáze

Metoda `EnsureCreated` vytvoří prázdnou databázi. Tato část přidá kód, který naplní databázi testovacími daty.

Vytvořte *data/DbInitializer.cs* s následujícím kódem:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Kód zkontroluje, zda jsou v databázi nějaké studenty. Pokud neexistují žádné studenty, přidá testovací data do databáze. Vytvoří testovací data v polích, nikoli `List<T>` v kolekcích pro optimalizaci výkonu.

* V *Program.cs*nahraďte `EnsureCreated` hovor voláním: `DbInitializer.Initialize`

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole Správce balíčků** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *CU.db.*

---

* Restartujte aplikaci.

* Výběrem stránky Studenti zobrazíte osývaná data.

## <a name="view-the-database"></a>Zobrazit databázi

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Otevřete **Průzkumníka objektů SERVERU SQL Server** (SSOX) z nabídky **Zobrazení** v sadě Visual Studio.
* Ve SSOX vyberte **(localdb)\MSSQLLocalDB > databáze > SchoolContext-{GUID}**. Název databáze je generován z názvu kontextu, který jste zadali dříve plus pomlčka a GUID.
* Rozbalte uzel **Tabulky.**
* Klikněte pravým tlačítkem myši na tabulku **Student** a kliknutím na **Zobrazit data** zobrazte vytvořené sloupce a řádky vložené do tabulky.
* Klikněte pravým tlačítkem myši na tabulku **Student** a `Student` kliknutím na Zobrazit **kód** zobrazíte, `Student` jak se model mapuje na schéma tabulky.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pomocí nástroje SQLite můžete zobrazit schéma databáze a osiva dat. Databázový soubor má název *CU.db* a je umístěn ve složce projektu.

---

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.

Webový server má omezený počet vláken k dispozici a v situacích s vysokým zatížením všechny dostupné podprocesy mohou být používány. V takovém případě server nemůže zpracovat nové požadavky, dokud vlákna jsou uvolněny. Pomocí synchronního kódu může být mnoho vláken svázáno, zatímco ve skutečnosti nepracují, protože čekají na dokončení vstupně-up. S asynchronním kódem, když proces čeká na dokončení vstupně-va, jeho vlákno je uvolněno pro server pro zpracování jiných požadavků. V důsledku toho asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovat větší provoz bez zpoždění.

Asynchronní kód zavést malé množství režie v době běhu. V situacích s nízkým provozem je zásah ový výkon zanedbatelný, zatímco v situacích s vysokým provozem je potenciální zlepšení výkonu značné.

V následujícím kódu [asynchronní](/dotnet/csharp/language-reference/keywords/async) `Task<T>` klíčové slovo, vrácená hodnota, `await` klíčové slovo a `ToListAsync` metoda provést spuštění kódu asynchronně.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* Klíčové `async` slovo říká kompilátoru:
  * Generovat zpětná volání pro části těla metody.
  * Vytvořte [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) objekt, který je vrácen.
* Návratový `Task<T>` typ představuje probíhající práci.
* Klíčové `await` slovo způsobí, že kompilátor rozdělit metodu do dvou částí. První část končí operací, která byla spuštěna asynchronně. Druhá část je umístěn a callback metoda, která je volána po dokončení operace.
* `ToListAsync`je asynchronní verze metody `ToList` rozšíření.

Některé věci, které je třeba si uvědomit při psaní asynchronního kódu, který používá EF Core:

* Pouze příkazy, které způsobují dotazy nebo příkazy, které mají být odeslány do databáze jsou prováděny asynchronně. To `ToListAsync`zahrnuje `SingleOrDefaultAsync` `FirstOrDefaultAsync`, `SaveChangesAsync`, , a . Neobsahuje příkazy, které pouze `IQueryable`změnit `var students = context.Students.Where(s => s.LastName == "Davolio")`, například .
* Kontext EF Core není bezpečný pro přístup z více vláken: nepokoušejte se provést více operací paralelně.
* Chcete-li využít výhod výkonu asynchronního kódu, ověřte, zda balíčky knihovny (například pro stránkování) používají asynchronní, pokud volají metody EF Core, které odesílají dotazy do databáze.

Další informace o asynchronním programování v rozhraní .NET naleznete v [tématu Async Overview](/dotnet/standard/async) a [Asynchronní programování s async a await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Další kroky

> [!div class="step-by-step"]
> [Další kurz](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ukázková webová aplikace Contoso University ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí core frameworku entit.Contoso University ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí core entity framework (EF).

Ukázková aplikace je webová stránka fiktivní univerzity Contoso. Zahrnuje funkce, jako je přijetí studentů, tvorba kurzů a úkoly instruktora. Tato stránka je první z řady kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University.

[Stáhněte si nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Stáhnout pokyny](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Znalost razor [stránek](xref:razor-pages/index). Noví programátoři by měli před zahájením této série dokončit [začínáme se stránkami Razor Pages.](xref:tutorials/razor-pages/razor-pages-start)

## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na problém, který nelze vyřešit, můžete obecně najít řešení porovnáním kódu s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Dobrým způsobem, jak získat pomoc, je zveřejnění matné otázky [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>Webová aplikace Univerzity Contoso

Aplikace postavená v těchto kurzech je základní univerzitní web.

Uživatelé mohou zobrazit a aktualizovat informace o studentovi, kurzu a instruktorovi. Zde je několik obrazovek vytvořených v tutoriálu.

![Stránka Studentský index](intro/_static/students-index.png)

![Stránka Úpravy studentů](intro/_static/student-edit.png)

Styl uživatelského uživatelského nastavení tohoto webu se blíží tomu, co je generováno vestavěné šablony. Zaměření kurzu je na EF Core s Razor Pages, ne na ui.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Vytvořte webovou aplikaci ContosoUniversity Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* Vytvořte novou ASP.NET základní webovou aplikaci. Název projektu **ContosoUniversity**. Je důležité pojmenovat projekt *ContosoUniversity,* aby se obory názvů shodovaly s kopírováním/vložením kódu.
* V rozevíracím souboru vyberte **ASP.NET Core 2.1** a pak vyberte **možnost Webová aplikace**.

Obrázky z předchozích kroků najdete v [tématu Vytvoření webové aplikace Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Spusťte aplikaci.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Nastavení stylu webu

Několik změn nastavilo nabídku webu, rozložení a domovskou stránku. Aktualizovat *stránky/Sdílené/_Layout.cshtml* s následujícími změnami:

* Změňte každý výskyt "ContosoUniversity" na "Contoso University". Existují tři výskyty.

* Přidejte položky nabídky pro **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **Kontakt.**

Změny jsou zvýrazněny. (Všechny značky *se* nezobrazí.)

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

V *pages/Index.cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Vytvoření datového modelu

Vytvořte třídy entit pro aplikaci Contoso University. Začněte s následujícími třemi entitami:

![Diagram datového modelu zápisu do kurzu-studenta](intro/_static/data-model-diagram.png)

Existuje vztah 1:N mezi `Student` entitami. `Enrollment` Existuje vztah 1:N mezi `Course` entitami. `Enrollment` Student se může zapsat do libovolného počtu kurzů. Kurz může mít libovolný počet studentů zapsaných v něm.

V následujících částech je vytvořena třída pro každou z těchto entit.

### <a name="the-student-entity"></a>Studentská entita

![Diagram studentských entit](intro/_static/student-entity.png)

Vytvořte složku *Modely.* Ve složce *Modely* vytvořte soubor třídy s názvem *Student.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

Vlastnost `ID` se stane sloupcem primárního klíče tabulky databáze (DB), která odpovídá této třídě. Ve výchozím nastavení EF Core interpretuje `ID` `classnameID` vlastnost, která je pojmenována nebo jako primární klíč. V `classnameID` `classname` , je název třídy. Alternativní automaticky rozpoznaný primární klíč je `StudentID` v předchozím příkladu.

Vlastnost `Enrollments` je [navigační vlastnost](/ef/core/modeling/relationships). Navigační vlastnosti odkazují na jiné entity, které souvisejí s touto entitou. V tomto případě `Enrollments` vlastnost `Student entity` a obsahuje `Enrollment` všechny entity, které `Student`s tím souvisejí . Například pokud Student řádek v DB má dva `Enrollments` související Zápis řádky, navigační vlastnost obsahuje tyto dvě `Enrollment` entity. Související `Enrollment` řádek je řádek, který obsahuje hodnotu primárního klíče studenta ve sloupci. `StudentID` Předpokládejme například, že student s ID= `Enrollment` 1 má v tabulce dva řádky. Tabulka `Enrollment` má dva `StudentID` řádky s = 1. `StudentID`je cizí klíč `Enrollment` v tabulce, který určuje `Student` studenta v tabulce.

Pokud vlastnost navigace může obsahovat více entit, vlastnost navigace `ICollection<T>`musí být typ seznamu, například . `ICollection<T>`nebo typ, jako je `List<T>` nebo `HashSet<T>`. Při `ICollection<T>` použití EF Core `HashSet<T>` vytvoří kolekci ve výchozím nastavení. Navigační vlastnosti, které mají více entit, pocházejí ze vztahů N:N a 1:N.

### <a name="the-enrollment-entity"></a>Entita Zápis

![Diagram entit zápisu](intro/_static/enrollment-entity.png)

Ve složce *Modely* vytvořte *Enrollment.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

Vlastnost `EnrollmentID` je primární klíč. Tato entita `classnameID` používá `ID` vzor `Student` místo jako entita. Vývojáři obvykle zvolit jeden vzor a použít jej v celém datovém modelu. V pozdějším kurzu pomocí ID bez názvu třídy je zobrazeno usnadnit implementaci dědičnosti v datovém modelu.

Ubytovací `Grade` zařízení `enum`je . Otazník za `Grade` deklarací typu `Grade` označuje, že vlastnost je nullable. Stupeň, který je null se liší od nulové třídy - null znamená, že platová třída není známa nebo ještě nebyla přiřazena.

Vlastnost `StudentID` je cizí klíč a odpovídající navigační `Student`vlastnost je . Entita `Enrollment` je `Student` přidružena k jedné entitě, takže vlastnost obsahuje jednu `Student` entitu. Entita `Student` se `Student.Enrollments` liší od navigační `Enrollment` vlastnosti, která obsahuje více entit.

Vlastnost `CourseID` je cizí klíč a odpovídající navigační `Course`vlastnost je . Entita `Enrollment` je `Course` přidružena k jedné entitě.

EF Core interpretuje vlastnost jako cizí klíč, pokud je pojmenována `<navigation property name><primary key property name>`. Například`StudentID` pro `Student` navigační vlastnost, `Student` protože je primární `ID`klíč entity . Vlastnosti cizího klíče `<primary key property name>`lze také pojmenovat . Například `CourseID` vzhledem `Course` k tomu, `CourseID`že primární klíč entity je .

### <a name="the-course-entity"></a>Entita Kurzu

![Diagram entity kurzu](intro/_static/course-entity.png)

Ve složce *Modely* vytvořte *Course.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

Vlastnost `Enrollments` je navigační vlastnost. Entita `Course` může souviset `Enrollment` s libovolným počtem entit.

Atribut `DatabaseGenerated` umožňuje aplikaci určit primární klíč, nikoli ho vygenerovat DB.

## <a name="scaffold-the-student-model"></a>Lešení studentský model

V této části je model studenta lešený. To znamená, že nástroj lešení vytváří stránky pro operace Vytvořit, Číst, Aktualizovat a Odstranit (CRUD) pro model studenta.

* Sestavte projekt.
* Vytvořte složku *Stránky/Studenti.*

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumníku řešení**klikněte pravým tlačítkem myši na složku *Stránky/Studenti* > **Přidat** > **novou scaffolded položku**.
* V dialogovém okně **Přidat písmo** vyberte **Razor Pages using Entity Framework (CRUD)** > **ADD**.

Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**

* V rozevíracím seznamu **Model třídy** vyberte **Student (ContosoUniversity.Models)**.
* V řádku **třídy Kontext** **+** dat vyberte znaménko (plus) a změňte generovaný název na **ContosoUniversity.Models.SchoolContext**.
* V rozevíracím souboru **třídy Kontext dat** vyberte **ContosoUniversity.Models.SchoolContext.**
* Vyberte **Přidat**.

![DIALOGOVÉ OKNO CRUD](intro/_static/s1.png)

Pokud máte problém s předchozím krokem, přečtěte si zobrazit scénář [k saffold filmového modelu.](xref:tutorials/razor-pages/model#scaffold-the-movie-model)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte následující příkazy pro zasouvací vzor studenta.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Proces vytváření uživatelského příkazu vytvořil a změnil následující soubory:

### <a name="files-created"></a>Vytvořené soubory

* *Stránky/Studenti* Vytvořit, odstranit, podrobnosti, upravit, indexovat.
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>Aktualizace souborů

* *Startup.cs* : Změny tohoto souboru jsou podrobně popsány v další části.
* *appsettings.json* : Připojovací řetězec používaný pro připojení k místní databázi je přidán.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Prozkoumejte kontext registrovaný pomocí vkládání závislostí

ASP.NET Core je postaven s [vstřikování závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) jsou registrovány s vkládání závislostí při spuštění aplikace. Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu db, je zobrazen dále v kurzu.

Nástroj pro vytváření uživatelského funkce automaticky vytvořil kontext DB a zaregistroval jej do kontejneru vkládání závislostí.

Zkontrolujte `ConfigureServices` metodu v *Startup.cs*. Zvýrazněný řádek byl přidán lešení:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu. Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*

## <a name="update-main"></a>Aktualizovat hlavní

V *Program.cs*upravte metodu `Main` takto:

* Získejte kontextové instance DB z kontejneru vkládání závislostí.
* Volání [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Po dokončení `EnsureCreated` metody zlikvidujte kontext.

Následující kód zobrazuje aktualizovaný *soubor Program.cs.*

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated`zajišťuje, že databáze pro kontext existuje. Pokud existuje, není provedena žádná akce. Pokud neexistuje, jsou vytvořeny databáze a všechny jeho schéma. `EnsureCreated`nepoužívá migrace k vytvoření databáze. Databázi, která `EnsureCreated` je vytvořena pomocí aplikace, nelze později aktualizovat pomocí migrace.

`EnsureCreated`se nazývá spuštění aplikace, což umožňuje následující pracovní tok:

* Odstraňte db.
* Změňte schéma DB (například přidejte `EmailAddress` pole).
* Spusťte aplikaci.
* `EnsureCreated`vytvoří DB se`EmailAddress` sloupcem.

`EnsureCreated`je vhodný na počátku vývoje, kdy se schéma rychle vyvíjí. Později v kurzu db se odstraní a migrace se používají.

### <a name="test-the-app"></a>Otestování aplikace

Spusťte aplikaci a přijměte zásady používání souborů cookie. Tato aplikace neuchovává osobní údaje. O zásadách používání souborů cookie si můžete přečíst na [adrese OBECNÁ PODPORA NAŘÍZENÍ EU o ochraně osobních údajů (GDPR).](xref:security/gdpr)

* Vyberte odkaz **Studenti** a pak **Vytvořit nový**.
* Otestujte odkazy Upravit, Podrobnosti a Odstranit.

## <a name="examine-the-schoolcontext-db-context"></a>Prozkoumání kontextu SchoolContext DB

Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je kontextová třída DB. Kontext dat je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje, které entity jsou zahrnuty do datového modelu. V tomto projektu je `SchoolContext`třída pojmenována .

Aktualizujte *SchoolContext.cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Zvýrazněný kód vytvoří [vlastnost\<DbSet TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit. V ef core terminologii:

* Sada entit obvykle odpovídá tabulce DB.
* Entita odpovídá řádku v tabulce.

`DbSet<Enrollment>`a `DbSet<Course>` může být vynechán. EF Core je implicitně `Student` zahrnuje, `Enrollment` protože entita odkazuje na entitu a entita `Enrollment` odkazuje na entitu. `Course` Pro účely tohoto `DbSet<Enrollment>` `DbSet<Course>` kurzu `SchoolContext`uchovávejte a v .

### <a name="sql-server-express-localdb"></a>Místní databáze SQL Server Express

Připojovací řetězec určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB je odlehčená verze sql server express databázový stroj a je určen pro vývoj aplikací, nikoli produkční použití. LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje žádná složitá konfigurace. Ve výchozím nastavení localdb vytvoří *.mdf* DB soubory v adresáři. `C:/Users/<user>`

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Přidání kódu pro inicializaci DATABÁZE s testovacími daty

EF Core vytvoří prázdnou DB. V této části `Initialize` je metoda zapsána k naplnění s testovací data.

Ve složce *Data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a přidejte následující kód:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Poznámka: Předchozí kód `Models` používá pro obor`namespace ContosoUniversity.Models`názvů `Data`( ) spíše než . `Models`je konzistentní s kódem generovaným generováním šaše. Další informace naleznete v [tomto problému s lešením GitHub](https://github.com/aspnet/Scaffolding/issues/822).

Kód zkontroluje, zda jsou v DB nějaké studenty. Pokud nejsou žádné studenty v DB, DB je inicializována s testovací data. Načte testovací data do `List<T>` polí, nikoli do kolekcí pro optimalizaci výkonu.

Metoda `EnsureCreated` automaticky vytvoří DB pro kontext DB. Pokud DB existuje, `EnsureCreated` vrátí bez úpravdb.

V *Program.cs*upravte metodu `Main` volání `Initialize`:

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole Správce balíčků** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *CU.db.*

---

## <a name="view-the-db"></a>Zobrazit DATABÁZI

Název databáze je generován z názvu kontextu, který jste zadali dříve plus pomlčka a GUID. Název databáze tedy bude "SchoolContext-{GUID}". Identifikátor GUID se bude lišit pro každého uživatele.
Otevřete **Průzkumníka objektů SERVERU SQL Server** (SSOX) z nabídky **Zobrazení** v sadě Visual Studio.
Ve ssoxu klikněte na **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.

Rozbalte uzel **Tabulky.**

Klikněte pravým tlačítkem myši na tabulku **Student** a kliknutím na **Zobrazit data** zobrazte vytvořené sloupce a řádky vložené do tabulky.

## <a name="asynchronous-code"></a>Asynchronní kód

Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.

Webový server má omezený počet vláken k dispozici a v situacích s vysokým zatížením všechny dostupné podprocesy mohou být používány. V takovém případě server nemůže zpracovat nové požadavky, dokud vlákna jsou uvolněny. Pomocí synchronního kódu může být mnoho vláken svázáno, zatímco ve skutečnosti nepracují, protože čekají na dokončení vstupně-up. S asynchronním kódem, když proces čeká na dokončení vstupně-va, jeho vlákno je uvolněno pro server pro zpracování jiných požadavků. V důsledku toho asynchronní kód umožňuje efektivnější použití prostředků serveru a server je povolen pro zpracování většího provozu bez zpoždění.

Asynchronní kód zavést malé množství režie v době běhu. V situacích s nízkým provozem je zásah ový výkon zanedbatelný, zatímco v situacích s vysokým provozem je potenciální zlepšení výkonu značné.

V následujícím kódu [asynchronní](/dotnet/csharp/language-reference/keywords/async) `Task<T>` klíčové slovo, vrácená hodnota, `await` klíčové slovo a `ToListAsync` metoda provést spuštění kódu asynchronně.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Klíčové `async` slovo říká kompilátoru:
  * Generovat zpětná volání pro části těla metody.
  * Automaticky vytvořte vrácený objekt [Task.](/dotnet/api/system.threading.tasks.task) Další informace naleznete v [tématu Typ vrácení úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Implicitní návratový typ `Task` představuje probíhající práci.
* Klíčové `await` slovo způsobí, že kompilátor rozdělit metodu do dvou částí. První část končí operací, která byla spuštěna asynchronně. Druhá část je umístěn a callback metoda, která je volána po dokončení operace.
* `ToListAsync`je asynchronní verze metody `ToList` rozšíření.

Některé věci, které je třeba si uvědomit při psaní asynchronního kódu, který používá EF Core:

* Pouze příkazy, které způsobují dotazy nebo příkazy, které mají být odeslány do DB jsou prováděny asynchronně. To zahrnuje `ToListAsync` `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, `SaveChangesAsync`, a . Neobsahuje příkazy, které pouze `IQueryable`změnit `var students = context.Students.Where(s => s.LastName == "Davolio")`, například .
* Kontext EF Core není bezpečný pro přístup z více vláken: nepokoušejte se provést více operací paralelně.
* Chcete-li využít výhod výkonu asynchronního kódu, ověřte, zda balíčky knihovny (například pro stránkování) používají asynchronní, pokud volají metody EF Core, které odesílají dotazy do DB.

Další informace o asynchronním programování v rozhraní .NET naleznete v [tématu Async Overview](/dotnet/standard/async) a [Asynchronní programování s async a await](/dotnet/csharp/programming-guide/concepts/async/).

V dalším kurzu jsou zkoumány základní operace CRUD (vytváření, čtení, aktualizace, odstranění).



## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Další](xref:data/ef-rp/crud)

::: moniker-end
