---
title: Přidání modelu do aplikace v ASP.NET Core Razor Pages
author: rick-anderson
description: Objevte, jak přidat třídy pro správu filmy v databázi pomocí Entity Framework Core (jádro EF).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: d2f9a64c77d76702004b94cdf36e558b33d7e19a
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172577"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Přidání modelu do aplikace v ASP.NET Core Razor Pages

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy. Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite. Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází. EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.

Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core. Definují vlastnosti data, která jsou uložena v databázi.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Přidání datového modelu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **Přidat** > **novou složku**. Pojmenujte *modely*složek.

Klikněte pravým tlačítkem na složku *modely* . Vyberte **přidat** > **třídy**. Pojmenujte **film**třídy.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *modely*.
* Přidejte třídu do složky *modely* s názvem *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V Oblast řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **Přidat** > **Nová složka...** . Pojmenujte *modely*složek.
* Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor...** .
* V dialogovém okně **nový soubor** :

  * V levém podokně vyberte **Obecné** .
  * V prostředním podokně vyberte **prázdnou třídu** .
  * Pojmenujte **film** třídy a vyberte **Nový**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.

## <a name="scaffold-the-movie-model"></a>Vygenerované uživatelské rozhraní Video modelu

V této části je automaticky generovaný model video. To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvořit *stránky nebo složky filmů* :

* Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.
* Pojmenovat *filmy* ve složce

Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **Nová vygenerovaná položka**.

![Image z předchozích kroků.](model/_static/sca.png)

V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.

![Image z předchozích kroků.](model/_static/add_scaffold.png)

Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :

* V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .
* V řádku **Třída kontextu dat** vyberte symbol **+** (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext. [Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje. Vytvoří třídu kontextu databáze se správným oborem názvů.
* Vyberte **Přidat**.

![Image z předchozích kroků.](model/_static/3/arp.png)

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
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vytvořit *stránky nebo složky filmů* :

* Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.
* Pojmenovat *filmy* ve složce

Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **nové generování uživatelského rozhraní...** .

![Image z předchozích kroků.](model/_static/scaMac.png)

V dialogovém okně **nové generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Další**.

![Image z předchozích kroků.](model/_static/add_scaffoldMac.png)

Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :

* V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video (RazorPagesMovie. Models)** .
* Do řádku **Třída kontextu dat** zadejte název nové třídy, RazorPagesMovie. **Data**. RazorPagesMovieContext. [Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje. Vytvoří třídu kontextu databáze se správným oborem názvů.
* Vyberte **Přidat**.

![Image z předchozích kroků.](model/_static/arpMac.png)

Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.

### <a name="add-ef-tools"></a>Přidat nástroje EF

Spusťte následující příkaz .NET Core CLI:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Předchozí příkaz přidá nástroje Entity Framework Core pro .NET Core CLI.

---

### <a name="files-created"></a>Soubory vytvořené

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:

* *Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.
* *Data/RazorPagesMovieContext. cs*

### <a name="updated"></a>Datum aktualizace

* *Startup.cs*

Vytvořený a aktualizované soubory jsou vysvětlené v následující části.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:

* *Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.
* *Data/RazorPagesMovieContext. cs*

### <a name="updated"></a>Datum aktualizace

* *Startup.cs*

Vytvořený a aktualizované soubory jsou vysvětlené v následující části.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Proces generování uživatelského rozhraní vytvoří následující soubory:

* *Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.

Vytvořené soubory jsou vysvětleny v další části.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Počáteční migraci

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V této části konzoly Správce balíčků (PMC) umožňuje:

* Přidáte počáteční migraci.
* Aktualizujte počáteční migraci databáze.

V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků**.

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

V konzole PMC zadejte následující příkazy:

```powershell
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

Příkaz migrace generuje kód pro vytvoření počátečního schématu databáze. Schéma je založené na modelu určeném v `DbContext`. Argument `InitialCreate` slouží k pojmenování migrace. Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.

Příkaz `update` spustí metodu `Up` v migracích, které nebyly aplikovány. V takovém případě `update` spustí metodu `Up` v části *migrace/\<časového razítka > souboru _InitialCreate. cs* , který vytvoří databázi.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Prozkoumání kontextu registrovaný pomocí vkládání závislostí

ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace. Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru. Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.

Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.

Projděte si metodu `Startup.ConfigureServices`. Zvýrazněný řádek byl přidán modulem scaffolder:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext` koordinuje funkce EF Core (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`. Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Předchozí kód vytvoří vlastnost [negenerickými\<Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky. Entita odpovídající řádek v tabulce.

Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Projděte si metodu `Up`.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Projděte si metodu `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Otestování aplikace

* Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).

Pokud dojde k chybě:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Nezmeškali jste [Krok migrace](#pmc).

* Otestujte odkaz pro **Vytvoření** .

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > V poli `Price` možná nebudete moct zadat desítkové čárky. Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální. Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.

V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: začátek](xref:tutorials/razor-pages/razor-pages-start)
> [Next: vygenerované Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy. Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite. Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází. EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.

Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core. Definují vlastnosti data, která jsou uložena v databázi.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Přidání datového modelu

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **Přidat** > **novou složku**. Pojmenujte *modely*složek.

Klikněte pravým tlačítkem na složku *modely* . Vyberte **přidat** > **třídy**. Pojmenujte **film**třídy.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *modely*.
* Přidejte třídu do složky *modely* s názvem *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **Přidat** > **Nová složka**. Pojmenujte *modely*složek.
* Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.
* V dialogovém okně **nový soubor** :

  * V levém podokně vyberte **Obecné** .
  * V prostředním podokně vyberte **prázdnou třídu** .
  * Pojmenujte **film** třídy a vyberte **Nový**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.

## <a name="scaffold-the-movie-model"></a>Vygenerované uživatelské rozhraní Video modelu

V této části je automaticky generovaný model video. To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvořit *stránky nebo složky filmů* :

* Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.
* Pojmenovat *filmy* ve složce

Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **Nová vygenerovaná položka**.

![Image z předchozích kroků.](model/_static/sca.png)

V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.

![Image z předchozích kroků.](model/_static/add_scaffold.png)

Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .
* V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a přijměte vygenerovaný název **RazorPagesMovie. Models. RazorPagesMovieContext**.
* Vyberte **Přidat**.

![Image z předchozích kroků.](model/_static/arp.png)

Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

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

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vytvořit *stránky nebo složky filmů* :

* Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.
* Pojmenovat *filmy* ve složce

Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **Nová vygenerovaná položka**.

![Image z předchozích kroků.](model/_static/scaMac.png)

V dialogovém okně **Přidat nové uživatelské rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.

![Image z předchozích kroků.](model/_static/add_scaffoldMac.png)

Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :

* V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video**.
* V řádku **třídy kontextu dat** zadejte vyberte **RazorPagesMovieContext** . tím se vytvoří nová třída kontextu databáze se správným oborem názvů. V takovém případě bude **RazorPagesMovie. Models. RazorPagesMovieContext**.
* Vyberte **Přidat**.

![Image z předchozích kroků.](model/_static/arpMac.png)

Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.

---

Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:

### <a name="files-created"></a>Soubory vytvořené

* *Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.
* *Data/RazorPagesMovieContext. cs*

### <a name="file-updated"></a>Aktualizovat soubor

* *Startup.cs*

Vytvořený a aktualizované soubory jsou vysvětlené v následující části.

<a name="pmc"></a>

## <a name="initial-migration"></a>Počáteční migraci

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V této části konzoly Správce balíčků (PMC) umožňuje:

* Přidáte počáteční migraci.
* Aktualizujte počáteční migraci databáze.

V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků**.

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

V konzole PMC zadejte následující příkazy:

```powershell
Add-Migration Initial
Update-Database
```

Příkaz `Add-Migration` generuje kód pro vytvoření počátečního schématu databáze. Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ). K pojmenování migrace se používá argument `InitialCreate`. Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít. Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.

Příkaz `Update-Database` spustí metodu `Up` v souboru s *časovou známkou Migration/\<> _InitialCreate. cs* . Metoda `Up` vytvoří databázi.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> Předchozí příkazy generují následující upozornění:*pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.* Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Prozkoumání kontextu registrovaný pomocí vkládání závislostí

ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace. Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru. Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.

Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.

Projděte si metodu `Startup.ConfigureServices`. Zvýrazněný řádek byl přidán modulem scaffolder:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext` koordinuje funkce EF Core (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`. Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Předchozí kód vytvoří vlastnost [negenerickými\<Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky. Entita odpovídající řádek v tabulce.

Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) . Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Projděte si metodu `Up`.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Projděte si metodu `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Otestování aplikace

* Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).

Pokud dojde k chybě:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Nezmeškali jste [Krok migrace](#pmc).

* Otestujte odkaz pro **Vytvoření** .

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > V poli `Price` možná nebudete moct zadat desítkové čárky. Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální. Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.

V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: začátek](xref:tutorials/razor-pages/razor-pages-start)
> [Next: vygenerované Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end
