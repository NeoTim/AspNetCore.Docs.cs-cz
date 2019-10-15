---
title: Přidání modelu do aplikace Razor Pages v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat třídy pro správu filmů v databázi pomocí Entity Framework Core (EF Core).
ms.author: riande
ms.date: 9/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: c101fe4aee9a1fbf28d66a8527e3c199194d73fe
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72334181"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Přidání modelu do aplikace Razor Pages v ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

V této části jsou přidány třídy pro správu filmů v databázi. Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází. EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.

Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core. Definují vlastnosti dat, která jsou uložena v databázi.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Přidání datového modelu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **přidat** **novou složku** > . Pojmenujte *modely*složek.

Klikněte pravým tlačítkem na složku *modely* . Vyberte **Přidat** **třídu** > . Pojmenujte **film**třídy.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *modely*.
* Přidejte třídu do složky *modely* s názvem *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **přidat** **novou složku** > . Pojmenujte *modely*složek.
* Klikněte pravým tlačítkem na složku *modely* a pak vyberte **přidat** **nový soubor**>.
* V dialogovém okně **nový soubor** :

  * V levém podokně vyberte **Obecné** .
  * V prostředním podokně vyberte **prázdnou třídu** .
  * Pojmenujte **film** třídy a vyberte **Nový**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.

## <a name="scaffold-the-movie-model"></a>Generování uživatelského rozhraní modelu filmů

V této části je model filmu vygenerovaný. To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvořit *stránky nebo složky filmů* :

* Klikněte pravým tlačítkem na složku *stránky* > **přidat** **novou složku**>.
* Pojmenovat *filmy* ve složce

Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** **novou vygenerované položky**>.

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :

* V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .
* V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext. [Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje. Vytvoří třídu kontextu databáze se správným oborem názvů.
* Vyberte **Přidat**.

![Obrázek z předchozích instrukcí.](model/_static/3/arp.png)

Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).
* Nainstalujte nástroj pro generování uživatelského rozhraní:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Pro Windows**: spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pro MacOS a Linux**: spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).
* Nainstalujte nástroj pro generování uživatelského rozhraní:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a>Vytvořené soubory

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:

* *Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.
* *Data/RazorPagesMovieContext. cs*

### <a name="updated"></a>Aktualizuj

* *Startup.cs*

Vytvořené a aktualizované soubory jsou vysvětleny v další části.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Proces generování uživatelského rozhraní vytvoří následující soubory:

* *Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.

Vytvořené soubory jsou vysvětleny v další části.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Počáteční migrace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V této části se používá konzola správce balíčků (PMC) k těmto akcím:

* Přidejte počáteční migraci.
* Aktualizujte databázi pomocí prvotní migrace.

V nabídce **nástroje** vyberte **správce balíčků NuGet** > **Konzola správce balíčků**.

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

Do PMC zadejte následující příkazy:

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.

Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.

Příkaz `ef migrations add InitialCreate` generuje kód pro vytvoření počátečního schématu databáze. Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ). K pojmenování migrace se používá argument `InitialCreate`. Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.

Příkaz `ef database update` spustí metodu `Up` v souboru *migrations/\<time-razítko > _InitialCreate. cs* . Metoda `Up` vytvoří databázi.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Kontrola kontextu zaregistrovaného vkládáním závislostí

ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace. Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.

Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.

Projděte si metodu `Startup.ConfigureServices`. Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

Souřadnice `RazorPagesMovieContext` EF Core funkce (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`. Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Předchozí kód vytvoří vlastnost [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce. Entita odpovídá řádku v tabulce.

Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Projděte si metodu `Up`.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Projděte si metodu `Up`.

---

Příkaz `Add-Migration` generuje kód pro vytvoření počátečního schématu databáze. Schéma je založené na modelu určeném v `RazorPagesMovieContext` (v souboru *data/RazorPagesMovieContext. cs* ). K pojmenování migrace se používá argument `Initial`. Můžete použít libovolný název, ale podle konvence je použit název, který popisuje migraci. Další informace najdete v tématu <xref:data/ef-mvc/migrations>.

Příkaz `Update-Database` spustí metodu `Up` v souboru *migrations/{Time-razítk} _InitialCreate. cs* , ve kterém se vytvoří databáze.

<a name="test"></a>

### <a name="test-the-app"></a>Testování aplikace

* Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).

Pokud se zobrazí chyba:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Nezmeškali jste [Krok migrace](#pmc).

* Otestujte odkaz pro **Vytvoření** .

  ![Vytvořit stránku](model/_static/conan.png)

  > [!NOTE]
  > V poli `Price` možná nebudete moct zadat desítkové čárky. Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální. Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Otestujte odkazy **Upravit**, **Podrobnosti**a **Odstranit** .

Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí:](xref:tutorials/razor-pages/razor-pages-start)začátek 
> [Další: vygenerované Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

V této části jsou přidány třídy pro správu filmů v databázi. Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází. EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.

Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core. Definují vlastnosti dat, která jsou uložena v databázi.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Přidání datového modelu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **přidat** **novou složku** > . Pojmenujte *modely*složek.

Klikněte pravým tlačítkem na složku *modely* . Vyberte **Přidat** **třídu** > . Pojmenujte **film**třídy.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *modely*.
* Přidejte třídu do složky *modely* s názvem *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **přidat** **novou složku** > . Pojmenujte *modely*složek.
* Klikněte pravým tlačítkem na složku *modely* a pak vyberte **přidat** **nový soubor**>.
* V dialogovém okně **nový soubor** :

  * V levém podokně vyberte **Obecné** .
  * V prostředním podokně vyberte **prázdnou třídu** .
  * Pojmenujte **film** třídy a vyberte **Nový**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.

## <a name="scaffold-the-movie-model"></a>Generování uživatelského rozhraní modelu filmů

V této části je model filmu vygenerovaný. To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvořit *stránky nebo složky filmů* :

* Klikněte pravým tlačítkem na složku *stránky* > **přidat** **novou složku**>.
* Pojmenovat *filmy* ve složce

Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** **novou vygenerované položky**>.

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .
* V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a přijměte vygenerovaný název **RazorPagesMovie. Models. RazorPagesMovieContext**.
* Vyberte **Přidat**.

![Obrázek z předchozích instrukcí.](model/_static/arp.png)

Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).
* Nainstalujte nástroj pro generování uživatelského rozhraní:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Pro Windows**: spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pro MacOS a Linux**: spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).
* Nainstalujte nástroj pro generování uživatelského rozhraní:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:

### <a name="files-created"></a>Vytvořené soubory

* *Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.
* *Data/RazorPagesMovieContext. cs*

### <a name="file-updated"></a>Soubor aktualizován

* *Startup.cs*

Vytvořené a aktualizované soubory jsou vysvětleny v další části.

<a name="pmc"></a>

## <a name="initial-migration"></a>Počáteční migrace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V této části se používá konzola správce balíčků (PMC) k těmto akcím:

* Přidejte počáteční migraci.
* Aktualizujte databázi pomocí prvotní migrace.

V nabídce **nástroje** vyberte **správce balíčků NuGet** > **Konzola správce balíčků**.

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

Do PMC zadejte následující příkazy:

```Powershell
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.

Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.

Příkaz `ef migrations add InitialCreate` generuje kód pro vytvoření počátečního schématu databáze. Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ). K pojmenování migrace se používá argument `InitialCreate`. Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.

Příkaz `ef database update` spustí metodu `Up` v souboru *migrations/\<time-razítko > _InitialCreate. cs* . Metoda `Up` vytvoří databázi.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Kontrola kontextu zaregistrovaného vkládáním závislostí

ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace. Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.

Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.

Projděte si metodu `Startup.ConfigureServices`. Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

Souřadnice `RazorPagesMovieContext` EF Core funkce (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`. Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Předchozí kód vytvoří vlastnost [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce. Entita odpovídá řádku v tabulce.

Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Projděte si metodu `Up`.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Projděte si metodu `Up`.

---

Příkaz `Add-Migration` generuje kód pro vytvoření počátečního schématu databáze. Schéma je založené na modelu určeném v `RazorPagesMovieContext` (v souboru *data/RazorPagesMovieContext. cs* ). K pojmenování migrace se používá argument `Initial`. Můžete použít libovolný název, ale podle konvence je použit název, který popisuje migraci. Další informace najdete v tématu <xref:data/ef-mvc/migrations>.

Příkaz `Update-Database` spustí metodu `Up` v souboru *migrations/{Time-razítk} _InitialCreate. cs* , ve kterém se vytvoří databáze.

<a name="test"></a>

### <a name="test-the-app"></a>Testování aplikace

* Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).

Pokud se zobrazí chyba:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Nezmeškali jste [Krok migrace](#pmc).

* Otestujte odkaz pro **Vytvoření** .

  ![Vytvořit stránku](model/_static/conan.png)

  > [!NOTE]
  > V poli `Price` možná nebudete moct zadat desítkové čárky. Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální. Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Otestujte odkazy **Upravit**, **Podrobnosti**a **Odstranit** .

Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí:](xref:tutorials/razor-pages/razor-pages-start)začátek 
> [Další: vygenerované Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end
