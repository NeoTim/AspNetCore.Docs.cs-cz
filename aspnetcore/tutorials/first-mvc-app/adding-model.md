---
title: Přidání modelu pro aplikace ASP.NET Core MVC
author: rick-anderson
description: Přidání modelu pro jednoduchou aplikaci ASP.NET Core.
ms.author: riande
ms.date: 02/12/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: da30c1c97cbf40a89d163b2116c8d5f9ad422b25
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159237"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a>Přidání modelu pro aplikace ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Petr Dykstra](https://github.com/tdykstra)

V této části přidáte třídy pro správu filmy v databázi. Tyto třídy bude "**M**odelu" součástí **M**VC aplikace.

Použití těchto tříd s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází. EF Core je objektově relační mapování (ORM) platforma, která zjednodušuje kód přístupu k datům, který musíte napsat.

Třídy modelu vytvoříte jsou označovány jako POCO třídy (od **P**rostý **O**ld **C**LR **O**bjekty) vzhledem k tomu, že nemají žádné závislosti EF Core. Právě definují vlastnosti data, která se uloží v databázi.

V tomto kurzu nejprve napsat tříd modelu a EF Core vytvoří databázi. Alternativní není součástí tohoto přístupu je k vytvoření tříd modelu z existující databáze. Informace o tento přístup, najdete v části [ASP.NET Core – existující databáze](/ef/core/get-started/aspnetcore/existing-db).

## <a name="add-a-data-model-class"></a>Přidejte třídu modelu dat

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Klikněte pravým tlačítkem myši *modely* složky > **přidat** > **třídy**. Název třídy **filmu**.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Přidat třídu *modely* složku s názvem *Movie.cs*.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---  
<!-- End of VS tabs -->

## <a name="scaffold-the-movie-model"></a>Vygenerované uživatelské rozhraní Video modelu

V této části je automaticky generovaný model video. To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V **Průzkumníka řešení**, klikněte pravým tlačítkem myši *řadiče* složky **> Přidat > novou vygenerovanou položku**.

![Přehled nad krok](adding-model/_static/add_controller21.png)

V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **kontroler MVC se zobrazeními, s použitím Entity Framework > Přidat**.

![Přidat vygenerované uživatelské rozhraní – dialogové okno](adding-model/_static/add_scaffold21.png)

Dokončení **přidat kontroler** dialogové okno:

* **Třída modelu:** *Video (MvcMovie.Models)*
* **Třída kontextu dat:** Vyberte **+** ikonu a přidejte výchozí **MvcMovie.Models.MvcMovieContext**

![Přidat kontext dat](adding-model/_static/dc.png)

* **Zobrazení:** Ponechte výchozí hodnotu každého zaškrtnutým políčkem
* **Název kontroleru:** Ponechte výchozí *MoviesController*
* Vyberte **přidat**

![Přidat dialog Kontroleru](adding-model/_static/add_controller2.png)

Visual Studio vytvoří:

* Entity Framework Core [třídy kontextu databáze](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)
* Kontroler filmy (*Controllers/MoviesController.cs*)
* Zobrazení Razor soubory vytvořit, odstranit, podrobností, úpravy a Index stránky (<em>zobrazení/filmy/&ast;.cshtml</em>)

Automatické vytváření kontext databáze a [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (vytvoření, čtení, aktualizace a odstranění) metody akce a zobrazení se označuje jako *generování uživatelského rozhraní*.

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).
* Nainstalujte nástroj pro generování uživatelského rozhraní:

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Spusťte následující příkaz:

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).
* Nainstalujte nástroj pro generování uživatelského rozhraní:

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Spusťte následující příkaz:

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<!-- End of VS tabs                  -->

Pokud spustíte aplikaci a klikněte na **Mvc Movie** odkaz, dojde k chybě podobný následujícímu:

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

Je potřeba vytvořit databázi, a pomocí EF Core [migrace](xref:data/ef-mvc/migrations) funkce, které provedete. Migrace vám umožní vytvořit databázi, která odpovídá datového modelu a aktualizovat schéma databáze, pokud datový model změny.

<a name="pmc"></a>

## <a name="initial-migration"></a>Počáteční migraci

V této části jsou prováděny následující úlohy:

* Přidáte počáteční migraci.
* Aktualizujte počáteční migraci databáze.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků** (PMC).

   ![PMC nabídky](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. V konzole PMC zadejte následující příkazy:

   ```console
   Add-Migration Initial
   Update-Database
   ```

   `Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.

   Schéma databáze je založeno na zadaném v modelu `MvcMovieContext` třídy (v *Data/MvcMovieContext.cs* souboru). `Initial` Argument je název migrace. Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít. Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.

   `Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.

Schéma databáze je založeno na zadaném v modelu `MvcMovieContext` třídy (v *Data/MvcMovieContext.cs* souboru). `InitialCreate` Argument je název migrace. Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.

---  

Předchozí příkazy Generovat následující upozornění:

```text
No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.
```

Můžete tuto upozornění ignorovat. Opravíme v pozdějších kurzech.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Prozkoumání kontextu registrovaný pomocí vkládání závislostí

ASP.NET Core využívá rozhraní [injektáž závislostí (DI)](xref:fundamentals/dependency-injection). Služby (například kontext EF Core databáze) jsou registrovány DI během spuštění aplikace. Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru. Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Nástroj pro generování uživatelského rozhraní automaticky vytvořen kontext databáze a kontejnerů DI zaregistrován.

Zkontrolujte následující `Startup.ConfigureServices` metody. Zvýrazněný řádek byl přidán modulem scaffolder:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`MvcMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu. Kontext dat (`MvcMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje entit, které jsou zahrnuty v datovém modelu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

Předchozí kód vytvoří [DbSet\<video >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost sady entit. Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky. Entita odpovídající řádek v tabulce.

Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu. Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

Vytvoří kontext databáze a kontejnerů DI zaregistrován.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testování aplikace

* Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).

Pokud dojde k výjimce databáze podobný následujícímu:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Je provedena [kroku migrace](#pmc).

* Test **vytvořit** odkaz.

  > [!NOTE]
  > Není možné zadat desetinné čárky v `Price` pole. Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována. Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).

* Test **upravit**, **podrobnosti**, a **odstranit** odkazy.

Zkontrolujte `Startup` třídy:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Předchozí zvýrazněný kód ukazuje kontext databáze filmů přidávaný do [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru:

* `services.AddDbContext<MvcMovieContext>(options =>` Určuje databázi, do použití a připojovací řetězec.
* `=>` je [lambda – operátor](/dotnet/articles/csharp/language-reference/operators/lambda-operator)

Otevřít *Controllers/MoviesController.cs* souboru a prozkoumání konstruktoru:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Konstruktor používá [injektáž závislostí](xref:fundamentals/dependency-injection) vkládat kontext databáze (`MvcMovieContext `) do kontroleru. Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modely se silnými typy a @model – klíčové slovo

Dříve v tomto kurzu jste viděli, jak kontroleru můžete předat data nebo objekty zobrazení pomocí `ViewData` slovníku. `ViewData` Slovník je dynamický objekt, který představuje pohodlný způsob s pozdní vazbou k předávání informací do zobrazení.

MVC rovněž poskytuje možnost předávání silně typované objekty modelu zobrazení. Tento přístup silného typu umožňuje lepší čas kompilace Kontrola kódu. Generování uživatelského rozhraní mechanismus použít tento přístup (který předává model silného typu) se `MoviesController` třídy a zobrazení při vytvoření rovnou metody a zobrazení.

Zkontrolujte vygenerovaný `Details` metodu *Controllers/MoviesController.cs* souboru:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

`id` Jako data trasy, která je obecně předán parametr. Například `https://localhost:5001/movies/details/1` nastaví:

* Kontroleru `movies` kontroler (první segment adresy URL).
* Akce, která má `details` (druhý segment adresy URL).
* Id na hodnotu 1 (poslední segment adresy URL).

Můžete také předat `id` pomocí dotazu řetězec následujícím způsobem:

`https://localhost:5001/movies/details?id=1`

`id` Parametr je definován jako [typ připouštějící hodnotu Null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) v případě, že je hodnota ID není k dispozici.

A [výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) předáno pracovnímu `FirstOrDefaultAsync` vyberte film entity, které odpovídají směrování dat nebo dotaz řetězcovou hodnotu.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Pokud se najde videa, instance `Movie` modelu je předán `Details` zobrazení:

```csharp
return View(movie);
   ```

Zkontrolovat obsah *Views/Movies/Details.cshtml* souboru:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Zahrnutím `@model` příkazu v horní části souboru zobrazení, můžete určit typ objektu, který očekává, že zobrazení. Při vytvoření kontroleru film, následující `@model` příkaz byl automaticky zahrnut v horní části *Details.cshtml* souboru:

```HTML
@model MvcMovie.Models.Movie
   ```

To `@model` – direktiva umožňuje přístup k video, které kontroleru předána do zobrazení podle používání `Model` objekt, který je silně typováno. Například v *Details.cshtml* zobrazení, že kód předá jednotlivých polí filmů a `DisplayNameFor` a `DisplayFor` pomocných rutin HTML se silnými typy `Model` objektu. `Create` a `Edit` metody a zobrazení také předat `Movie` objekt modelu.

Zkontrolujte *Index.cshtml* zobrazení a `Index` metoda v filmy kontroleru. Všimněte si, jak kód vytvoří `List` objektu při volání `View` metody. Kód předá to `Movies` ze seznamu `Index` metody akce k zobrazení:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Když vytvoříte řadič filmy, generování uživatelského rozhraní automaticky zahrnuty následující `@model` příkazu v horní části *Index.cshtml* souboru:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

`@model` – Direktiva umožňuje přístup k seznamu filmy, které kontroleru předána do zobrazení podle používání `Model` objekt, který je silně typováno. Například v *Index.cshtml* zobrazíte kód cyklicky projde filmů s `foreach` příkaz přes silného typu `Model` objektu:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Protože `Model` objektu je silně typováno (jako `IEnumerable<Movie>` objektu), každé položky ve smyčce je zadán jako `Movie`. Kromě dalších výhod to znamená, že dostanete kompilace Kontrola kódu:

## <a name="additional-resources"></a>Další zdroje

* [Pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)
* [Globalizace a lokalizace](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Předchozím přidávání zobrazení](adding-view.md)
> [další práce s SQL](working-with-sql.md)  
