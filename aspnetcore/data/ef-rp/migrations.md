---
title: Razor Pages s EF Core v ASP.NET Core – migrace – 4 z 8
author: rick-anderson
description: V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu v aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: 73624f515e8089b5852864b60ec66ad79c7475c3
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914067"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a>Razor Pages s EF Core v ASP.NET Core – migrace – 4 z 8

Tím, že [Dykstra](https://github.com/tdykstra), [Jan P Smith](https://twitter.com/thereformedprog)a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se seznámíte s funkcí migrace EF Core pro správu změn datového modelu.

Při vývoji nové aplikace se datový model často mění. Pokaždé, když se model změní, model se nesynchronizuje s databází. Tato série kurzů začala konfigurací Entity Framework vytvořit databázi, pokud neexistuje. Pokaždé, když se datový model změní, je nutné odstranit databázi. Při příštím spuštění aplikace volá `EnsureCreated` opětovné vytvoření databáze, aby odpovídala novému datovému modelu. `DbInitializer` Třída se pak spustí a dosadí novou databázi.

Tento přístup k uchování databáze v synchronizaci s datovým modelem funguje dobře, dokud aplikaci nenainstalujete do produkčního prostředí. Když je aplikace spuštěná v produkčním prostředí, obvykle ukládá data, která je potřeba zachovat. Aplikace nemůže začít s testovací databází pokaždé, když je provedena změna (například přidání nového sloupce). Funkce migrace EF Core vyřeší tento problém tím, že umožňuje EF Core aktualizovat schéma databáze místo vytvoření nové databáze.

Místo vyřazení a opětovného vytvoření databáze při změně datového modelu migrace aktualizuje schéma a zachová stávající data.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>Odstranit databázi

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

K odstranění databáze použijte **Průzkumník objektů systému SQL Server** (SSOX) nebo spusťte následující příkaz v **konzole správce balíčků** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Spusťte následující příkaz na příkazovém řádku pro instalaci nástrojů EF CLI:

  ```console
  dotnet tool install --global dotnet-ef --version 3.0.0-*
  ```

* Na příkazovém řádku přejděte do složky projektu. Složka projektu obsahuje soubor *ContosoUniversity. csproj* .

* Odstraňte soubor *cu. DB* nebo spusťte následující příkaz:

  ```console
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>Vytvoření prvotní migrace

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V PMC spusťte následující příkazy:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ujistěte se, že je příkazový řádek ve složce projektu, a spusťte následující příkazy:

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Metody nahoru a dolů

Příkaz EF Core `migrations add` vygeneroval kód pro vytvoření databáze. Tento kód migrace se nachází v *\<časovém razítku migrace > souboru _InitialCreate. cs* . `Up` Metoda`InitialCreate` třídy vytvoří tabulky databáze, které odpovídají sadám entit datového modelu. `Down` Metoda je odstraní, jak je znázorněno v následujícím příkladu:

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

Předchozí kód je určen pro počáteční migraci. Kód:

* Bylo vygenerováno `migrations add InitialCreate` příkazem. 
* Se spustí `database update` příkazem.
* Vytvoří databázi pro datový model určený třídou kontextu databáze.

Pro název souboru se používá parametr názvu migrace (v příkladu "InitialCreate"). Název migrace může být libovolný platný název souboru. Nejlepší je zvolit slovo nebo frázi, která shrnuje, co se v migraci provádí. Například migrace, která přidala tabulku oddělení, se může jmenovat "AddDepartmentTable".

## <a name="the-migrations-history-table"></a>Tabulka historie migrace

* K prozkoumání databáze použijte SSOX nebo nástroj SQLite.
* Všimněte si přidání `__EFMigrationsHistory` tabulky. V `__EFMigrationsHistory` tabulce je sledováno, které migrace byly pro databázi aplikovány.
* Zobrazit data v `__EFMigrationsHistory` tabulce. Zobrazuje jeden řádek pro první migraci.

## <a name="the-data-model-snapshot"></a>Snímek datového modelu

Migrace vytvoří *snímek* aktuálního datového modelu v části *migrace/SchoolContextModelSnapshot. cs*. Když přidáte migraci, EF určí, co se změnilo, porovnáním s aktuálním datovým modelem se souborem snímku.

Vzhledem k tomu, že soubor snímku sleduje stav datového modelu, nelze migraci odstranit odstraněním `<timestamp>_<migrationname>.cs` souboru. Chcete-li zálohovat nejnovější migraci, je nutné použít `migrations remove` příkaz. Tento příkaz odstraní migraci a zajistí správné resetování snímku. Další informace najdete v tématu [Odebrání migrace pro dotnet EF](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

## <a name="remove-ensurecreated"></a>Odebrat EnsureCreated

Tato řada kurzů začala pomocí `EnsureCreated`. `EnsureCreated`nevytvoří tabulku historie migrace a nedá se použít s migracemi. Je navržená pro testování nebo rychlé vytváření prototypů, kde se databáze vynechává a často se znovu vytvoří.

Od tohoto okamžiku budou kurzy používat migrace.

Do pole *data/DBInitializer. cs*přidejte následující řádek:

```csharp
context.Database.EnsureCreated();
```
Spusťte aplikaci a ověřte, že je databáze osazená.

## <a name="applying-migrations-in-production"></a>Použití migrace v produkčním prostředí

Doporučujeme, aby provozní aplikace nevolaly funkci [Database. migrace](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) při spuštění aplikace. `Migrate`by se neměl volat z aplikace, která je nasazená na serverovou farmu. Pokud je aplikace škálovaná na více instancí serveru, je obtížné zajistit, aby aktualizace schématu databáze neprobíhaly na více serverech nebo byly v konfliktu s přístupem pro čtení a zápis.

Migrace databáze by se měla provádět v rámci nasazení a řízeným způsobem. Přístupy k migraci do produkční databáze zahrnují:

* Použití migrace k vytváření skriptů SQL a používání skriptů SQL v nasazení.
* Spuštění `dotnet ef database update` ze kontrolovaného prostředí.

## <a name="troubleshooting"></a>Poradce při potížích

Pokud aplikace používá SQL Server LocalDB a zobrazí následující výjimku:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Řešení může být spuštěno `dotnet ef database update` na příkazovém řádku.

### <a name="additional-resources"></a>Další zdroje

* [EF Core CLI](/ef/core/miscellaneous/cli/dotnet).
* [Konzola Správce balíčků (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>Další postup

V dalším kurzu se vytvoří datový model a přidají se vlastnosti entity a nové entity.

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/sort-filter-page)
> –[Další kurz](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se používá funkce migrace EF Core pro správu změn datového modelu.

Pokud narazíte na problémy, které nemůžete [vyřešit,](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)Stáhněte dokončenou aplikaci.

Při vývoji nové aplikace se datový model často mění. Pokaždé, když se model změní, model se nesynchronizuje s databází. Tento kurz se zahájil konfigurací Entity Framework k vytvoření databáze, pokud neexistuje. Pokaždé, když se změní datový model:

* DATABÁZE je vyřazena.
* EF vytvoří nový, který odpovídá modelu.
* Aplikace vysemena databáze s testovacími daty.

Tento přístup k uchování databáze v synchronizaci s datovým modelem funguje dobře, dokud aplikaci nenainstalujete do produkčního prostředí. Když je aplikace spuštěná v produkčním prostředí, obvykle ukládá data, která je potřeba zachovat. Aplikace nemůže začít s testovací databází pokaždé, když je provedena změna (například přidání nového sloupce). Funkce migrace EF Core vyřeší tento problém tím, že umožňuje EF Core aktualizovat schéma databáze místo vytvoření nové databáze.

Místo vyřazení a opětovného vytvoření databáze při změně datového modelu migrace aktualizuje schéma a zachová stávající data.

## <a name="drop-the-database"></a>Odstranit databázi

Použijte **Průzkumník objektů systému SQL Server** (SSOX) nebo `database drop` příkaz:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V **konzole správce balíčků** (PMC) spusťte následující příkaz:

```PMC
Drop-Database
```

Pokud `Get-Help about_EntityFrameworkCore` chcete získat informace o nápovědě, spusťte z PMC.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Otevřete příkazové okno a přejděte do složky projektu. Složka projektu obsahuje soubor *Startup.cs* .

V příkazovém okně zadejte následující:

 ```console
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>Vytvoření prvotní migrace a aktualizace databáze

Sestavte projekt a vytvořte první migraci.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a>Projděte si metody nahoru a dolů.

Příkaz EF Core `migrations add` vygeneroval kód pro vytvoření databáze. Tento kód migrace se nachází v *\<časovém razítku migrace > souboru _InitialCreate. cs* . `Up` Metoda`InitialCreate` třídy vytvoří tabulky databáze, které odpovídají sadám entit datového modelu. `Down` Metoda je odstraní, jak je znázorněno v následujícím příkladu:

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

Migrace volá `Up` metodu, která implementuje změny datového modelu pro migraci. Když zadáte příkaz pro vrácení aktualizace, migrace volá `Down` metodu.

Předchozí kód je určen pro počáteční migraci. Tento kód byl vytvořen při `migrations add InitialCreate` spuštění příkazu. Pro název souboru se používá parametr názvu migrace (v příkladu "InitialCreate"). Název migrace může být libovolný platný název souboru. Nejlepší je zvolit slovo nebo frázi, která shrnuje, co se v migraci provádí. Například migrace, která přidala tabulku oddělení, se může jmenovat "AddDepartmentTable".

Při vytvoření počáteční migrace a existence databáze:

* Generuje se kód pro vytvoření databáze.
* Kód pro vytvoření databáze není nutné spouštět, protože databáze již odpovídá datovému modelu. Pokud je kód pro vytvoření databáze spuštěn, neprovede žádné změny, protože databáze již odpovídá datovému modelu.

Když se aplikace nasadí do nového prostředí, musí se pro vytvoření databáze spustit kód pro vytvoření databáze.

Dříve byla databáze vyřazena a neexistuje, takže migrace vytvoří novou databázi.

### <a name="the-data-model-snapshot"></a>Snímek datového modelu

Migrace vytvoří *snímek* aktuálního schématu databáze v části *migrations/SchoolContextModelSnapshot. cs*. Když přidáte migraci, EF určí, co se změnilo, porovnáním datového modelu se souborem snímku.

K odstranění migrace použijte následující příkaz:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Odebrání – migrace

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```console
dotnet ef migrations remove
```

Další informace najdete v tématu [Odebrání migrace pro dotnet EF](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

---

Příkaz odebrat migrace odstraní migraci a zajistí správné resetování snímku.

### <a name="remove-ensurecreated-and-test-the-app"></a>Odebrání EnsureCreated a testování aplikace

Pro prvotní vývoj se `EnsureCreated` použil. V tomto kurzu se používají migrace. `EnsureCreated`má následující omezení:

* Vynechá migrace a vytvoří databázi a schéma.
* Nevytváří tabulku migrace.
* Nelze použít s migracemi.
* Je určený pro testování nebo rychlé vytváření prototypů, kde se databáze vynechává a často se znovu vytvoří.

Odebrat `EnsureCreated`:

```csharp
context.Database.EnsureCreated();
```

Spusťte aplikaci a ověřte, že je tato databáze osazená.

### <a name="inspect-the-database"></a>Kontrola databáze

K prozkoumání databáze použijte **Průzkumník objektů systému SQL Server** . Všimněte si přidání `__EFMigrationsHistory` tabulky. V `__EFMigrationsHistory` tabulce je sledováno, které migrace byly pro databázi aplikovány. Zobrazit data v `__EFMigrationsHistory` tabulce zobrazuje jeden řádek pro první migraci. Poslední přihlášení v předchozím příkladu výstupu CLI ukazuje příkaz INSERT, který tento řádek vytvoří.

Spusťte aplikaci a ověřte, že vše funguje.

## <a name="applying-migrations-in-production"></a>Použití migrace v produkčním prostředí

Doporučujeme, aby produkční aplikace nevolaly metodu [Database. migrace](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) při spuštění aplikace. `Migrate`by se neměl volat z aplikace v serverové farmě. Například pokud je aplikace nasazená v cloudu s možností škálování na více instancí (spouští se víc instancí aplikace).

Migrace databáze by se měla provádět v rámci nasazení a řízeným způsobem. Přístupy k migraci do produkční databáze zahrnují:

* Použití migrace k vytváření skriptů SQL a používání skriptů SQL v nasazení.
* Spuštění `dotnet ef database update` ze kontrolovaného prostředí.

EF Core používá `__MigrationsHistory` tabulku k zobrazení, jestli je potřeba spustit nějaké migrace. Pokud je databáze aktuální, nespustí se žádná migrace.

## <a name="troubleshooting"></a>Poradce při potížích

Stáhněte dokončenou aplikaci](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations). [

Aplikace vygeneruje následující výjimku:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Řešení Spustit `dotnet ef database update`

### <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).
* [Konzola Správce balíčků (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/sort-filter-page)Další
> [](xref:data/ef-rp/complex-data-model)

::: moniker-end

