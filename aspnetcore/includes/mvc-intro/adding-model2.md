---
ms.openlocfilehash: de093954fedc0fef1f945e881e1c7a6a24178bdb
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265411"
---
## <a name="add-initial-migration-and-update-the-database"></a>Přidat počáteční migraci a aktualizaci databáze

* Otevřete příkazový řádek a přejděte do adresáře projektu. (Adresáře, který obsahuje *Startup.cs* souboru).

* V příkazovém řádku spusťte následující příkazy:

  ```console
  dotnet restore
  dotnet ef migrations add Initial
  dotnet ef database update
  ```

  [.NET core](/dotnet/core/tools/index) je na více platforem implementace rozhraní .NET. Zde je, co dělat tyto příkazy:

  * [DotNet restore](/dotnet/core/tools/dotnet-restore): Stáhne balíčky NuGet, který je zadán v *.csproj* souboru.
  * `dotnet ef migrations add Initial` Spustí příkaz migrace Entity Framework .NET Core CLI a vytvoří počáteční migraci. Parametr po "Přidání" je název, který přiřadíte k migraci. Tady pojmenováváte migrace "Počáteční" protože se počáteční migraci databáze. Tato operace vytvoří *Data/Migrations/\<datum a čas > _Initial.cs* soubor, který obsahuje příkazy migrace pro přidání *film* tabulky v databázi.
  * `dotnet ef database update`  Aktualizuje databázi pomocí migrace, kterou jsme právě vytvořili.

V dalším kurzu získáte informace o databázi a připojovací řetězec. Získáte informace o změn datových modelů v [přidat pole](xref:tutorials/first-mvc-app/new-field) kurzu.
