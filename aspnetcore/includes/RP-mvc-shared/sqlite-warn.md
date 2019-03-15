---
ms.openlocfilehash: 1f8d3913c83aaf5fe6ec2cec482a30f0f066c16b
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841690"
---

> [!NOTE]
> Pro účely tohoto kurzu můžete použít Entity Framework Core *migrace* funkce, kde je to možné. Migrace aktualizace schématu databáze, aby se shodují se změnami v datovém modelu. Ale migrace lze provést pouze typy změn, které podporuje zprostředkovatel EF Core a jsou omezené možnosti zprostředkovatele SQLite. Například přidáním sloupce se podporuje, ale odstranění nebo změna sloupec není podporován. Pokud chcete odebrat nebo změnit sloupec, je vytvořen migrace `ef migrations add` úspěšný příkaz ale `ef database update` příkaz selže. Vzhledem k těmto omezením v tomto kurzu nepoužívá migrace pro změny schématu SQLite. Při změně schématu, místo toho vyřadit a znovu vytvořit databázi.
>
>Alternativní řešení pro omezení SQLite je ručně psát kód migrace tabulky sestavení, když se něco provést změny tabulky. Tabulka opětovné sestavení zahrnuje:
>
>* Přejmenování existující tabulce.
>* Vytváří se nová tabulka.
>* Kopírování dat z původní tabulky do nové tabulky.
>* Odstranit staré tabulky.
>
>Další informace naleznete v následujících materiálech:
>
> * [Omezení poskytovatele pro databázi SQLite EF Core](/ef/core/providers/sqlite/limitations)
> * [Přizpůsobení migrace kódu](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Předvyplnění dat](/ef/core/modeling/data-seeding)