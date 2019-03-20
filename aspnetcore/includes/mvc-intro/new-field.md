---
ms.openlocfilehash: 088f347cee73e745882455e6882d24a1e949f095
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265067"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a>Přidání nového pole do aplikace ASP.NET Core MVC

<!-- This include not used by windows version -->

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu přidáte nové pole do `Movies` tabulky. Vytvoříme vyřaďte databázi a vytvořte novou, když se změní schéma (přidání nového pole). Tento pracovní postup funguje dobře již v rané fázi při vývoji, když nemáme žádné produkční data a zachovat.

Jakmile je aplikace nasazená a máte data, která je potřeba zachovat, nelze vyřadit vaší databáze, pokud je třeba změnit schéma. Entity Framework [migrace Code First](/ef/core/get-started/aspnetcore/new-db) vám umožní aktualizovat schéma a migrovat databáze beze ztráty dat. Migrace je oblíbené funkce při použití SQL serveru, ale SQLite nepodporuje mnoho operací migrace schématu, tak jenom velmi jednoduše migrace jsou možné. Zobrazit [omezení SQLite](/ef/core/providers/sqlite/limitations) Další informace.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Přidání vlastnosti do hodnocení filmů modelu

Otevřít *Models/Movie.cs* a přidejte `Rating` vlastnost:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Models/MovieDateRating.cs?highlight=12&name=snippet)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

::: moniker-end

Protože jsme přidali nové pole do `Movie` třídy, je také potřeba aktualizovat na seznamu povolených elementů vazby tak tuto novou vlastnost budou zahrnuty. V *MoviesController.cs*, aktualizovat `[Bind]` atribut pro obě `Create` a `Edit` metody akce, které chcete zahrnout `Rating` vlastnost:

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Budete také potřebovat aktualizovat zobrazit šablony, aby bylo možné zobrazit, vytvářet a upravovat nové `Rating` vlastností v okně prohlížeče.

Upravit */Views/Movies/Index.cshtml* a přidejte `Rating` pole:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]

Aktualizace */Views/Movies/Create.cshtml* s `Rating` pole.

Aplikace nebude fungovat, dokud aktualizujeme DB zahrnout nové pole. Pokud jste ji nyní spustit, zobrazí se následující `SqliteException`:

```
SqliteException: SQLite Error 1: 'no such column: m.Rating'.
```

Tato chyba se zobrazuje, protože aktualizované třídy modelu film se liší od schématu tabulky Movie existující databáze. (Neexistuje žádný `Rating` sloupec v tabulce databáze.)

Řešení chyby několika způsoby:

1. Vyřaďte databázi a mít automaticky znovu vytvořit databázi založené na nové schéma třídy modelu Entity Framework. S tímto přístupem budete dojít ke ztrátě existujících dat v databázi, proto nelze provést s produkční databází. Použití inicializátoru automaticky naplnit databázi daty testu je často produktivní způsob, jak vyvíjet aplikace.

2. Ručně upravte schéma stávající databázi tak, aby odpovídalo tříd modelu. Výhodou tohoto přístupu je, že zachováte vaše data. Můžete tuto změnu provést buď ručně, nebo tak, že vytvoříte databázi změnit skript.

3. Pomocí migrace Code First aktualizovat schéma databáze.

V tomto kurzu vytvoříme vyřadit a znovu vytvořit databázi, když se změní schéma. Z terminálu vyřazení databáze spusťte následující příkaz:

`dotnet ef database drop`

Aktualizace `SeedData` třídy tak, že poskytuje hodnoty pro nový sloupec. Ukázka změnu je uveden níže, ale budete chtít tuto změnu pro každou `new Movie`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

Přidat `Rating` pole `Edit`, `Details`, a `Delete` zobrazení.

Spusťte aplikaci a ověřit, je možné vytvořit/upravit/zobrazit videa s `Rating` pole. šablony.
