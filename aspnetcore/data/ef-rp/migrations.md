---
title: Razor Stránky s EF core v ASP.NET core - Migrace - 4 z 8
author: rick-anderson
description: V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu v ASP.NET aplikaci Core MVC.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: 86fd83c898fce8e121e4d259aaca12c59591e606
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656532"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a>Razor Stránky s EF core v ASP.NET core - Migrace - 4 z 8

[Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), a Rick [Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Tento kurz představuje funkci migrace EF Core pro správu změn datového modelu.

Když je vyvinuta nová aplikace, datový model se často mění. Pokaždé, když se model změní, model se synchronizuje s databází. Tato série kurzů byla spuštěna konfigurací entity Framework k vytvoření databáze, pokud neexistuje. Pokaždé, když se změní datový model, budete muset vypustit databázi. Při příštím spuštění aplikace volání `EnsureCreated` znovu vytvoří databázi tak, aby odpovídala novému datovému modelu. Třída `DbInitializer` pak spustí osiva nové databáze.

Tento přístup k udržování databáze v synchronizaci s datovým modelem funguje dobře, dokud nenasadíte aplikaci do produkčního prostředí. Když je aplikace spuštěná v produkčním prostředí, obvykle ukládá data, která je potřeba udržovat. Aplikace nemůže začínat testovací databází pokaždé, když je provedena změna (například přidání nového sloupce). Funkce EF Core Migrations tento problém řeší povolením EF Core k aktualizaci schématu databáze namísto vytvoření nové databáze.

Místo uvolnění a opětovného vytvoření databáze při změně datového modelu migrace aktualizuje schéma a zachová existující data.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>Přetažení databáze

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pomocí **Průzkumníka objektů serveru SQL Server** (SSOX) odstraňte databázi nebo spusťte následující příkaz v konzole Správce **balíčků** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Spusťte na příkazovém řádku následující příkaz k instalaci příkazového řádku EF:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* V příkazovém řádku přejděte do složky projektu. Složka projektu obsahuje soubor *ContosoUniversity.csproj.*

* Odstraňte soubor *CU.db* nebo spusťte následující příkaz:

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>Vytvoření počáteční migrace

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Spusťte v pmc následující příkazy:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Zkontrolujte, zda je příkazový řádek ve složce projektu, a spusťte následující příkazy:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Metody nahoru a dolů

Příkaz EF `migrations add` Core generovaný kód k vytvoření databáze. Tento kód migrace je v souboru *\<časové razítko migrace>_InitialCreate.cs.* Metoda `Up` třídy `InitialCreate` vytvoří databázové tabulky, které odpovídají sady entit datového modelu. Metoda `Down` je odstraní, jak je znázorněno v následujícím příkladu:

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

Předchozí kód je pro počáteční migraci. Kód:

* Byl generován příkazem. `migrations add InitialCreate` 
* Je proveden příkazem. `database update`
* Vytvoří databázi pro datový model určený třídou kontextu databáze.

Parametr názvu migrace ("InitialCreate" v příkladu) se používá pro název souboru. Název migrace může být libovolný platný název souboru. Je nejlepší zvolit slovo nebo frázi, která shrnuje, co se v migraci děje. Například migrace, která přidala tabulku oddělení, může být nazvána "AddDepartmentTable".

## <a name="the-migrations-history-table"></a>Tabulka historie migrace

* Ke kontrole databáze použijte ssox nebo nástroj SQLite.
* Všimněte si `__EFMigrationsHistory` přidání tabulky. Tabulka `__EFMigrationsHistory` sleduje, které migrace byly použity do databáze.
* Zobrazení dat v `__EFMigrationsHistory` tabulce. Zobrazuje jeden řádek pro první migraci.

## <a name="the-data-model-snapshot"></a>Snímek datového modelu

Migrace vytvoří *snímek* aktuálního datového modelu v *migrations/SchoolContextModelSnapshot.cs*. Když přidáte migraci, EF určuje, co se změnilo porovnáním aktuálního datového modelu se souborem snímku.

Vzhledem k tomu, že soubor snímku sleduje stav datového modelu, `<timestamp>_<migrationname>.cs` nelze migraci odstranit odstraněním souboru. Chcete-li zálohovat poslední migraci, `migrations remove` musíte použít příkaz. Tento příkaz odstraní migraci a zajistí, že snímek je správně resetován. Další informace naleznete [v tématu dotnet ef migrace odebrat](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

## <a name="remove-ensurecreated"></a>Odebrat ensureCreated

Tato série kurzů `EnsureCreated`byla spuštěna pomocí aplikace . `EnsureCreated`nevytvoří tabulku historie migrace, a proto ji nelze s migrací použít. Je určen pro testování nebo rychlé prototypování, kde je databáze vynechána a často znovu vytvořena.

Od tohoto okamžiku budou kurzy používat migrace.

V *souboru Data/DBInitializer.cs*zakomentujte následující řádek:

```csharp
context.Database.EnsureCreated();
```
Spusťte aplikaci a ověřte, zda je databáze nasazena.

## <a name="applying-migrations-in-production"></a>Použití migrace ve výrobě

Doporučujeme, aby produkční aplikace **při** spuštění aplikace [nevolaly Database.Migrate.](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) `Migrate`by neměl být volán z aplikace, která je nasazena do serverové farmy. Pokud je aplikace škálována na více instancí serveru, je těžké zajistit, aby aktualizace schématu databáze nenastaly z více serverů nebo konflikt s přístupem pro čtení a zápis.

Migrace databáze by měla být provedena jako součást nasazení a řízeným způsobem. Přístupy migrace produkční databáze zahrnují:

* Použití migrace k vytvoření skriptů SQL a použití skriptů SQL v nasazení.
* Běží `dotnet ef database update` z řízeného prostředí.

## <a name="troubleshooting"></a>Řešení potíží

Pokud aplikace používá SQL Server LocalDB a zobrazí následující výjimku:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Řešením může být `dotnet ef database update` spuštění na příkazovém řádku.

### <a name="additional-resources"></a>Další zdroje

* [EF Core CLI](/ef/core/miscellaneous/cli/dotnet).
* [Konzola Správce balíčků (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>Další kroky

Další kurz vytvoří datový model a přidá vlastnosti entit a nové entity.

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/sort-filter-page)
> [Další kurz](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se používá funkce migrace EF Core pro správu změn datového modelu.

Pokud narazíte na problémy, které nelze vyřešit, stáhněte si [dokončenou aplikaci](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

Když je vyvinuta nová aplikace, datový model se často mění. Pokaždé, když se model změní, model se synchronizuje s databází. Tento kurz byl zahájen konfigurací entity framework k vytvoření databáze, pokud neexistuje. Pokaždé, když se změní datový model:

* DB je vynechána.
* EF vytvoří nový, který odpovídá modelu.
* Aplikace semena DB s testovací midata.

Tento přístup k udržování DB v synchronizaci s datovým modelem funguje dobře, dokud nenasadíte aplikaci do produkčního prostředí. Když je aplikace spuštěná v produkčním prostředí, obvykle ukládá data, která je potřeba udržovat. Aplikace nemůže začínat testovací db pokaždé, když je provedena změna (například přidání nového sloupce). Funkce EF Core Migrations tento problém řeší povolením EF Core k aktualizaci schématu DB namísto vytvoření nové databáze.

Místo uvolnění a opětovného vytvoření databáze při změně datového modelu migrace aktualizuje schéma a zachová existující data.

## <a name="drop-the-database"></a>Přetažení databáze

Použijte **Průzkumník objektů serveru SQL** Server `database drop` (SSOX) nebo příkaz:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V **konzole Správce balíčků** (PMC) spusťte následující příkaz:

```powershell
Drop-Database
```

Spusťte `Get-Help about_EntityFrameworkCore` z PMC a získejte informace nápovědy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Otevřete příkazové okno a přejděte do složky projektu. Složka projektu obsahuje *soubor Startup.cs.*

Do příkazového okna zadejte následující:

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>Vytvoření počáteční migrace a aktualizace databáze

Sestavte projekt a vytvořte první migraci.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a>Prozkoumejte metody nahoru a dolů

Příkaz EF `migrations add` Core generovaný kód pro vytvoření DB. Tento kód migrace je v souboru *\<časové razítko migrace>_InitialCreate.cs.* Metoda `Up` třídy `InitialCreate` vytvoří tabulky DB, které odpovídají množině entit datového modelu. Metoda `Down` je odstraní, jak je znázorněno v následujícím příkladu:

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

Migrace volá `Up` metodu k implementaci změn datového modelu pro migraci. Když zadáte příkaz pro vrácení aktualizace zpět, `Down` migrace volá metodu.

Předchozí kód je pro počáteční migraci. Tento kód byl `migrations add InitialCreate` vytvořen při spuštění příkazu. Parametr názvu migrace ("InitialCreate" v příkladu) se používá pro název souboru. Název migrace může být libovolný platný název souboru. Je nejlepší zvolit slovo nebo frázi, která shrnuje, co se v migraci děje. Například migrace, která přidala tabulku oddělení, může být nazvána "AddDepartmentTable".

Pokud je vytvořena počáteční migrace a db existuje:

* Je generován kód vytvoření DB.
* Kód vytvoření DB není nutné spustit, protože DB již odpovídá datovému modelu. Pokud je spuštěn kód vytvoření DB, neprovede žádné změny, protože DB již odpovídá datovému modelu.

Když je aplikace nasazena do nového prostředí, musí být kód vytvoření DB spuštěn, aby se vytvořila DATABÁZE.

Dříve DB byla vynechána a neexistuje, takže migrace vytvoří nové DB.

### <a name="the-data-model-snapshot"></a>Snímek datového modelu

Migrace vytvořit *snímek* aktuální schéma databáze v *migrace/SchoolContextModelSnapshot.cs*. Když přidáte migraci, EF určuje, co se změnilo porovnáním datového modelu se souborem snímku.

Chcete-li migraci odstranit, použijte následující příkaz:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Odebrat-migrace

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

Další informace naleznete [v tématu dotnet ef migrace odebrat](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

---

Příkaz Remove migrations odstraní migraci a zajistí správné obnovení snímku.

### <a name="remove-ensurecreated-and-test-the-app"></a>Odebrat EnsureCreated a otestovat aplikaci

Pro časný `EnsureCreated` vývoj byl použit. V tomto kurzu se používají migrace. `EnsureCreated`má následující omezení:

* Obchází migrace a vytvoří DB a schéma.
* Nevytvoří tabulku migrace.
* Nelze *not* použít s migrací.
* Je určen pro testování nebo rychlé prototypování, kde db je vynechána a znovu vytvořit často.

Odebrat `EnsureCreated`:

```csharp
context.Database.EnsureCreated();
```

Spusťte aplikaci a ověřte, že db je nasazena.

### <a name="inspect-the-database"></a>Kontrola databáze

Ke kontrole databáze použijte **Průzkumník objektů serveru SQL Server.** Všimněte si `__EFMigrationsHistory` přidání tabulky. Tabulka `__EFMigrationsHistory` sleduje, které migrace byly použity v db. Zobrazit data v `__EFMigrationsHistory` tabulce, zobrazí jeden řádek pro první migraci. Poslední protokol v předchozím příkladu výstupu příkazového řádku zobrazuje příkaz INSERT, který vytvoří tento řádek.

Spusťte aplikaci a ověřte, zda vše funguje.

## <a name="applying-migrations-in-production"></a>Použití migrace ve výrobě

Doporučujeme produkční aplikace **by nemělvolat** [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) při spuštění aplikace. `Migrate`by nemělbýt volána z aplikace v serverové farmě. Například pokud aplikace byla cloud nasazený s horizontálnínavýšení kapacity (více instancí aplikace běží).

Migrace databáze by měla být provedena jako součást nasazení a řízeným způsobem. Přístupy migrace produkční databáze zahrnují:

* Použití migrace k vytvoření skriptů SQL a použití skriptů SQL v nasazení.
* Běží `dotnet ef database update` z řízeného prostředí.

EF Core `__MigrationsHistory` používá tabulku, aby zjistil, jestli je třeba spustit všechny migrace. Pokud db je aktuální, je spuštěna žádná migrace.

## <a name="troubleshooting"></a>Řešení potíží

Stáhněte si [dokončenou aplikaci](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).

Aplikace generuje následující výjimku:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Řešení: Spustit`dotnet ef database update`

### <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [Rozhraní CLI jádra .NET](/ef/core/miscellaneous/cli/dotnet).
* [Konzola Správce balíčků (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/sort-filter-page)
> [další](xref:data/ef-rp/complex-data-model)

::: moniker-end

