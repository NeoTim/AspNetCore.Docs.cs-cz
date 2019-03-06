---
title: Stránky Razor s EF Core v ASP.NET Core – migrace - 4 z 8
author: rick-anderson
description: V tomto kurzu začnete používat funkci migrace EF Core ke správě změn datových modelů v aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 6/31/2017
uid: data/ef-rp/migrations
ms.openlocfilehash: ef79aca700068aadf9b5b2b689213b6e78a5d729
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346434"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a>Stránky Razor s EF Core v ASP.NET Core – migrace - 4 z 8

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

Podle [Petr Dykstra](https://github.com/tdykstra), [Jan Macek P](https://twitter.com/thereformedprog), a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

V tomto kurzu se používá funkce migrace EF Core ke správě změn datových modelů.

Pokud narazíte na potíže nelze vyřešit, stáhněte si [dokončené aplikace](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

Když se nová aplikace vyvíjí, model data často změny. Pokaždé, když změny modelu model získá synchronizován s databází. Tento kurz se tím, že konfigurace technologie Entity Framework pro vytvoření databáze, pokud neexistuje. Pokaždé, když datový model změny:

* Databáze je vyřazeno.
* EF vytvoří nový, který odpovídá modelu.
* Nasazení aplikace nasazuje databáze se testovací data.

Tento přístup k udržování databáze synchronizace s datovým modelem funguje dobře, dokud můžete aplikaci nasadit do produkčního prostředí. Když je aplikace spuštěna v produkčním prostředí, to je obvykle ukládání dat, která musí být zachovány. Aplikaci nelze spustit s testem DB pokaždé, když dojde ke změně (například přidá sloupec). Funkce migrace EF Core tento problém řeší tím, že EF Core aktualizovat schéma databáze místo vytvoření nové databáze.

Místo odstranění a opětovné vytvoření databáze při modelování dat změny, migrace aktualizuje schéma a uchovává existující data.

## <a name="drop-the-database"></a>Odpojení databáze

Použití **Průzkumník objektů systému SQL Server** (SSOX) nebo `database drop` příkaz:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V **Konzola správce balíčků** (PMC), spusťte následující příkaz:

```PMC
Drop-Database
```

Spustit `Get-Help about_EntityFrameworkCore` z konzole PMC zobrazíte nápovědu.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Otevřete okno příkazového řádku a přejděte do složky projektu. Obsahuje složky projektu *Startup.cs* souboru.

V příkazovém okně zadejte následující:

 ```console
 dotnet ef database drop
 ```

------

## <a name="create-an-initial-migration-and-update-the-db"></a>Vytvoření počáteční migraci a aktualizaci databáze

Sestavte projekt a vytvořit první migraci.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

------

### <a name="examine-the-up-and-down-methods"></a>Prozkoumání nahoru a dolů metody

EF Core `migrations add` příkaz vygeneruje kód pro vytvoření databáze. Tento kód migrace probíhá *migrace\<časové razítko > _InitialCreate.cs* souboru. `Up` Metodu `InitialCreate` třídy vytvoří tabulky databáze, které odpovídají sady entit datového modelu. `Down` Metoda odstraní, jak je znázorněno v následujícím příkladu:

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

Migrace volání `Up` metody k implementaci změn datových modelů pro migraci. Po zadání příkazu vrácení zpět aktualizace, volání migrace `Down` metody.

Předchozí kód je pro počáteční migraci. Tento kód byl vytvořen při `migrations add InitialCreate` jste příkaz spustili. Název parametru migrace ("InitialCreate" v příkladu) se používá pro název souboru. Název migrace může být libovolný platný název souboru. Doporučujeme vybrat slovo nebo slovní spojení, které shrnuje, co se provádí v migraci. Migraci, která byla přidána tabulka oddělení může například názvem "AddDepartmentTable."

Pokud počáteční migraci je vytvořen a existuje databáze:

* Generování kódu vytvoření databáze.
* Kód pro vytvoření databáze není nutné spustit, protože databáze již odpovídá datového modelu. Pokud je spuštěn kód pro vytvoření databáze, nebude provést změny, protože databáze již shoduje s datovým modelem.

Když je aplikace nasazena do nového prostředí, musí být spuštěn kód pro vytvoření databáze k vytvoření databáze.

Dříve databáze byla vyřazena a neexistuje, proto migrace vytvoří nová databáze.

### <a name="the-data-model-snapshot"></a>Snímek dat modelu

Vytvoření migrace *snímku* aktuální schéma databáze v *Migrations/SchoolContextModelSnapshot.cs*. Při přidání migrace EF Určuje, co se změnilo porovnáním datový model, který soubor snímku.

Pokud chcete odstranit migrace, použijte následující příkaz:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Remove migrace

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```console
dotnet ef migrations remove
```

Další informace najdete v tématu [migrace ef dotnet odebrat](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

------

Migrace příkaz remove odstraní migraci a zajistí, že je správně obnovit snímek.

### <a name="remove-ensurecreated-and-test-the-app"></a>Odebrat EnsureCreated a testování aplikace

Pro vývoj v rané fázi `EnsureCreated` byl použit. V tomto kurzu se používají migrace. `EnsureCreated` má následující omezení:

* Obchází migrace a vytvoří databáze a schéma.
* Nelze vytvořit tabulku migrace.
* Můžete *není* použít s migrací.
* Je určená pro testování nebo rychlé vytváření prototypů ve kterém je databáze vyřadit a znovu vytvořit často.

Odebrat následující řádek ze `DbInitializer`:

```csharp
context.Database.EnsureCreated();
```

Spusťte aplikaci a ověřte, že databáze je nasazený.

### <a name="inspect-the-database"></a>Zkontrolovat databázi

Použití **Průzkumník objektů systému SQL Server** ke kontrole databáze. Všimněte si, že přidání `__EFMigrationsHistory` tabulky. `__EFMigrationsHistory` Tabulka uchovává informace o migraci, které se použily k databázi. Zobrazení dat v `__EFMigrationsHistory` tabulky, zobrazuje jeden řádek pro první migraci. Poslední protokol v předchozím příkladu výstupu rozhraní příkazového řádku obsahuje, který vytváří tento řádek příkazu INSERT.

Spusťte aplikaci a ověřte, že vše funguje.

## <a name="applying-migrations-in-production"></a>Použití migrace v produkčním prostředí

Doporučujeme, abyste měli produkčních aplikací **není** volání [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) při spuštění aplikace. `Migrate` neměla být volána z aplikace v serverové farmě. Například, pokud tato aplikace je mimo cloud nasadili s horizontálním navýšením (běží více instancí aplikace).

Migrace databáze má počítat jako součást nasazení a řízené způsobem. Přístupy k migraci produkční databáze patří:

* Pomocí migrace vytvořit skripty SQL a pomocí skriptů SQL v nasazení.
* Spuštění `dotnet ef database update` v řízeném prostředí.

EF Core používá `__MigrationsHistory` tabulky zobrazíte, pokud žádné migrace, který je potřeba spustit. Pokud je aktuální databáze, migrace není spuštěn.

## <a name="troubleshooting"></a>Poradce při potížích

Stáhněte si [dokončené aplikace](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).

Aplikace generuje následující výjimku:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Řešení: Spustit `dotnet ef database update`

### <a name="additional-resources"></a>Další zdroje

* [Verzi tohoto kurzu na webu YouTube](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).
* [Konzola Správce balíčků (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

::: moniker-end

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/sort-filter-page)
> [další](xref:data/ef-rp/complex-data-model)
