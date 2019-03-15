---
ms.openlocfilehash: 1f8d3913c83aaf5fe6ec2cec482a30f0f066c16b
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841690"
---

> [!NOTE]
> <span data-ttu-id="42f36-101">Pro účely tohoto kurzu můžete použít Entity Framework Core *migrace* funkce, kde je to možné.</span><span class="sxs-lookup"><span data-stu-id="42f36-101">For this tutorial you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="42f36-102">Migrace aktualizace schématu databáze, aby se shodují se změnami v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="42f36-102">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="42f36-103">Ale migrace lze provést pouze typy změn, které podporuje zprostředkovatel EF Core a jsou omezené možnosti zprostředkovatele SQLite.</span><span class="sxs-lookup"><span data-stu-id="42f36-103">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="42f36-104">Například přidáním sloupce se podporuje, ale odstranění nebo změna sloupec není podporován.</span><span class="sxs-lookup"><span data-stu-id="42f36-104">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="42f36-105">Pokud chcete odebrat nebo změnit sloupec, je vytvořen migrace `ef migrations add` úspěšný příkaz ale `ef database update` příkaz selže.</span><span class="sxs-lookup"><span data-stu-id="42f36-105">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="42f36-106">Vzhledem k těmto omezením v tomto kurzu nepoužívá migrace pro změny schématu SQLite.</span><span class="sxs-lookup"><span data-stu-id="42f36-106">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="42f36-107">Při změně schématu, místo toho vyřadit a znovu vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="42f36-107">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="42f36-108">Alternativní řešení pro omezení SQLite je ručně psát kód migrace tabulky sestavení, když se něco provést změny tabulky.</span><span class="sxs-lookup"><span data-stu-id="42f36-108">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="42f36-109">Tabulka opětovné sestavení zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="42f36-109">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="42f36-110">Přejmenování existující tabulce.</span><span class="sxs-lookup"><span data-stu-id="42f36-110">Renaming the existing table.</span></span>
>* <span data-ttu-id="42f36-111">Vytváří se nová tabulka.</span><span class="sxs-lookup"><span data-stu-id="42f36-111">Creating a new table.</span></span>
>* <span data-ttu-id="42f36-112">Kopírování dat z původní tabulky do nové tabulky.</span><span class="sxs-lookup"><span data-stu-id="42f36-112">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="42f36-113">Odstranit staré tabulky.</span><span class="sxs-lookup"><span data-stu-id="42f36-113">Dropping the old table.</span></span>
>
><span data-ttu-id="42f36-114">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="42f36-114">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="42f36-115">Omezení poskytovatele pro databázi SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="42f36-115">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="42f36-116">Přizpůsobení migrace kódu</span><span class="sxs-lookup"><span data-stu-id="42f36-116">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="42f36-117">Předvyplnění dat</span><span class="sxs-lookup"><span data-stu-id="42f36-117">Data seeding</span></span>](/ef/core/modeling/data-seeding)