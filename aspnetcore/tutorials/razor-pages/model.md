---
title: Část 2 – Přidání modelu do Razor aplikace stránky v ASP.NET Core
author: rick-anderson
description: Část 2 série kurzů na Razor stránkách.
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6b50f46863a6dabb01bcf0976a42abb504e6f7b7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020454"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>Část 2 – Přidání modelu do Razor aplikace stránky v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

V této části jsou přidány třídy pro správu filmů. Třídy modelu aplikace používají [Entity Framework Core (EF Core)](/ef/core) pro práci s databází. EF Core je objektově-relační Mapovač (O/RM), který zjednodušuje přístup k datům.

Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core. Definují vlastnosti dat, která jsou uložena v databázi.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Přidání datového modelu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klikněte pravým tlačítkem na projekt ** Razor PagesMovie** > **Přidat**  >  **novou složku**. Pojmenujte *modely*složek.

Klikněte pravým tlačítkem na složku *modely* . Vyberte **Přidat**  >  **třídu**. Pojmenujte **film**třídy.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *modely*.
* Přidejte třídu do složky *modely* s názvem *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V oblast řešení klikněte pravým tlačítkem na projekt ** Razor PagesMovie** a pak vyberte **Přidat** > **novou složku...**. Pojmenujte *modely*složek.
* Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor...**.
* V dialogovém okně **nový soubor** :

  * V levém podokně vyberte **Obecné** .
  * V prostředním podokně vyberte **prázdnou třídu** .
  * Pojmenujte **film** třídy a vyberte **Nový**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.

## <a name="scaffold-the-movie-model"></a>Generování uživatelského rozhraní modelu filmů

V této části je model filmu vygenerovaný. To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvořit *stránky nebo složky filmů* :

* Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.
* Pojmenovat *filmy* ve složce

Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte ** Razor stránky pomocí Entity Framework (CRUD)** > **Přidat**.

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

Dokončete dialog **Přidat Razor stránky pomocí Entity Framework (CRUD)** :

* V rozevíracím seznamu **třída modelu** vyberte **video ( Razor PagesMovie. Models)**.
* V řádku **třídy kontextu dat** vyberte **+** znaménko (plus) a změňte vygenerovaný název z Razor PagesMovie.** Modely**. Razor PagesMovieContext na Razor PagesMovie.** Data**. Razor PagesMovieContext. [Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje. Vytvoří třídu kontextu databáze se správným oborem názvů.
* Vyberte **Přidat**.

![Obrázek z předchozích instrukcí.](model/_static/3/arp.png)

*appsettings.jsv* souboru se aktualizuje pomocí připojovacího řetězce použitého pro připojení k místní databázi.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

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
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vytvořit *stránky nebo složky filmů* :

* Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.
* Pojmenovat *filmy* ve složce

Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **nové generování uživatelského rozhraní...**.

![Obrázek z předchozích instrukcí.](model/_static/scaMac.png)

V dialogovém okně **nové generování uživatelského rozhraní** vyberte možnost ** Razor stránky pomocí Entity Framework (CRUD)** > **Další**.

![Obrázek z předchozích instrukcí.](model/_static/add_scaffoldMac.png)

Dokončete dialog **Přidat Razor stránky pomocí Entity Framework (CRUD)** :

* V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video ( Razor PagesMovie. Models)**.
* Do řádku **Třída kontextu dat** zadejte název nové třídy, Razor PagesMovie.** Data**. Razor PagesMovieContext. [Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje. Vytvoří třídu kontextu databáze se správným oborem názvů.
* Vyberte **Přidat**.

![Obrázek z předchozích instrukcí.](model/_static/arpMac.png)

*appsettings.jsv* souboru se aktualizuje pomocí připojovacího řetězce použitého pro připojení k místní databázi.

### <a name="add-ef-tools"></a>Přidat nástroje EF

Spusťte následující příkaz .NET Core CLI:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Předchozí příkaz přidá nástroje Entity Framework Core pro .NET Core CLI.

---

### <a name="files-created"></a>Vytvořené soubory

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:

* *Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.
* *Data/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Aktualizováno

* *Startup.cs*

Vytvořené a aktualizované soubory jsou vysvětleny v další části.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:

* *Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.
* *Data/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Aktualizováno

* *Startup.cs*

Vytvořené a aktualizované soubory jsou vysvětleny v další části.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Proces generování uživatelského rozhraní vytvoří následující soubory:

* *Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.

Vytvořené soubory jsou vysvětleny v další části.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Počáteční migrace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V této části se používá konzola správce balíčků (PMC) k těmto akcím:

* Přidejte počáteční migraci.
* Aktualizujte databázi pomocí prvotní migrace.

V nabídce **nástroje** vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**.

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

Do PMC zadejte následující příkazy:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.

Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.

Příkaz migrace generuje kód pro vytvoření počátečního schématu databáze. Schéma je založené na modelu určeném v `DbContext` . `InitialCreate`Argument slouží k pojmenování migrace. Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.

`update`Příkaz spustí `Up` metodu v migracích, které nebyly aplikovány. V takovém případě `update` spustí `Up` metodu v souboru *migrations/ \<time-stamp> _InitialCreate. cs* , který vytvoří databázi.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Kontrola kontextu zaregistrovaného vkládáním závislostí

ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace. Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.

Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.

Projděte si `Startup.ConfigureServices` metodu. Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext`Koordinuje funkce EF Core (vytváření, čtení, aktualizace, odstranění atd.) pro `Movie` model. Kontext dat ( `RazorPagesMovieContext` ) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Předchozí kód vytvoří vlastnost [negenerickými \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce. Entita odpovídá řádku v tabulce.

Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Projděte si `Up` metodu.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Projděte si `Up` metodu.

---

<a name="test"></a>

### <a name="test-the-app"></a>Otestování aplikace

* Spusťte aplikaci a přidejte ji `/Movies` k adrese URL v prohlížeči ( `http://localhost:port/movies` ).

Pokud se zobrazí chyba:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Nezmeškali jste [Krok migrace](#pmc).

* Otestujte odkaz pro **Vytvoření** .

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > V poli možná nebudete moct zadat desítkové čárky `Price` . Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální. Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.

Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: začátek](xref:tutorials/razor-pages/razor-pages-start) 
>  [Další: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy. Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite. Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází. EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.

Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core. Definují vlastnosti dat, která jsou uložena v databázi.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Přidání datového modelu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klikněte pravým tlačítkem na projekt ** Razor PagesMovie** > **Přidat**  >  **novou složku**. Pojmenujte *modely*složek.

Klikněte pravým tlačítkem na složku *modely* . Vyberte **Přidat**  >  **třídu**. Pojmenujte **film**třídy.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *modely*.
* Přidejte třídu do složky *modely* s názvem *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V Průzkumník řešení klikněte pravým tlačítkem na projekt ** Razor PagesMovie** a pak vyberte **Přidat**  >  **novou složku**. Pojmenujte *modely*složek.
* Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.
* V dialogovém okně **nový soubor** :

  * V levém podokně vyberte **Obecné** .
  * V prostředním podokně vyberte **prázdnou třídu** .
  * Pojmenujte **film** třídy a vyberte **Nový**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.

## <a name="scaffold-the-movie-model"></a>Generování uživatelského rozhraní modelu filmů

V této části je model filmu vygenerovaný. To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvořit *stránky nebo složky filmů* :

* Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.
* Pojmenovat *filmy* ve složce

Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte ** Razor stránky pomocí Entity Framework (CRUD)** > **Přidat**.

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

Dokončete dialog **Přidat Razor stránky pomocí Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* V rozevíracím seznamu **třída modelu** vyberte **video ( Razor PagesMovie. Models)**.
* V řádku **Třída kontextu dat** vyberte **+** znaménko (plus) a přijměte vygenerovaný název ** Razor PagesMovie. Models. Razor PagesMovieContext**.
* Vyberte **Přidat**.

![Obrázek z předchozích instrukcí.](model/_static/arp.png)

*appsettings.jsv* souboru se aktualizuje pomocí připojovacího řetězce použitého pro připojení k místní databázi.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).

* **Pro Windows**: spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pro MacOS a Linux**: spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vytvořit *stránky nebo složky filmů* :

* Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.
* Pojmenovat *filmy* ve složce

Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.

![Obrázek z předchozích instrukcí.](model/_static/scaMac.png)

V dialogovém okně **Přidat nové generování uživatelského rozhraní** vyberte ** Razor stránky pomocí Entity Framework (CRUD)** > **Přidat**.

![Obrázek z předchozích instrukcí.](model/_static/add_scaffoldMac.png)

Dokončete dialog **Přidat Razor stránky pomocí Entity Framework (CRUD)** :

* V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video**.
* V řádku **třídy kontextu dat** zadejte vyberte ** Razor PagesMovieContext** . tím se vytvoří nová třída kontextu databáze se správným oborem názvů. V takovém případě bude ** Razor PagesMovie. Models. Razor PagesMovieContext**.
* Vyberte **Přidat**.

![Obrázek z předchozích instrukcí.](model/_static/arpMac.png)

*appsettings.jsv* souboru se aktualizuje pomocí připojovacího řetězce použitého pro připojení k místní databázi.

---

Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:

### <a name="files-created"></a>Vytvořené soubory

* *Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.
* *Data/ Razor PagesMovieContext.cs*

### <a name="file-updated"></a>Soubor aktualizován

* *Startup.cs*

Vytvořené a aktualizované soubory jsou vysvětleny v další části.

<a name="pmc"></a>

## <a name="initial-migration"></a>Počáteční migrace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V této části se používá konzola správce balíčků (PMC) k těmto akcím:

* Přidejte počáteční migraci.
* Aktualizujte databázi pomocí prvotní migrace.

V nabídce **nástroje** vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**.

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

Do PMC zadejte následující příkazy:

```powershell
Add-Migration Initial
Update-Database
```

`Add-Migration`Příkaz vygeneruje kód pro vytvoření počátečního schématu databáze. Schéma je založené na modelu určeném v `DbContext` (v souboru * Razor PagesMovieContext.cs* ). `InitialCreate`Argument slouží k pojmenování migrace. Můžete použít libovolný název, ale podle konvence je použit název, který popisuje migraci. Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.

`Update-Database`Příkaz spustí `Up` metodu v souboru *migrations/ \<time-stamp> _InitialCreate. cs* . `Up`Metoda vytvoří databázi.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> Předchozí příkazy generují následující upozornění:*pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.* Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Kontrola kontextu zaregistrovaného vkládáním závislostí

ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace. Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.

Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.

Projděte si `Startup.ConfigureServices` metodu. Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext`Koordinuje funkce EF Core (vytváření, čtení, aktualizace, odstranění atd.) pro `Movie` model. Kontext dat ( `RazorPagesMovieContext` ) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Předchozí kód vytvoří vlastnost [negenerickými \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce. Entita odpovídá řádku v tabulce.

Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Projděte si `Up` metodu.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Projděte si `Up` metodu.

---

<a name="test"></a>

### <a name="test-the-app"></a>Otestování aplikace

* Spusťte aplikaci a přidejte ji `/Movies` k adrese URL v prohlížeči ( `http://localhost:port/movies` ).

Pokud se zobrazí chyba:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Nezmeškali jste [Krok migrace](#pmc).

* Otestujte odkaz pro **Vytvoření** .

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > V poli možná nebudete moct zadat desítkové čárky `Price` . Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální. Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.

Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: začátek](xref:tutorials/razor-pages/razor-pages-start) 
>  [Další: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page)

::: moniker-end
