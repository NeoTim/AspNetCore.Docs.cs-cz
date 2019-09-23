---
title: Přidání modelu do ASP.NET Core aplikace MVC
author: rick-anderson
description: Přidejte model do jednoduché aplikace ASP.NET Core.
ms.author: riande
ms.date: 8/15/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: b2ef7a582dfedcd670af7bb18a6927cec31f6414
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187268"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a>Přidání modelu do ASP.NET Core aplikace MVC

[Rick Anderson](https://twitter.com/RickAndMSFT) a [Dykstra](https://github.com/tdykstra)

V této části přidáte třídy pro správu filmů v databázi. Tyto třídy budou součástí aplikace typu **m**VC jako "**m**Odel".

Tyto třídy použijete s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází. EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje kód pro přístup k datům, který je nutné zapsat.

Třídy modelů, které vytvoříte, jsou známé jako třídy POCO (od **P**Lain **O**ld **C**LR **O**bjekty), protože nemají žádnou závislost na EF Core. Pouze definují vlastnosti dat, která budou uložena v databázi.

V tomto kurzu napíšete nejprve třídy modelu a EF Core vytvoříte databázi. Alternativním způsobem, který není pokrytý, je vygenerování tříd modelu z existující databáze. Informace o tomto přístupu najdete v tématu [ASP.NET Core – existující databáze](/ef/core/get-started/aspnetcore/existing-db).

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Přidat třídu datového modelu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Klikněte pravým tlačítkem na složku *modely* > **Přidat** > **třídu**. Název souboru *Movie.cs*.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

Do složky *modely* přidejte soubor s názvem *Movie.cs* .

---

Aktualizujte soubor *Movie.cs* pomocí následujícího kódu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

`Movie` Třída`Id` obsahuje pole, které je vyžadováno databází pro primární klíč.

Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) v `ReleaseDate` určuje typ dat (`Date`). S tímto atributem:

  * Uživatel není požádán o zadání informací o čase do pole datum.
  * Zobrazí se pouze datum, nejedná se o informace o čase.

V pozdějším kurzu jsou uvedena tato [Anotace](/dotnet/api/system.componentmodel.dataannotations) .

## <a name="add-nuget-packages"></a>Přidat balíčky NuGet

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V nabídce **nástroje** vyberte > **Správce balíčků NuGet** **Konzola správce balíčků** (PMC).

![PMC nabídky](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

V PMC spusťte následující příkaz:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer -IncludePrerelease
```

Předchozí příkaz přidá poskytovatele EF Core SQL Server. Balíček Provider nainstaluje balíček EF Core jako závislost. Další balíčky jsou automaticky nainstalovány v kroku generování uživatelského rozhraní později v tomto kurzu.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Vytvoření třídy kontextu databáze

Třída kontextu databáze je nutná ke koordinaci funkcí EF Core (vytvoření, čtení, aktualizace, odstranění) `Movie` modelu. Kontext databáze je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a určuje entity, které mají být zahrnuty do datového modelu.

Vytvořte složku *dat* .

Přidejte soubor *data/MvcMovieContext. cs* s následujícím kódem: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Předchozí kód vytvoří vlastnost [\<negenerickými Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky. Entita odpovídající řádek v tabulce.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Zaregistrujte kontext databáze

ASP.NET Core je sestaven s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) musí být při spuštění aplikace zaregistrované v DI. Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru. Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze. V této části zaregistrujete kontext databáze pomocí kontejneru DI.

Do horní části `using` *Startup.cs*přidejte následující příkazy:

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Do `Startup.ConfigureServices`následujícího pole přidejte následující zvýrazněný kód:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu. Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Přidat připojovací řetězec databáze

Přidejte do souboru *appSettings. JSON* připojovací řetězec:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Sestavte projekt jako kontrolu chyb kompilátoru.

## <a name="scaffold-movie-pages"></a>Stránky filmového uživatelského rozhraní

Použijte nástroj pro generování uživatelského rozhraní k vytvoření stránek pro vytváření, čtení, aktualizaci a odstranění (CRUD) pro model filmu.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Controllers* **> přidat > novou vygenerované položky**.

![zobrazení výše uvedeného kroku](adding-model/_static/add_controller21.png)

V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **kontroler MVC se zobrazeními a pomocí Entity Framework > Přidat**.

![Dialogové okno Přidat generování uživatelského rozhraní](adding-model/_static/add_scaffold21.png)

Dokončete dialog **Přidat řadič** :

* **Třída modelu:** *Video (MvcMovie. Models)*
* **Třída kontextu dat:** *MvcMovieContext (MvcMovie. data)*

![Přidat kontext dat](adding-model/_static/dc3.png)

* **Náhled** Ponechte výchozí hodnotu u každé zaškrtnuté možnosti.
* **Název kontroleru:** Zachovat výchozí *MoviesController*
* Vyberte **Přidat**

Visual Studio vytvoří:

* Řadič filmů (*Controllers/MoviesController. cs*)
* Soubory zobrazení Razor pro stránky vytvořit, odstranit, podrobnosti, upravit a index (*zobrazení/filmy/\*. cshtml*)

Automatické vytváření těchto souborů se říká *generování uživatelského rozhraní*.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).

* V systému Linux exportujte cestu k nástroji pro generování uživatelského rozhraní:

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Spusťte následující příkaz:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).

* Spusťte následující příkaz:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Vygenerované stránky nemůžete zatím použít, protože databáze neexistuje. Pokud aplikaci spouštíte a kliknete na odkaz **filmové aplikace** , *nemůžete otevřít databázi* ani *žádnou takovou tabulku: Chybová* zpráva videa

<a name="migration"></a>

## <a name="initial-migration"></a>Počáteční migraci

K vytvoření databáze použijte funkci [migrace](xref:data/ef-mvc/migrations) EF Core. Migrace je sada nástrojů, která umožňuje vytvořit a aktualizovat databázi tak, aby odpovídala vašemu datovému modelu.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V nabídce **nástroje** vyberte > **Správce balíčků NuGet** **Konzola správce balíčků** (PMC).

V konzole PMC zadejte následující příkazy:

```console
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: Vygeneruje migrační soubor *_InitialCreate. cs migrace/{timestamp}* . `InitialCreate` Argument je název migrace. Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci. Vzhledem k tomu, že se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze. Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě.

* `Update-Database`: Aktualizuje databázi na nejnovější migraci, kterou byl vytvořen předchozí příkaz. Tento příkaz spustí `Up` metodu v souboru *migrations/{Time-razítk} _InitialCreate. cs* , který vytvoří databázi.

  Příkaz aktualizace databáze generuje následující upozornění: 

  > Pro desetinný sloupec ' Price ' pro typ entity ' film ' nebyl zadán žádný typ. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL Server, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.

  Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

Spusťte následující příkazy .NET Core CLI:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: Vygeneruje migrační soubor *_InitialCreate. cs migrace/{timestamp}* . `InitialCreate` Argument je název migrace. Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci. Vzhledem k tomu, že se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze. Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě (v souboru *data/MvcMovieContext. cs* ).

* `ef database update`: Aktualizuje databázi na nejnovější migraci, kterou byl vytvořen předchozí příkaz. Tento příkaz spustí `Up` metodu v souboru *migrations/{Time-razítk} _InitialCreate. cs* , který vytvoří databázi.

[!INCLUDE [ more information on the CLI tools for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a>Třída InitialCreate

Projděte si migrační soubor *migrace/{timestamp} _InitialCreate. cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

 Metoda vytvoří tabulku filmů a nakonfiguruje `Id` ji jako primární klíč. `Up` Metoda vrátí změny schématu provedené `Up` migrací. `Down`

<a name="test"></a>

## <a name="test-the-app"></a>Testování aplikace

* Spusťte aplikaci a klikněte na odkaz **video aplikace** .

  Pokud se zobrazí výjimka podobná jedné z následujících:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Pravděpodobně jste [Krok migrace](#migration)vynechali.

* Otestujte stránku **vytvořit** . Zadejte a odešlete data.

  > [!NOTE]
  > Není možné zadat desetinné čárky v `Price` pole. Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována. Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Otestujte stránky **Upravit**, **Podrobnosti**a **Odstranit** .

## <a name="dependency-injection-in-the-controller"></a>Vkládání závislostí v kontroleru

Otevřete soubor *Controllers/MoviesController. cs* a prověřte konstruktor:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze (`MvcMovieContext`) do kontroleru. Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modely silného typu a @model klíčové slovo

Dříve v tomto kurzu jste viděli, jak může řadič předat data nebo objekty do zobrazení pomocí `ViewData` slovníku. `ViewData` Slovník je dynamický objekt, který poskytuje pohodlný způsob, jak předat informace do zobrazení.

MVC také poskytuje možnost předat objekty modelu silného typu do zobrazení. Tento přístup se silnými typy umožňuje kompilovat kontrolu kódu při kompilaci. Mechanizmus pro generování uživatelského rozhraní používal tento přístup (to znamená předání modelu silného typu) s `MoviesController` třídou a zobrazeními.

Prověřte vygenerovanou `Details` metodu v souboru *Controllers/MoviesController. cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

`id` Parametr je obvykle předán jako data směrování. Například `https://localhost:5001/movies/details/1` sady:

* Kontroler `movies` řadiče (první segment adresy URL).
* Akce na `details` (druhý segment adresy URL).
* ID na 1 (poslední segment adresy URL).

Řetězec dotazu můžete také předat `id` následujícím způsobem:

`https://localhost:5001/movies/details?id=1`

Parametr je definován jako typ s [možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) pro případ, že není zadána hodnota ID. `id`

[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán `FirstOrDefaultAsync` do pro výběr entit videa, které odpovídají datům směrování nebo hodnotě řetězce dotazu.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Pokud se najde film, `Movie` `Details` do zobrazení se předává instance modelu:

```csharp
return View(movie);
   ```

Projděte si obsah souboru *views/video/details. cshtml* :

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

`@model` Příkaz v horní části souboru zobrazení určuje typ objektu, který zobrazení očekává. Po vytvoření kontroleru filmů byl zahrnut následující `@model` příkaz:

```HTML
@model MvcMovie.Models.Movie
   ```

Tato `@model` Direktiva umožňuje přístup k videu, který kontroler předali do zobrazení. `Model` Objekt je silného typu. Například v zobrazení *Details. cshtml* kód předá každé pole videa do `DisplayNameFor` pomocníků HTML a `DisplayFor` s objektem silného typu `Model` . Metody `Create` a `Edit` zobrazení také`Movie` předají objekt modelu.

Prohlédněte si zobrazení *index. cshtml* a `Index` metodu v řadiči filmů. Všimněte si, jak kód při `List` `View` volání metody vytvoří objekt. Kód předá tento `Movies` seznam `Index` z metody Action do zobrazení:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Po vytvoření kontroleru filmů zahrnuje `@model` generování uživatelského rozhraní do horní části souboru *index. cshtml* tento příkaz:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Direktiva umožňuje přístup k seznamu filmů, které kontroler předává do zobrazení `Model` pomocí objektu se silným typem. `@model` Například v zobrazení *index. cshtml* přechází kód přes filmy pomocí `foreach` příkazu nad objektem silného typu: `Model`

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Vzhledem k tomu, že `IEnumerable<Movie>` `Movie` objektjesilnéhotypu(jakoobjekt),každápoložkavesmyčcejezapsána`Model` jako. Kromě jiných výhod to znamená, že se vám bude zobrazovat doba kompilace kódu.

## <a name="additional-resources"></a>Další zdroje

* [Pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)
* [Globalizace a lokalizace](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Předchozí přidání zobrazení](adding-view.md)
> [Next Working with SQL](working-with-sql.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Přidat třídu datového modelu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Klikněte pravým tlačítkem na složku *modely* > **Přidat** > **třídu**. Název třídy **filmu**.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidat třídu *modely* složku s názvem *Movie.cs*.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>Vygenerované uživatelské rozhraní Video modelu

V této části je automaticky generovaný model video. To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Controllers* **> přidat > novou vygenerované položky**.

![zobrazení výše uvedeného kroku](adding-model/_static/add_controller21.png)

V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **kontroler MVC se zobrazeními a pomocí Entity Framework > Přidat**.

![Dialogové okno Přidat generování uživatelského rozhraní](adding-model/_static/add_scaffold21.png)

Dokončete dialog **Přidat řadič** :

* **Třída modelu:** *Video (MvcMovie. Models)*
* **Třída kontextu dat:** Vyberte ikonu a přidejte výchozí **MvcMovie. Models. MvcMovieContext.** **+**

![Přidat kontext dat](adding-model/_static/dc.png)

* **Náhled** Ponechte výchozí hodnotu u každé zaškrtnuté možnosti.
* **Název kontroleru:** Zachovat výchozí *MoviesController*
* Vyberte **Přidat**

![Dialogové okno Přidat řadič](adding-model/_static/add_controller2.png)

Visual Studio vytvoří:

* [Třída kontextu databáze](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*data/MvcMovieContext. cs*)
* Řadič filmů (*Controllers/MoviesController. cs*)
* Soubory zobrazení Razor pro stránky vytvořit, odstranit, podrobnosti, upravit a index (*zobrazení/filmy/\*. cshtml*)

K automatickému vytvoření kontextu databáze a operací [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (vytvoření, čtení, aktualizace a odstranění) se říká *generování uživatelského rozhraní*.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).
* Nainstalujte nástroj pro generování uživatelského rozhraní:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* V systému Linux exportujte cestu k nástroji pro generování uživatelského rozhraní:

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Spusťte následující příkaz:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).
* Nainstalujte nástroj pro generování uživatelského rozhraní:

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

Pokud aplikaci spouštíte a kliknete na **filmový odkaz MVC** , zobrazí se chybová zpráva podobná následující:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

Musíte vytvořit databázi a k tomu použijte funkci [migrace](xref:data/ef-mvc/migrations) EF Core. Migrace vám umožní vytvořit databázi, která odpovídá vašemu datovému modelu, a aktualizovat schéma databáze při změně datového modelu.

<a name="pmc"></a>

## <a name="initial-migration"></a>Počáteční migraci

V této části jsou dokončeny následující úkoly:

* Přidáte počáteční migraci.
* Aktualizujte počáteční migraci databáze.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V nabídce **nástroje** vyberte > **Správce balíčků NuGet** **Konzola správce balíčků** (PMC).

   ![PMC nabídky](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. V konzole PMC zadejte následující příkazy:

   ```console
   Add-Migration Initial
   Update-Database
   ```

   `Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.

   Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě. `Initial` Argument je název migrace. Můžete použít libovolný název, ale podle konvence se použije název, který popisuje migraci. Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.

   `Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.

Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě (v souboru *data/MvcMovieContext. cs* ). `InitialCreate` Argument je název migrace. Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>Prozkoumání kontextu registrovaný pomocí vkládání závislostí

ASP.NET Core je sestaven s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) jsou během spuštění aplikace zaregistrované v DI. Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru. Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru DI.

Projděte si `Startup.ConfigureServices` následující metodu. Zvýrazněný řádek byl přidán modulem scaffolder:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

`MvcMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu. Kontext dat (`MvcMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje, které entity jsou v datovém modelu zahrnuté:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

Předchozí kód vytvoří vlastnost [\<negenerickými Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky. Entita odpovídající řádek v tabulce.

Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu. Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

Vytvořili jste kontext databáze a zaregistrovali jej pomocí kontejneru DI.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testování aplikace

* Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).

Pokud získáte výjimku databáze podobnou následující:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Je provedena [kroku migrace](#pmc).

* Test **vytvořit** odkaz. Zadejte a odešlete data.

  > [!NOTE]
  > Není možné zadat desetinné čárky v `Price` pole. Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována. Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Test **upravit**, **podrobnosti**, a **odstranit** odkazy.

Prověřte `Startup` třídu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Předchozí zvýrazněný kód ukazuje kontext databáze filmů přidaný do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) :

* `services.AddDbContext<MvcMovieContext>(options =>`Určuje databázi, která se má použít, a připojovací řetězec.
* `=>`je [operátor lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)

Otevřete soubor *Controllers/MoviesController. cs* a prověřte konstruktor:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze (`MvcMovieContext`) do kontroleru. Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modely silného typu a @model klíčové slovo

Dříve v tomto kurzu jste viděli, jak může řadič předat data nebo objekty do zobrazení pomocí `ViewData` slovníku. `ViewData` Slovník je dynamický objekt, který poskytuje pohodlný způsob, jak předat informace do zobrazení.

MVC také poskytuje možnost předat objekty modelu silného typu do zobrazení. Tento přístup silného typu umožňuje lepší kompilaci kódu. Mechanismus generování uživatelského rozhraní používal tento přístup (to znamená předání modelu silného typu) s `MoviesController` třídou a zobrazeními, když vytváří metody a zobrazení.

Prověřte vygenerovanou `Details` metodu v souboru *Controllers/MoviesController. cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

`id` Parametr je obvykle předán jako data směrování. Například `https://localhost:5001/movies/details/1` sady:

* Kontroler `movies` řadiče (první segment adresy URL).
* Akce na `details` (druhý segment adresy URL).
* ID na 1 (poslední segment adresy URL).

Řetězec dotazu můžete také předat `id` následujícím způsobem:

`https://localhost:5001/movies/details?id=1`

Parametr je definován jako typ s [možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) pro případ, že není zadána hodnota ID. `id`

[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán `FirstOrDefaultAsync` do pro výběr entit videa, které odpovídají datům směrování nebo hodnotě řetězce dotazu.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Pokud se najde film, `Movie` `Details` do zobrazení se předává instance modelu:

```csharp
return View(movie);
   ```

Projděte si obsah souboru *views/video/details. cshtml* :

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Zahrnutím `@model` příkazu v horní části souboru zobrazení můžete určit typ objektu, který zobrazení očekává. Při vytváření kontroleru filmů byl do horní části souboru `@model` *Details. cshtml* přidán následující příkaz:

```HTML
@model MvcMovie.Models.Movie
   ```

Tato `@model` Direktiva umožňuje přístup k videu, který kontroler předává do zobrazení `Model` pomocí objektu se silným typem. Například v zobrazení *Details. cshtml* kód předá každé pole videa do `DisplayNameFor` pomocníků HTML a `DisplayFor` s objektem silného typu `Model` . Metody `Create` a `Edit` zobrazení také`Movie` předají objekt modelu.

Prohlédněte si zobrazení *index. cshtml* a `Index` metodu v řadiči filmů. Všimněte si, jak kód při `List` `View` volání metody vytvoří objekt. Kód předá tento `Movies` seznam `Index` z metody Action do zobrazení:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Když jste vytvořili kontroler filmů, generování uživatelského rozhraní automaticky obsahuje následující `@model` příkaz v horní části souboru *index. cshtml* :

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Direktiva umožňuje přístup k seznamu filmů, které kontroler předává do zobrazení `Model` pomocí objektu se silným typem. `@model` Například v zobrazení *index. cshtml* přechází kód přes filmy pomocí `foreach` příkazu nad objektem silného typu: `Model`

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Vzhledem k tomu, že `IEnumerable<Movie>` `Movie` objektjesilnéhotypu(jakoobjekt),každápoložkavesmyčcejezapsána`Model` jako. Kromě jiných výhod to znamená, že se vám bude zobrazovat doba kompilace kódu:

## <a name="additional-resources"></a>Další zdroje

* [Pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)
* [Globalizace a lokalizace](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Předchozí přidání zobrazení](adding-view.md)
> [Next Working with SQL](working-with-sql.md)

::: moniker-end
