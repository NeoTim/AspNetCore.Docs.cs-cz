---
title: Přidání modelu do aplikace Core MVC ASP.NET
author: rick-anderson
description: Přidejte model do jednoduché aplikace ASP.NET Core.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: e7fc0496438734e13cfafcecf432da4a94737897
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434509"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a>Přidání modelu do aplikace Core MVC ASP.NET

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a Tom [Dykstra](https://github.com/tdykstra)

V této části přidáte třídy pro správu filmů v databázi. Tyto třídy budou součástí aplikace **M**VC **"M**odel".

Tyto třídy s [jádrem entity frameworku](/ef/core) (EF Core) se používají pro práci s databází. EF Core je objekt-relační mapování (ORM) framework, který zjednodušuje přístupový kód dat, který musíte napsat.

Třídy modelu, které vytvoříte, jsou označovány jako třídy POCO (z **P**lain **O**ld **C**LR **O**bjects), protože nemají žádnou závislost na EF Core. Pouze definovat vlastnosti dat, která budou uložena v databázi.

V tomto kurzu nejprve napíšete třídy modelu a EF Core vytvoří databázi.

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Přidání třídy datového modelu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klepněte pravým tlačítkem myši na složku *Modely* > **Přidat** > **třídu**. Pojmenujte soubor *Movie.cs*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Přidejte do složky *Modely* soubor s názvem *Movie.cs.*

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Klepněte pravým tlačítkem myši na složku *Modely* > **Přidat** > **novou třídu** > **prázdnou třídu**. Pojmenujte soubor *Movie.cs*.

---

Aktualizujte *soubor Movie.cs* následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

Třída `Movie` obsahuje `Id` pole, které je vyžadováno databází pro primární klíč.

Atribut <xref:System.ComponentModel.DataAnnotations.DataType> na `ReleaseDate` určuje typ dat (`Date`). S tímto atributem:

* Uživatel nemusí zadávat informace o čase do pole data.
* Zobrazí se pouze datum, nikoli informace o čase.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) jsou zahrnuty v pozdější kurz.

## <a name="add-nuget-packages"></a>Přidání balíčků NuGet

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V nabídce **Nástroje** vyberte konzolu Správce balíčků **Aplikace NuGet Správce** > **balíčků** (PMC).

![Nabídka PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

V pmc spusťte následující příkaz:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

Předchozí příkaz přidá zprostředkovatele EF Core SQL Server. Balíček zprostředkovatele nainstaluje balíček EF Core jako závislost. Další balíčky jsou nainstalovány automaticky v kroku lešení později v kurzu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

V nabídce **Projekt** vyberte **Spravovat balíčky NuGet**.

Do pole **Hledat** v pravém `Microsoft.EntityFrameworkCore.SQLite` horním horním sejde a prohledání stiskněte klávesu **Return.** Vyberte odpovídající balíček NuGet a stiskněte tlačítko **Přidat balíček.**

![Přidat balíček Nuget core entity frameworku](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

Zobrazí se dialogové okno **Vybrat projekty** s vybraným projektem. `MvcMovie` Stiskněte tlačítko **Ok.**

Zobrazí se dialogové okno **Přijetí licence.** Podle potřeby zkontrolujte licence a klikněte na tlačítko **Přijmout.**

Chcete-li nainstalovat následující balíčky NuGet, opakujte výše uvedené kroky:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Vytvoření třídy kontextu databáze

Třída kontextu databáze je potřeba ke koordinaci funkce EF Core (Vytvořit, Číst, Aktualizovat, Odstranit) pro `Movie` model. Kontext databáze je odvozen od [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a určuje entity, které mají být zahrnuty do datového modelu.

Vytvořte složku *Data.*

Přidejte soubor *Data/MvcMovieContext.cs* s následujícím kódem: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Předchozí kód vytvoří [vlastnost\<DbSet Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. V terminologii entity framework sada entit obvykle odpovídá databázové tabulce. Entita odpovídá řádku v tabulce.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Registrace kontextu databáze

ASP.NET Core je postaven s [vstřikování závislostí (DI)](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) musí být registrovány s DI při spuštění aplikace. Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu DB je zobrazen dále v kurzu. V této části zaregistrujete kontext databáze s kontejnerem DI.

V horní `using` části *Startup.cs*přidejte následující příkazy :

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

V kládání `Startup.ConfigureServices`přidejte následující zvýrazněný kód:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu. Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Přidání připojovacího řetězce databáze

Přidejte připojovací řetězec do souboru *appsettings.json:*

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Sestavení projektu jako kontrola chyb kompilátoru.

## <a name="scaffold-movie-pages"></a>Stránky filmu lešení

Nástroj zasychání slouží k vytvoření stránek CRUD pro vytvoření, čtení, aktualizaci a odstranění (CRUD).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku *Řadiče* **> Přidat > novou položku scaffolded item**.

![pohled na výše uvedený krok](adding-model/_static/add_controller21.png)

V dialogovém okně **Přidat pomocí okna Pomocí** entity Framework vyberte řadič **MVC se zobrazeními pomocí entity Framework > Add**.

![Dialogové okno Přidat zasaná cíp](adding-model/_static/add_scaffold21.png)

Dokončete dialogové okno **Přidat řadič:**

* **Třída modelu:** *Film (MvcMovie.Models)*
* **Třída kontextu dat:** *MvcMovieContext (MvcMovie.Data)*

![Přidat kontext dat](adding-model/_static/dc3.png)

* **Zobrazení:** Zachovat výchozí nastavení jednotlivých možností
* **Název řadiče:** Zachovat výchozí *ovladač MoviesController*
* Vyberte **Přidat**.

Visual Studio vytváří:

* Řadič pro filmy *(Controllers/MoviesController.cs*)
* Soubory zobrazení holicí strojek pro stránky Vytvořit, Odstranit, Podrobnosti, Upravit a Index *(Zobrazení/\*Filmy / .cshtml)*

Automatické vytváření těchto souborů se označuje jako *generování uživatelského a uživatelského zařízení*.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*

* Na Linuxu exportujte cestu nástroje lešení:

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*

* Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Stránky skládaným šetřenou šaškárovou fénou zatím nelze použít, protože databáze neexistuje. Pokud aplikaci spustíte a kliknete na odkaz **Aplikace pro film,** zobrazí se *chybová zpráva Aplikace Nelze otevřít* nebo žádná *tabulka: Filmová* chybová zpráva.

<a name="migration"></a>

## <a name="initial-migration"></a>Počáteční migrace

K vytvoření databáze použijte funkci EF Core [Migrations.](xref:data/ef-mvc/migrations) Migrace je sada nástrojů, které umožňují vytvořit a aktualizovat databázi tak, aby odpovídala datovému modelu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V nabídce **Nástroje** vyberte konzolu Správce balíčků **Aplikace NuGet Správce** > **balíčků** (PMC).

Do pmc zadejte následující příkazy:

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: Generuje migrační soubor *Migrace/{timestamp}_InitialCreate.cs.* Argument `InitialCreate` je název migrace. Lze použít libovolný název, ale podle konvence je vybrán název, který popisuje migraci. Protože se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze. Schéma databáze je založeno na modelu zadaném `MvcMovieContext` ve třídě.

* `Update-Database`: Aktualizuje databázi na nejnovější migraci, kterou vytvořil předchozí příkaz. Tento příkaz `Up` spustí metodu v souboru *Migrations/{time-stamp}_InitialCreate.cs,* který vytvoří databázi.

  Příkaz Aktualizace databáze generuje následující upozornění: 

  > Pro desítkový sloupec Cena u typu entity Film nebyl zadán žádný typ. To způsobí, že hodnoty, které mají být tiše zkrácena, pokud se nevejdou do výchozí přesnosti a měřítka. Explicitně určete typ sloupce serveru SQL, který může pojmout všechny hodnoty pomocí 'HasColumnType()'.

  Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Spusťte následující příkazy příkazového příkazu .NET Core CLI:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: Generuje migrační soubor *Migrace/{timestamp}_InitialCreate.cs.* Argument `InitialCreate` je název migrace. Lze použít libovolný název, ale podle konvence je vybrán název, který popisuje migraci. Protože se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze. Schéma databáze je založeno na modelu zadaném `MvcMovieContext` ve třídě (v souboru *Data/MvcMovieContext.cs).*

* `ef database update`: Aktualizuje databázi na nejnovější migraci, kterou vytvořil předchozí příkaz. Tento příkaz `Up` spustí metodu v souboru *Migrations/{time-stamp}_InitialCreate.cs,* který vytvoří databázi.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a>Třída InitialCreate

Zkontrolujte migrační soubor *Migrace/{timestamp}_InitialCreate.cs:*

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

Metoda `Up` vytvoří tabulku Film a `Id` nakonfiguruje se jako primární klíč. Metoda `Down` vrátí změny schématu provedené migrací. `Up`

<a name="test"></a>

## <a name="test-the-app"></a>Otestování aplikace

* Spusťte aplikaci a klikněte na odkaz **Aplikace pro film.**

  Pokud se vám výjimka podobná jedné z následujících možností:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Pravděpodobně jste vynechal [migrace krok](#migration).

* Otestujte stránku **Vytvořit.** Zadejte a odešlete data.

  > [!NOTE]
  > Je možné, že do pole nebudete moci `Price` zadat desetinná čárka. Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a pro formáty kalendářních dat mimo americkou angličtinu, musí být aplikace globalizovaná. Pokyny pro globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Otestujte stránky **Úpravy**, **Podrobnosti**a **Odstranit.**

## <a name="dependency-injection-in-the-controller"></a>Vkládání závislostí v řadiči

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Otevřete soubor *Controllers/MoviesController.cs* a zkontrolujte konstruktor:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k vložení`MvcMovieContext`kontextu databáze ( ) do řadiče. Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v řadiči.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k vložení`MvcMovieContext`kontextu databáze ( ) do řadiče. Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v řadiči.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Použití SQLite pro vývoj, SQL Server pro výrobu

Když je vybránsQLite, vygenerovaný kód šablony je připraven k vývoji. Následující kód ukazuje, <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> jak vložit do spuštění. `IWebHostEnvironment`je injektován tak `ConfigureServices` lze použít SQLite ve vývoji a SQL Server v produkčním prostředí.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modely silného typu @model a klíčové slovo

Dříve v tomto kurzu jste viděli, jak řadič může předat `ViewData` data nebo objekty do zobrazení pomocí slovníku. Slovník `ViewData` je dynamický objekt, který poskytuje pohodlný způsob pozdní sazby předat informace do zobrazení.

MVC také poskytuje možnost předat objekty modelu silného typu do pohledu. Tento přístup silného typu umožňuje kontrolu kódu času kompilace. Mechanismus lešení používá tento přístup (to znamená předávání modelu silného typu) s třídou `MoviesController` a zobrazeními.

Prohlédněte si `Details` vygenerovanou metodu v souboru *Controllers/MoviesController.cs:*

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Parametr `id` je obecně předán jako data trasy. Například `https://localhost:5001/movies/details/1` sady:

* Řadič k `movies` řadiči (první segment ADRESY URL).
* Akce `details` do (druhý segment adresy URL).
* Id na 1 (poslední segment adresy URL).

Můžete také předat `id` s řetězcem dotazu takto:

`https://localhost:5001/movies/details?id=1`

Parametr `id` je definován jako [typ s možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) v případě, že není zadána hodnota ID.

[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán `FirstOrDefaultAsync` k výběru filmových entit, které odpovídají datům trasy nebo hodnotě řetězce dotazu.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Pokud je film nalezen, je `Movie` do `Details` pohledu předána instance modelu:

```csharp
return View(movie);
```

Prohlédněte si obsah souboru *Views/Movies/Details.cshtml:*

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Příkaz `@model` v horní části souboru zobrazení určuje typ objektu, který zobrazení očekává. Při vytvoření řadiče filmu `@model` byl zahrnut následující příkaz:

```cshtml
@model MvcMovie.Models.Movie
```

Tato `@model` směrnice umožňuje přístup k filmu, který řadič předán zobrazení. Objekt `Model` je silně zadán. Například v zobrazení *Details.cshtml* kód předá každé `DisplayNameFor` pole `DisplayFor` filmu pomocníkům HTML s `Model` objektem silného typu. Metody `Create` `Edit` a pohledy a `Movie` pohledy také předat objekt modelu.

Zkontrolujte zobrazení *Index.cshtml* a metodu `Index` v kontroleru filmy. Všimněte si, `List` jak kód vytvoří `View` objekt při volání metody. Kód předá `Movies` tento `Index` seznam z metody akce do zobrazení:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Když byl vytvořen řadič filmů, lešení `@model` obsahovalo v horní části souboru *Index.cshtml* následující příkaz:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Směrnice `@model` umožňuje přístup k seznamu filmů, které řadič předán zobrazení `Model` pomocí objektu, který je silně zadali. Například v zobrazení *Index.cshtml* kód prochází filmy s `foreach` příkazem přes objekt `Model` silného typu:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Vzhledem `Model` k tomu, že objekt `IEnumerable<Movie>` je silně zadán (jako objekt), každá položka ve smyčce je zadán jako `Movie`. Mezi další výhody to znamená, že získáte kontrolu času kompilace kódu.

## <a name="additional-resources"></a>Další zdroje

* [Pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)
* [Globalizace a lokalizace](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Předchozí přidání zobrazení](adding-view.md)
> [další práce s SQL](working-with-sql.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Přidání třídy datového modelu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klepněte pravým tlačítkem myši na složku *Modely* > **Přidat** > **třídu**. Pojmenujte třídu **Film**.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidejte třídu do složky *Models* s názvem *Movie.cs*.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>Lešení filmového modelu

V této části je filmový model scaffolded. To znamená, že nástroj lešení vytváří stránky pro operace Vytvořit, Číst, Aktualizovat a Odstranit (CRUD) pro filmový model.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku *Řadiče* **> Přidat > novou položku scaffolded item**.

![pohled na výše uvedený krok](adding-model/_static/add_controller21.png)

V dialogovém okně **Přidat pomocí okna Pomocí** entity Framework vyberte řadič **MVC se zobrazeními pomocí entity Framework > Add**.

![Dialogové okno Přidat zasaná cíp](adding-model/_static/add_scaffold21.png)

Dokončete dialogové okno **Přidat řadič:**

* **Třída modelu:** *Film (MvcMovie.Models)*
* **Třída kontextu dat:** Vyberte **+** ikonu a přidejte výchozí **MvcMovie.Models.MvcMovieContext**

![Přidat kontext dat](adding-model/_static/dc.png)

* **Zobrazení:** Zachovat výchozí nastavení jednotlivých možností
* **Název řadiče:** Zachovat výchozí *ovladač MoviesController*
* Vyberte **Přidat**.

![Dialogové okno Přidat řadič](adding-model/_static/add_controller2.png)

Visual Studio vytváří:

* Třída kontextu [základní databáze](xref:data/ef-mvc/intro#create-the-database-context) entity frameworku *(Data/MvcMovieContext.cs)*
* Řadič pro filmy *(Controllers/MoviesController.cs*)
* Soubory zobrazení holicí strojek pro stránky Vytvořit, Odstranit, Podrobnosti, Upravit a Index *(Zobrazení/\*Filmy / .cshtml)*

Automatické vytváření kontextu databáze a metody a zobrazení akce [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (vytvoření, čtení, aktualizace a odstranění) se označují jako *generování uživatelského líačku*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*
* Nainstalujte nástroj lešení:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Na Linuxu exportujte cestu nástroje lešení:

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Spusťte následující příkaz:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*
* Nainstalujte nástroj lešení:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Spusťte následující příkaz:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

Pokud aplikaci spustíte a kliknete na odkaz **Mvc Movie,** zobrazí se chyba podobná následující:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

Je třeba vytvořit databázi a použít funkci EF core [migrace](xref:data/ef-mvc/migrations) k tomu. Migrace umožňuje vytvořit databázi, která odpovídá datovému modelu a aktualizovat schéma databáze při změně datového modelu.

<a name="pmc"></a>

## <a name="initial-migration"></a>Počáteční migrace

V této části jsou dokončeny následující úkoly:

* Přidejte počáteční migraci.
* Aktualizujte databázi počáteční migrací.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V nabídce **Nástroje** vyberte konzolu Správce balíčků **Aplikace NuGet Správce** > **balíčků** (PMC).

   ![Nabídka PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. Do pmc zadejte následující příkazy:

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   Příkaz `Add-Migration` generuje kód k vytvoření počátečního schématu databáze.

   Schéma databáze je založeno na modelu zadaném `MvcMovieContext` ve třídě. Argument `Initial` je název migrace. Lze použít libovolný název, ale podle konvence se používá název, který popisuje migraci. Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.

   Příkaz `Update-Database` spustí `Up` metodu v souboru *Migrations/{time-stamp}_InitialCreate.cs,* který vytvoří databázi.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

Příkaz `ef migrations add InitialCreate` generuje kód k vytvoření počátečního schématu databáze.

Schéma databáze je založeno na modelu zadaném `MvcMovieContext` ve třídě (v souboru *Data/MvcMovieContext.cs).* Argument `InitialCreate` je název migrace. Lze použít libovolný název, ale podle konvence je vybrán název, který popisuje migraci.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>Prozkoumejte kontext registrovaný pomocí vkládání závislostí

ASP.NET Core je postaven s [vstřikování závislostí (DI)](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) jsou registrovány s DI při spuštění aplikace. Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu DB je zobrazen dále v kurzu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nástroj lešení automaticky vytvořil kontext DB a zaregistroval jej do kontejneru DI.

Prozkoumejte `Startup.ConfigureServices` následující metodu. Zvýrazněný řádek byl přidán lešení:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

Souřadnice `MvcMovieContext` EF core funkce (vytvořit, číst, aktualizovat, `Movie` odstranit, atd.) pro model. Kontext dat`MvcMovieContext`( ) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje, které entity jsou zahrnuty do datového modelu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

Předchozí kód vytvoří [vlastnost\<DbSet Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. V terminologii entity framework sada entit obvykle odpovídá databázové tabulce. Entita odpovídá řádku v tabulce.

Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu. Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Vytvořili jste kontext DB a zaregistrovali ho s kontejnerem DI.

---

<a name="test"></a>

### <a name="test-the-app"></a>Otestování aplikace

* Spusťte aplikaci `/Movies` a připojte`http://localhost:port/movies`adresu URL v prohlížeči ( ).

Pokud se vám výjimka databáze podobná následující:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Jste vynechal [krok migrace](#pmc).

* Otestujte odkaz **Vytvořit.** Zadejte a odešlete data.

  > [!NOTE]
  > Je možné, že do pole nebudete moci `Price` zadat desetinná čárka. Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a pro formáty kalendářních dat mimo americkou angličtinu, musí být aplikace globalizovaná. Pokyny pro globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.

Zkontrolujte `Startup` třídu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Předchozí zvýrazněný kód zobrazuje kontext databáze filmu, který se přidává do [kontejneru vkládání závislostí:](xref:fundamentals/dependency-injection)

* `services.AddDbContext<MvcMovieContext>(options =>`určuje databázi, která má být používána, a připojovací řetězec.
* `=>`je [provozovatelem lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)

Otevřete soubor *Controllers/MoviesController.cs* a zkontrolujte konstruktor:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k vložení`MvcMovieContext`kontextu databáze ( ) do řadiče. Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v řadiči.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modely silného typu @model a klíčové slovo

Dříve v tomto kurzu jste viděli, jak řadič může předat `ViewData` data nebo objekty do zobrazení pomocí slovníku. Slovník `ViewData` je dynamický objekt, který poskytuje pohodlný způsob pozdní sazby předat informace do zobrazení.

MVC také poskytuje možnost předat objekty modelu silného typu do pohledu. Tento přístup silného typu umožňuje lepší kontrolu času kompilace kódu. Mechanismus generování uživatelského líacího a parafingu používá tento přístup `MoviesController` (to znamená předávání modelu silného typu) s třídou a zobrazeními při vytváření metod a pohledů.

Prohlédněte si `Details` vygenerovanou metodu v souboru *Controllers/MoviesController.cs:*

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Parametr `id` je obecně předán jako data trasy. Například `https://localhost:5001/movies/details/1` sady:

* Řadič k `movies` řadiči (první segment ADRESY URL).
* Akce `details` do (druhý segment adresy URL).
* Id na 1 (poslední segment adresy URL).

Můžete také předat `id` s řetězcem dotazu takto:

`https://localhost:5001/movies/details?id=1`

Parametr `id` je definován jako [typ s možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) v případě, že není zadána hodnota ID.

[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán `FirstOrDefaultAsync` k výběru filmových entit, které odpovídají datům trasy nebo hodnotě řetězce dotazu.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Pokud je film nalezen, je `Movie` do `Details` pohledu předána instance modelu:

```csharp
return View(movie);
   ```

Prohlédněte si obsah souboru *Views/Movies/Details.cshtml:*

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Zahrnutím `@model` příkazu do horní části souboru zobrazení můžete určit typ objektu, který zobrazení očekává. Když jste vytvořili řadič `@model` filmu, byl v horní části souboru *Details.cshtml* automaticky zahrnut následující příkaz:

```cshtml
@model MvcMovie.Models.Movie
```

Tato `@model` směrnice umožňuje přístup k filmu, který řadič předán `Model` zobrazení pomocí objektu, který je silně zadaný. Například v zobrazení *Details.cshtml* kód předá každé `DisplayNameFor` pole `DisplayFor` filmu pomocníkům HTML s `Model` objektem silného typu. Metody `Create` `Edit` a pohledy a `Movie` pohledy také předat objekt modelu.

Zkontrolujte zobrazení *Index.cshtml* a metodu `Index` v kontroleru filmy. Všimněte si, `List` jak kód vytvoří `View` objekt při volání metody. Kód předá `Movies` tento `Index` seznam z metody akce do zobrazení:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Když jste vytvořili řadič pro filmy, lešení automaticky zahrnulo do horní části souboru `@model` *Index.cshtml* následující příkaz:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Směrnice `@model` umožňuje přístup k seznamu filmů, které řadič předán zobrazení `Model` pomocí objektu, který je silně zadali. Například v zobrazení *Index.cshtml* kód prochází filmy s `foreach` příkazem přes objekt `Model` silného typu:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Vzhledem `Model` k tomu, že objekt `IEnumerable<Movie>` je silně zadán (jako objekt), každá položka ve smyčce je zadán jako `Movie`. Mezi další výhody, to znamená, že dostanete kompilaci čas kontroly kódu:

## <a name="additional-resources"></a>Další zdroje

* [Pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)
* [Globalizace a lokalizace](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Předchozí přidání zobrazení](adding-view.md)
> [další práce s databází](working-with-sql.md)

::: moniker-end
