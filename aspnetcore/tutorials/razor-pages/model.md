---
title: Přidání modelu do aplikace Razor Pages v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat třídy pro správu filmů v databázi pomocí entity framework core (EF Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7f7c2a09b74e6007ee3ea9c038398bac54988186
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488868"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Přidání modelu do aplikace Razor Pages v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

V této části jsou přidány třídy pro správu filmů. Třídy modelu aplikace používají [entity framework core (EF Core)](/ef/core) pro práci s databází. EF Core je objekt-relační mapovač (O/RM), který zjednodušuje přístup k datům.

Třídy modelu jsou označovány jako třídy POCO (z "prostý staré objekty CLR"), protože nemají žádnou závislost na EF Core. Definují vlastnosti dat, která jsou uložena v databázi.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Přidání datového modelu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** > **Přidat** > **novou složku**. Pojmenujte složku *Modely*.

Klikněte pravým tlačítkem myši na složku *Modely.* Vyberte **Přidat** > **třídu**. Pojmenujte třídu **Film**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *Modely*.
* Přidejte třídu do složky *Models* s názvem *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V panelu řešení klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** a pak vyberte **Přidat** > **novou složku...**. Pojmenujte složku *Modely*.
* Klikněte pravým tlačítkem myši na složku *Modely* a potom vyberte **Přidat** > **nový soubor...**.
* V dialogovém okně **Nový soubor:**

  * V levém podokně vyberte **Obecné.**
  * V prostředním podokně vyberte **Vyprázdnit třídu.**
  * Pojmenujte třídu **Film** a vyberte **Nový**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Sestavení projektu ověřte, že neexistují žádné chyby kompilace.

## <a name="scaffold-the-movie-model"></a>Lešení filmového modelu

V této části je filmový model scaffolded. To znamená, že nástroj lešení vytváří stránky pro operace Vytvořit, Číst, Aktualizovat a Odstranit (CRUD) pro filmový model.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvoření složky *Stránky a filmy:*

* Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.
* Pojmenujte složku *Filmy*

Klikněte pravým tlačítkem myši na složku *Stránky/filmy* > **Přidat** > **novou scaffolded položku**.

![Obrázek z předchozích pokynů.](model/_static/sca.png)

V dialogovém okně **Přidat pomocí okna Písmo šaten** vyberte **Razor Pages pomocí entity Framework (CRUD)** > **Add**.

![Obrázek z předchozích pokynů.](model/_static/add_scaffold.png)

Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**

* V rozevíracím seznamu **Třída Model** vyberte **Film (RazorPagesMovie.Models)**.
* V řádku **třídy kontext** **+** dat vyberte znaménko (plus) a změňte generovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext. [Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) není nutná. Vytvoří databázovou třídu kontextu se správným oborem názvů.
* Vyberte **Přidat**.

![Obrázek z předchozích pokynů.](model/_static/3/arp.png)

Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*
* Nainstalujte nástroj lešení:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Pro Windows**: Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pro macOS a Linux**: Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vytvoření složky *Stránky a filmy:*

* Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.
* Pojmenujte složku *Filmy*

Klikněte pravým tlačítkem myši na složku *Stránky/Filmy* > **Přidat** > **nové lešení...**.

![Obrázek z předchozích pokynů.](model/_static/scaMac.png)

V dialogovém okně **Nové lešení** vyberte **Razor Pages using Entity Framework (CRUD)** > **Next**.

![Obrázek z předchozích pokynů.](model/_static/add_scaffoldMac.png)

Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**

* V rozevíracím seznamu **Třídy Model** vyberte nebo zadejte **film (RazorPagesMovie.Models)**.
* Do řádku **třídy Kontext dat** zadejte název nové třídy RazorPagesMovie. **Data**. RazorPagesMovieContext. [Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) není nutná. Vytvoří databázovou třídu kontextu se správným oborem názvů.
* Vyberte **Přidat**.

![Obrázek z předchozích pokynů.](model/_static/arpMac.png)

Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.

### <a name="add-ef-tools"></a>Přidat nástroje EF

Spusťte následující příkaz CLI jádra .NET:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Předchozí příkaz přidá základní nástroje entity frameworku pro rozhraní příkazového příkazu .NET Core.

---

### <a name="files-created"></a>Vytvořené soubory

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Proces vytváření uživatelského příkazu vytvoří a aktualizuje následující soubory:

* *Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Aktualizováno

* *Startup.cs*

Vytvořené a aktualizované soubory jsou vysvětleny v další části.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Proces vytváření uživatelského příkazu vytvoří a aktualizuje následující soubory:

* *Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Aktualizováno

* *Startup.cs*

Vytvořené a aktualizované soubory jsou vysvětleny v další části.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Proces vytváření uživatelského příkazu vytvoří následující soubory:

* *Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.

Vytvořené soubory jsou vysvětleny v další části.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Počáteční migrace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V této části se konzola Správce balíčků (PMC) používá k:

* Přidejte počáteční migraci.
* Aktualizujte databázi počáteční migrací.

V nabídce **Nástroje** vyberte konzolu **Správce balíčků** **Aplikace NuGet .** >

  ![Nabídka PMC](../first-mvc-app/adding-model/_static/pmc.png)

Do pmc zadejte následující příkazy:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Předchozí příkazy generují následující upozornění: "Pro desítkový sloupec Cena nebyl zadán žádný typ. To způsobí, že hodnoty, které mají být tiše zkrácena, pokud se nevejdou do výchozí přesnosti a měřítka. Explicitně určete typ sloupce serveru SQL, který může pojmout všechny hodnoty pomocí 'HasColumnType()'."

Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.

Příkaz Migrace generuje kód pro vytvoření počátečního schématu databáze. Schéma je založeno na modelu zadaném `DbContext`v . Argument `InitialCreate` se používá k pojmenování migrace. Lze použít libovolný název, ale podle konvence je vybrán název, který popisuje migraci.

Příkaz `update` spustí `Up` metodu v migracích, které nebyly použity. V tomto `update` případě `Up` spustí metodu v *migraci /\<časové razítko>_InitialCreate.cs* souboru, který vytvoří databázi.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Prozkoumejte kontext registrovaný pomocí vkládání závislostí

ASP.NET Core je postaven s [vstřikování závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) jsou registrovány s vkládání závislostí při spuštění aplikace. Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu DB je zobrazen dále v kurzu.

Nástroj pro vytváření uživatelského funkce automaticky vytvořil kontext DB a zaregistroval jej do kontejneru vkládání závislostí.

Zkontrolujte `Startup.ConfigureServices` metodu. Zvýrazněný řádek byl přidán lešení:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

Souřadnice `RazorPagesMovieContext` EF core funkce (vytvořit, číst, aktualizovat, `Movie` odstranit, atd.) pro model. Kontext dat`RazorPagesMovieContext`( ) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje, které entity jsou zahrnuty do datového modelu.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Předchozí kód vytvoří [vlastnost\<DbSet Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. V terminologii entity framework sada entit obvykle odpovídá databázové tabulce. Entita odpovídá řádku v tabulce.

Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu. Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Zkontrolujte `Up` metodu.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Zkontrolujte `Up` metodu.

---

<a name="test"></a>

### <a name="test-the-app"></a>Otestování aplikace

* Spusťte aplikaci `/Movies` a připojte`http://localhost:port/movies`adresu URL v prohlížeči ( ).

Pokud se zobrazí chyba:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Jste vynechal [krok migrace](#pmc).

* Otestujte odkaz **Vytvořit.**

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > Je možné, že do pole nebudete moci `Price` zadat desetinná čárka. Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a pro formáty kalendářních dat mimo americkou angličtinu, musí být aplikace globalizovaná. Pokyny pro globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.

Další kurz vysvětluje soubory vytvořené pomocí lešení.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: Scaffolded Razor Stránky](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)napříč platformami . Aplikace vytvořené ze ASP.NET šablony Core používají databázi SQLite. Třídy modelu aplikace se používají s [entity Framework Core (EF Core)](/ef/core) [(SQLite EF Základní databáze zprostředkovatele)](/ef/core/providers/sqlite)pro práci s databází. EF Core je objekt-relační mapování (ORM) framework, který zjednodušuje přístup k datům.

Třídy modelu jsou označovány jako třídy POCO (z "prostý staré objekty CLR"), protože nemají žádnou závislost na EF Core. Definují vlastnosti dat, která jsou uložena v databázi.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Přidání datového modelu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** > **Přidat** > **novou složku**. Pojmenujte složku *Modely*.

Klikněte pravým tlačítkem myši na složku *Modely.* Vyberte **Přidat** > **třídu**. Pojmenujte třídu **Film**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Přidejte složku s názvem *Modely*.
* Přidejte třídu do složky *Models* s názvem *Movie.cs*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V Průzkumníku řešení klepněte pravým tlačítkem myši na projekt **RazorPagesMovie** a pak vyberte **Přidat** > **novou složku**. Pojmenujte složku *Modely*.
* Klikněte pravým tlačítkem myši na složku *Modely* a potom vyberte **Přidat** > **nový soubor**.
* V dialogovém okně **Nový soubor:**

  * V levém podokně vyberte **Obecné.**
  * V prostředním podokně vyberte **Vyprázdnit třídu.**
  * Pojmenujte třídu **Film** a vyberte **Nový**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Sestavení projektu ověřte, že neexistují žádné chyby kompilace.

## <a name="scaffold-the-movie-model"></a>Lešení filmového modelu

V této části je filmový model scaffolded. To znamená, že nástroj lešení vytváří stránky pro operace Vytvořit, Číst, Aktualizovat a Odstranit (CRUD) pro filmový model.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvoření složky *Stránky a filmy:*

* Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.
* Pojmenujte složku *Filmy*

Klikněte pravým tlačítkem myši na složku *Stránky/filmy* > **Přidat** > **novou scaffolded položku**.

![Obrázek z předchozích pokynů.](model/_static/sca.png)

V dialogovém okně **Přidat pomocí okna Písmo šaten** vyberte **Razor Pages pomocí entity Framework (CRUD)** > **Add**.

![Obrázek z předchozích pokynů.](model/_static/add_scaffold.png)

Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* V rozevíracím seznamu **Třída Model** vyberte **Film (RazorPagesMovie.Models)**.
* V řádku **třídy kontext** **+** dat vyberte znaménko (plus) a přijměte generovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Vyberte **Přidat**.

![Obrázek z předchozích pokynů.](model/_static/arp.png)

Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*

* **Pro Windows**: Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pro macOS a Linux**: Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vytvoření složky *Stránky a filmy:*

* Klikněte pravým tlačítkem myši na složku *Stránky* > **Přidat** > **novou složku**.
* Pojmenujte složku *Filmy*

Klikněte pravým tlačítkem myši na složku *Stránky/filmy* > **Přidat** > **novou scaffolded položku**.

![Obrázek z předchozích pokynů.](model/_static/scaMac.png)

V dialogovém okně **Přidat nové lešení** vyberte **Razor Pages using Entity Framework (CRUD)** > **Add**.

![Obrázek z předchozích pokynů.](model/_static/add_scaffoldMac.png)

Dokončete dialogové okno **Přidat žiletky pomocí entity frameworku (CRUD):**

* V rozevíracím seznamu **Třída Model** vyberte nebo zadejte **film**.
* Do řádku **třídy kontextu Data** zadejte **RazorPagesMovieContext,** který vytvoří novou třídu kontextu db se správným oborem názvů. V tomto případě to bude **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Vyberte **Přidat**.

![Obrázek z předchozích pokynů.](model/_static/arpMac.png)

Soubor *appsettings.json* je aktualizován pomocí připojovacího řetězce používaného k připojení k místní databázi.

---

Proces vytváření uživatelského příkazu vytvoří a aktualizuje následující soubory:

### <a name="files-created"></a>Vytvořené soubory

* *Stránky/filmy:* Vytvoření, odstranění, podrobnosti, úpravy a rejstřík.
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>Soubor byl aktualizován.

* *Startup.cs*

Vytvořené a aktualizované soubory jsou vysvětleny v další části.

<a name="pmc"></a>

## <a name="initial-migration"></a>Počáteční migrace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V této části se konzola Správce balíčků (PMC) používá k:

* Přidejte počáteční migraci.
* Aktualizujte databázi počáteční migrací.

V nabídce **Nástroje** vyberte konzolu **Správce balíčků** **Aplikace NuGet .** >

  ![Nabídka PMC](../first-mvc-app/adding-model/_static/pmc.png)

Do pmc zadejte následující příkazy:

```powershell
Add-Migration Initial
Update-Database
```

Příkaz `Add-Migration` generuje kód k vytvoření počátečního schématu databáze. Schéma je založeno na modelu zadaném `DbContext` v souboru (V *souboru RazorPagesMovieContext.cs).* Argument `InitialCreate` se používá k pojmenování migrace. Lze použít libovolný název, ale podle konvence se používá název, který popisuje migraci. Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.

Příkaz `Update-Database` spustí `Up` metodu v souboru *Migrace/\<časové razítko>_InitialCreate.cs.* Metoda `Up` vytvoří databázi.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> Předchozí příkazy generují následující upozornění: "*Pro desítkový sloupec Cena nebyl zadán žádný typ. To způsobí, že hodnoty, které mají být tiše zkrácena, pokud se nevejdou do výchozí přesnosti a měřítka. Explicitně určete typ sloupce serveru SQL, který může pojmout všechny hodnoty pomocí 'HasColumnType()'.*" Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Prozkoumejte kontext registrovaný pomocí vkládání závislostí

ASP.NET Core je postaven s [vstřikování závislostí](xref:fundamentals/dependency-injection). Služby (například kontext EF Core DB) jsou registrovány s vkládání závislostí při spuštění aplikace. Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru. Kód konstruktoru, který získá instanci kontextu DB je zobrazen dále v kurzu.

Nástroj pro vytváření uživatelského funkce automaticky vytvořil kontext DB a zaregistroval jej do kontejneru vkládání závislostí.

Zkontrolujte `Startup.ConfigureServices` metodu. Zvýrazněný řádek byl přidán lešení:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

Souřadnice `RazorPagesMovieContext` EF core funkce (vytvořit, číst, aktualizovat, `Movie` odstranit, atd.) pro model. Kontext dat`RazorPagesMovieContext`( ) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Kontext dat určuje, které entity jsou zahrnuty do datového modelu.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Předchozí kód vytvoří [vlastnost\<DbSet Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit. V terminologii entity framework sada entit obvykle odpovídá databázové tabulce. Entita odpovídá řádku v tabulce.

Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu. Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Zkontrolujte `Up` metodu.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Zkontrolujte `Up` metodu.

---

<a name="test"></a>

### <a name="test-the-app"></a>Otestování aplikace

* Spusťte aplikaci `/Movies` a připojte`http://localhost:port/movies`adresu URL v prohlížeči ( ).

Pokud se zobrazí chyba:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Jste vynechal [krok migrace](#pmc).

* Otestujte odkaz **Vytvořit.**

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > Je možné, že do pole nebudete moci `Price` zadat desetinná čárka. Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a pro formáty kalendářních dat mimo americkou angličtinu, musí být aplikace globalizovaná. Pokyny pro globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.

Další kurz vysvětluje soubory vytvořené pomocí lešení.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: Scaffolded Razor Stránky](xref:tutorials/razor-pages/page)

::: moniker-end
