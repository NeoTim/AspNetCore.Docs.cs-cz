---
ms.openlocfilehash: 4a569a3f7f804919b7fd6e481c2ec5a3acdfbbe8
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265529"
---
<a name="cli"></a>

## <a name="perform-initial-migration"></a>Provést počáteční migraci

Z příkazového řádku spusťte následující příkazy rozhraní příkazového řádku .NET Core:

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

`dotnet ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze. Schéma je založen na zadaném v modelu `DbContext` (v *Models/MvcMovieContext.cs* souboru). `Initial` Argument se používá k pojmenování migrace. Můžete použít libovolný název, ale podle konvence zvolte název, který popisuje migraci. Zobrazit [Úvod do migrace](xref:data/ef-mvc/migrations#introduction-to-migrations) Další informace.

`dotnet ef database update` Příkaz spustí `Up` metodu *migrace /\<časové razítko > _InitialCreate.cs* soubor, který vytvoří databázi.
