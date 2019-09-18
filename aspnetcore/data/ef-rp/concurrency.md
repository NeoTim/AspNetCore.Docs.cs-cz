---
title: Stránky Razor s EF Core v ASP.NET Core - souběžnosti - 8 8
author: tdykstra
description: Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: c9cbf8fd3ed85f32b3c166bf2df702fd26df4fc3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080992"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a><span data-ttu-id="f2af8-103">Stránky Razor s EF Core v ASP.NET Core - souběžnosti - 8 8</span><span class="sxs-lookup"><span data-stu-id="f2af8-103">Razor Pages with EF Core in ASP.NET Core - Concurrency - 8 of 8</span></span>

<span data-ttu-id="f2af8-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Petr Dykstra](https://github.com/tdykstra), a [Jan Macek P](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="f2af8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f2af8-105">Tento kurz ukazuje, jak řešit konflikty při více uživateli aktualizovat entitu současně (ve stejnou dobu).</span><span class="sxs-lookup"><span data-stu-id="f2af8-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="f2af8-106">Konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="f2af8-106">Concurrency conflicts</span></span>

<span data-ttu-id="f2af8-107">Dojde ke konfliktu souběžnosti při:</span><span class="sxs-lookup"><span data-stu-id="f2af8-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="f2af8-108">Uživatel přejde na stránku upravit pro entitu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="f2af8-109">Jiný uživatel aktualizuje stejnou entitu před zapsáním změny prvního uživatele do databáze.</span><span class="sxs-lookup"><span data-stu-id="f2af8-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="f2af8-110">Pokud detekce souběžnosti není povolená, kdokoli aktualizuje databázi poslední přepsání změn provedených ostatními uživateli.</span><span class="sxs-lookup"><span data-stu-id="f2af8-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="f2af8-111">Pokud je toto riziko přijatelné, náklady na programování pro souběžnost můžou převážit výhody.</span><span class="sxs-lookup"><span data-stu-id="f2af8-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="f2af8-112">Pesimistická souběžnost (uzamykání)</span><span class="sxs-lookup"><span data-stu-id="f2af8-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="f2af8-113">Jedním ze způsobů, jak zabránit konfliktům souběžnosti, je použití zámků databáze.</span><span class="sxs-lookup"><span data-stu-id="f2af8-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="f2af8-114">Tato metoda se nazývá pesimistická souběžnost.</span><span class="sxs-lookup"><span data-stu-id="f2af8-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="f2af8-115">Předtím, než aplikace přečte řádek databáze, který je v úmyslu aktualizovat, si vyžádá zámek.</span><span class="sxs-lookup"><span data-stu-id="f2af8-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="f2af8-116">Když je řádek uzamčený pro přístup k aktualizacím, žádní jiní uživatelé nemůžou Uzamknout řádek, dokud se neuvolní první zámek.</span><span class="sxs-lookup"><span data-stu-id="f2af8-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="f2af8-117">Správa zámků má nevýhody.</span><span class="sxs-lookup"><span data-stu-id="f2af8-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="f2af8-118">Může být komplexní pro program a může způsobit problémy s výkonem při zvýšení počtu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="f2af8-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="f2af8-119">Entity Framework Core neposkytuje žádnou integrovanou podporu pro IT a v tomto kurzu se nezobrazuje, jak ho implementovat.</span><span class="sxs-lookup"><span data-stu-id="f2af8-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="f2af8-120">Optimistická souběžnost</span><span class="sxs-lookup"><span data-stu-id="f2af8-120">Optimistic concurrency</span></span>

<span data-ttu-id="f2af8-121">Optimistického řízení souběžnosti umožňuje konfliktů souběžnosti, která se provede, a potom reaguje správně při dělají.</span><span class="sxs-lookup"><span data-stu-id="f2af8-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="f2af8-122">Například Jana návštěv stránky pro úpravu oddělení a změny rozpočet anglické oddělení od $350,000.00 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="f2af8-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Změna rozpočtu na 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="f2af8-124">Předtím, než Jan klikne **Uložit**, Jan navštíví na stejnou stránku a změny pole Datum zahájení 9/1/2013 z 9/1/2007.</span><span class="sxs-lookup"><span data-stu-id="f2af8-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Změna počátečního data a 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="f2af8-126">Jana klikne na **Uložit** jako první a projeví se jeho změna, protože prohlížeč zobrazí stránku indexu s nulovou hodnotou rozpočtu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="f2af8-127">Jan klikne **Uložit** na stránce Upravit, která stále zobrazuje rozpočtu 350,000.00 $.</span><span class="sxs-lookup"><span data-stu-id="f2af8-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="f2af8-128">Co se stane dále, podle toho, jak zpracovávat konflikty souběžnosti:</span><span class="sxs-lookup"><span data-stu-id="f2af8-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="f2af8-129">Můžete sledovat, kterou vlastnost uživatel změnil, a aktualizovat pouze odpovídající sloupce v databázi.</span><span class="sxs-lookup"><span data-stu-id="f2af8-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="f2af8-130">Ve scénáři bude ztracena žádná data.</span><span class="sxs-lookup"><span data-stu-id="f2af8-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="f2af8-131">Různé vlastnosti byly aktualizovány dva uživatelé.</span><span class="sxs-lookup"><span data-stu-id="f2af8-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="f2af8-132">Při příštím někdo přejde z anglické oddělení, zobrazí se Jana a John's na změny.</span><span class="sxs-lookup"><span data-stu-id="f2af8-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="f2af8-133">Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě.</span><span class="sxs-lookup"><span data-stu-id="f2af8-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="f2af8-134">Tento přístup má některé nevýhody:</span><span class="sxs-lookup"><span data-stu-id="f2af8-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="f2af8-135">Nelze nedošlo ke ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f2af8-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="f2af8-136">Není obecně praktické ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f2af8-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="f2af8-137">Vyžaduje udržování významné stavu, aby bylo možné udržovat přehled o všech načtených hodnoty a nové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f2af8-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="f2af8-138">Zachování velké množství stavu může ovlivnit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="f2af8-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="f2af8-139">Můžete zvýšit složitost aplikace ve srovnání s detekce souběžnosti s entitou.</span><span class="sxs-lookup"><span data-stu-id="f2af8-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="f2af8-140">Můžete nechat John's na změnu Jana změna přepsána.</span><span class="sxs-lookup"><span data-stu-id="f2af8-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="f2af8-141">Při příštím někdo přejde z anglické oddělení, zobrazí se 9/1/2013 a počet získaných $350,000.00 hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="f2af8-142">Tento přístup se nazývá *Wins, klient* nebo *poslední ve službě Wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="f2af8-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="f2af8-143">(Všechny hodnoty z klienta přednost co je v úložišti.) Pokud tak učiníte nemusíte vytvářet kód pro zpracování souběžnosti, Wins, klient probíhá automaticky.</span><span class="sxs-lookup"><span data-stu-id="f2af8-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="f2af8-144">Můžete zabránit tomu, aby se změnila aktualizace od Jan v databázi.</span><span class="sxs-lookup"><span data-stu-id="f2af8-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="f2af8-145">Obvykle by aplikace:</span><span class="sxs-lookup"><span data-stu-id="f2af8-145">Typically, the app would:</span></span>

  * <span data-ttu-id="f2af8-146">Zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-146">Display an error message.</span></span>
  * <span data-ttu-id="f2af8-147">Zobrazit aktuální stav dat.</span><span class="sxs-lookup"><span data-stu-id="f2af8-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="f2af8-148">Povolit uživateli, který chcete znovu použít změny.</span><span class="sxs-lookup"><span data-stu-id="f2af8-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="f2af8-149">Tento postup se nazývá *Store Wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="f2af8-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="f2af8-150">(Hodnoty úložiště dat přednost hodnoty odeslány klientem.) Implementace služby Wins Store scénář v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="f2af8-151">Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění.</span><span class="sxs-lookup"><span data-stu-id="f2af8-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="f2af8-152">Zjišťování konfliktů v EF Core</span><span class="sxs-lookup"><span data-stu-id="f2af8-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="f2af8-153">EF Core vyvolá `DbConcurrencyException` výjimky, když detekuje konflikty.</span><span class="sxs-lookup"><span data-stu-id="f2af8-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="f2af8-154">Aby bylo možné povolit detekci konfliktů, musí být datový model nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="f2af8-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="f2af8-155">Mezi možnosti povolení detekce konfliktů patří následující:</span><span class="sxs-lookup"><span data-stu-id="f2af8-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="f2af8-156">Nakonfigurujte EF Core pro zahrnutí původních hodnot sloupců nakonfigurovaných jako [tokeny souběžnosti](/ef/core/modeling/concurrency) v klauzuli WHERE příkazů Update a DELETE.</span><span class="sxs-lookup"><span data-stu-id="f2af8-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="f2af8-157">Když `SaveChanges` je volána, klauzule WHERE hledá původní hodnoty všech vlastností pokomentovaných s atributem [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) .</span><span class="sxs-lookup"><span data-stu-id="f2af8-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="f2af8-158">Příkaz Update nenajde řádek, který se má aktualizovat, pokud se některá z vlastností tokenu souběžnosti změnila od prvního načtení řádku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="f2af8-159">EF Core interpretuje, že jako konflikt souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="f2af8-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="f2af8-160">U databázových tabulek, které mají mnoho sloupců, může tento přístup mít za následek velmi velké klauzule WHERE a může vyžadovat velké množství stavu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="f2af8-161">Proto se tento přístup obecně nedoporučuje a nejedná se o metodu používanou v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="f2af8-162">V tabulce databáze zahrňte sloupec sledování, který se dá použít k určení, kdy došlo ke změně řádku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="f2af8-163">V databázi SQL Server je `rowversion`datový typ sloupce sledování.</span><span class="sxs-lookup"><span data-stu-id="f2af8-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="f2af8-164">`rowversion` Hodnota je sekvenční číslo, které se zvýší pokaždé, když se řádek aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="f2af8-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="f2af8-165">V příkazu Update nebo DELETE zahrnuje klauzule WHERE původní hodnotu sloupce sledování (číslo verze původního řádku).</span><span class="sxs-lookup"><span data-stu-id="f2af8-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="f2af8-166">Pokud byl aktualizovaný řádek změněn jiným uživatelem, hodnota ve `rowversion` sloupci se liší od původní hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f2af8-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="f2af8-167">V takovém případě příkaz Update nebo DELETE nemůže najít řádek, který se má aktualizovat z klauzule WHERE.</span><span class="sxs-lookup"><span data-stu-id="f2af8-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="f2af8-168">EF Core vyvolá výjimku souběžnosti, pokud nejsou žádné řádky ovlivněny příkazem Update nebo DELETE.</span><span class="sxs-lookup"><span data-stu-id="f2af8-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="f2af8-169">Přidat vlastnost sledování</span><span class="sxs-lookup"><span data-stu-id="f2af8-169">Add a tracking property</span></span>

<span data-ttu-id="f2af8-170">V *Models/Department.cs*, přidání vlastnosti sledování do s názvem RowVersion:</span><span class="sxs-lookup"><span data-stu-id="f2af8-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="f2af8-171">Atribut [Timestamp (časové razítko](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) ) identifikuje sloupec jako sloupec sledování souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="f2af8-171">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="f2af8-172">Rozhraní Fluent API je alternativní způsob, jak určit vlastnost sledování:</span><span class="sxs-lookup"><span data-stu-id="f2af8-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f2af8-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2af8-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2af8-174">U databáze `[Timestamp]` SQL Server je atribut vlastnosti entity definovaný jako bajtové pole:</span><span class="sxs-lookup"><span data-stu-id="f2af8-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="f2af8-175">Způsobí, že bude sloupec zahrnut v klauzulích DELETE a UPDATE WHERE.</span><span class="sxs-lookup"><span data-stu-id="f2af8-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="f2af8-176">Nastaví typ sloupce v databázi na [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="f2af8-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="f2af8-177">Databáze generuje číslo verze sekvenčního řádku, které se zvýší pokaždé, když se řádek aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="f2af8-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="f2af8-178">`Delete` V příkazu `Update` nebozahrnujeklauzulehodnotunačtené`Where` verze řádku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="f2af8-179">Pokud se aktualizovaný řádek od načtení změnil:</span><span class="sxs-lookup"><span data-stu-id="f2af8-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="f2af8-180">Hodnota verze aktuálního řádku se neshoduje s načtenou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="f2af8-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="f2af8-181">Příkazy `Update` nebo `Delete` nenaleznouřádek,protožeklauzulevyhledáváhodnotunačtené`Where` verze řádku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="f2af8-182">A `DbUpdateConcurrencyException` je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="f2af8-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="f2af8-183">Následující kód ukazuje část generovaných EF Core, když se aktualizuje název oddělení T-SQL:</span><span class="sxs-lookup"><span data-stu-id="f2af8-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="f2af8-184">Předchozí zvýrazněný kód ukazuje `WHERE` obsahující klauzuli `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="f2af8-185">Pokud se databáze `RowVersion` neshoduje `RowVersion` s parametrem (`@p2`), žádné řádky se neaktualizují.</span><span class="sxs-lookup"><span data-stu-id="f2af8-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="f2af8-186">Následující zvýrazněný kód ukazuje T-SQL, která ověřuje, že byl aktualizován přesně jeden řádek:</span><span class="sxs-lookup"><span data-stu-id="f2af8-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="f2af8-187">[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) vrací počet řádků, které jsou ovlivněny poslední příkaz.</span><span class="sxs-lookup"><span data-stu-id="f2af8-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="f2af8-188">Pokud nejsou aktualizovány žádné řádky, EF Core vyvolá `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f2af8-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2af8-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f2af8-190">Pro databázi SQLite je `[Timestamp]` atribut vlastnosti entity definovaný jako bajtové pole:</span><span class="sxs-lookup"><span data-stu-id="f2af8-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="f2af8-191">Způsobí, že bude sloupec zahrnut v klauzulích DELETE a UPDATE WHERE.</span><span class="sxs-lookup"><span data-stu-id="f2af8-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="f2af8-192">Provede mapování na typ sloupce objektu BLOB.</span><span class="sxs-lookup"><span data-stu-id="f2af8-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="f2af8-193">Když se aktualizuje řádek, triggery databáze aktualizují sloupec RowVersion pomocí nového náhodného bajtového pole.</span><span class="sxs-lookup"><span data-stu-id="f2af8-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="f2af8-194">`Delete` V příkazu `Update` orzahrnujeklauzulenačtenou`Where` hodnotu sloupce rowversion.</span><span class="sxs-lookup"><span data-stu-id="f2af8-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="f2af8-195">Pokud se aktualizovaný řádek od načtení změnil:</span><span class="sxs-lookup"><span data-stu-id="f2af8-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="f2af8-196">Hodnota verze aktuálního řádku se neshoduje s načtenou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="f2af8-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="f2af8-197">Příkaz `Update` nebo `Delete` nenalezneřádek,protožeklauzulevyhledápůvodní`Where` hodnotu verze řádku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="f2af8-198">A `DbUpdateConcurrencyException` je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="f2af8-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="f2af8-199">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="f2af8-199">Update the database</span></span>

<span data-ttu-id="f2af8-200">`RowVersion` Přidáním vlastnosti se změní datový model, který vyžaduje migraci.</span><span class="sxs-lookup"><span data-stu-id="f2af8-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="f2af8-201">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="f2af8-201">Build the project.</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f2af8-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2af8-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2af8-203">V PMC spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f2af8-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f2af8-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2af8-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2af8-205">V terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f2af8-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="f2af8-206">Tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="f2af8-206">This command:</span></span>

* <span data-ttu-id="f2af8-207">Vytvoří migrační soubor *_RowVersion. cs migrace/{Time razítko}* .</span><span class="sxs-lookup"><span data-stu-id="f2af8-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="f2af8-208">Aktualizace *Migrations/SchoolContextModelSnapshot.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="f2af8-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="f2af8-209">Tato aktualizace přidává následující zvýrazněný kód do `BuildModel` metody:</span><span class="sxs-lookup"><span data-stu-id="f2af8-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f2af8-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2af8-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2af8-211">V PMC spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f2af8-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f2af8-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2af8-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2af8-213">`Migrations/<timestamp>_RowVersion.cs` Otevřete soubor a přidejte zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="f2af8-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="f2af8-214">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="f2af8-214">The preceding code:</span></span>

  * <span data-ttu-id="f2af8-215">Aktualizuje existující řádky s náhodnými hodnotami objektů BLOB.</span><span class="sxs-lookup"><span data-stu-id="f2af8-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="f2af8-216">Pokaždé, když se aktualizuje řádek, přidá aktivační procedury databáze, které nastaví sloupec RowVersion na náhodný hodnotu BLOB.</span><span class="sxs-lookup"><span data-stu-id="f2af8-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="f2af8-217">V terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f2af8-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="f2af8-218">Stránky oddělení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="f2af8-218">Scaffold Department pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f2af8-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2af8-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2af8-220">Postupujte podle pokynů v části [stránky pro studenty](xref:data/ef-rp/intro#scaffold-student-pages) s těmito výjimkami:</span><span class="sxs-lookup"><span data-stu-id="f2af8-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="f2af8-221">Vytvořte složku *stránky nebo oddělení* .</span><span class="sxs-lookup"><span data-stu-id="f2af8-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="f2af8-222">Použijte `Department` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="f2af8-223">Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f2af8-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2af8-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2af8-225">Vytvořte složku *stránky nebo oddělení* .</span><span class="sxs-lookup"><span data-stu-id="f2af8-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="f2af8-226">Spusťte následující příkaz pro generování uživatelského rozhraní stránek oddělení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="f2af8-227">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="f2af8-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="f2af8-228">**V systému Linux nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="f2af8-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="f2af8-229">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="f2af8-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="f2af8-230">Aktualizace stránky indexu</span><span class="sxs-lookup"><span data-stu-id="f2af8-230">Update the Index page</span></span>

<span data-ttu-id="f2af8-231">Nástroj pro generování uživatelského rozhraní vytvořil `RowVersion` sloupec pro stránku indexu, ale toto pole by se v produkční aplikaci nezobrazovalo.</span><span class="sxs-lookup"><span data-stu-id="f2af8-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="f2af8-232">V tomto kurzu se zobrazí poslední bajt `RowVersion` , který vám ukáže, jak funguje zpracování souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="f2af8-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="f2af8-233">Poslední bajt není zaručený jako jedinečný.</span><span class="sxs-lookup"><span data-stu-id="f2af8-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="f2af8-234">Aktualizovat stránku *Pages\Departments\Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f2af8-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="f2af8-235">Nahraďte indexem oddělení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="f2af8-236">Změňte kód obsahující `RowVersion` tak, aby zobrazoval pouze poslední bajt pole bajtů.</span><span class="sxs-lookup"><span data-stu-id="f2af8-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="f2af8-237">Nahraďte FirstMidName jméno a příjmení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="f2af8-238">Následující kód ukazuje aktualizovanou stránku:</span><span class="sxs-lookup"><span data-stu-id="f2af8-238">The following code shows the updated page:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="f2af8-239">Aktualizace modelu stránky úpravy</span><span class="sxs-lookup"><span data-stu-id="f2af8-239">Update the Edit page model</span></span>

<span data-ttu-id="f2af8-240">*Pages\Departments\Edit.cshtml.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f2af8-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="f2af8-241">[Původní](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) se aktualizuje `rowVersion` hodnotou z entity, když byla `OnGet` načtena v metodě.</span><span class="sxs-lookup"><span data-stu-id="f2af8-241">The [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="f2af8-242">EF Core generuje příkazu SQL UPDATE s klauzulí WHERE, který obsahuje původní `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="f2af8-243">Pokud žádné řádky jsou ovlivněny příkazu UPDATE (žádné řádky mít původní `RowVersion` hodnota), `DbUpdateConcurrencyException` je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="f2af8-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="f2af8-244">V předchozím zvýrazněném kódu:</span><span class="sxs-lookup"><span data-stu-id="f2af8-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="f2af8-245">Hodnota v `Department.RowVersion` poli je to, co bylo v entitě v okamžiku, kdy byla původně načtena v žádosti o získání stránky pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="f2af8-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="f2af8-246">Hodnota je poskytnuta `OnPost` metodě pomocí skrytého pole na stránce Razor, které zobrazuje entitu, kterou chcete upravit.</span><span class="sxs-lookup"><span data-stu-id="f2af8-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="f2af8-247">Hodnota skrytého pole je zkopírována `Department.RowVersion` do pořadače modelu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="f2af8-248">`OriginalValue`je to, co EF Core bude používat v klauzuli WHERE.</span><span class="sxs-lookup"><span data-stu-id="f2af8-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="f2af8-249">Předtím, než se spustí zvýrazněný řádek `OriginalValue` kódu, má hodnotu, která byla v `FirstOrDefaultAsync` databázi volána v této metodě, která se může lišit od toho, co bylo zobrazeno na stránce pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="f2af8-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="f2af8-250">Zvýrazněný kód zajišťuje, aby EF Core používal původní `RowVersion` hodnotu ze zobrazené `Department` entity v klauzuli WHERE příkazu SQL Update.</span><span class="sxs-lookup"><span data-stu-id="f2af8-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="f2af8-251">Když dojde k chybě souběžnosti, následující zvýrazněný kód Získá hodnoty klienta (hodnoty odeslané do této metody) a hodnoty databáze.</span><span class="sxs-lookup"><span data-stu-id="f2af8-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="f2af8-252">Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišné od odeslání do `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="f2af8-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="f2af8-253">Následující zvýrazněný kód nastaví `RowVersion` hodnotu na novou hodnotu načtenou z databáze.</span><span class="sxs-lookup"><span data-stu-id="f2af8-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="f2af8-254">Při příštím kliknutí na tlačítko **Uložit**, pouze souběžnosti chyby, ke kterým dochází, protože poslední zobrazení stránky pro úpravu bude zachycena.</span><span class="sxs-lookup"><span data-stu-id="f2af8-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="f2af8-255">`ModelState.Remove` Příkazu se totiž `ModelState` má starý `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="f2af8-256">Na stránce Razor `ModelState` hodnota pole má přednost před hodnoty vlastností modelu Pokud jsou obě přítomny.</span><span class="sxs-lookup"><span data-stu-id="f2af8-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-razor-page"></a><span data-ttu-id="f2af8-257">Aktualizace stránky Razor</span><span class="sxs-lookup"><span data-stu-id="f2af8-257">Update the Razor page</span></span>

<span data-ttu-id="f2af8-258">Aktualizovat *stránky/oddělení/upravit. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f2af8-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="f2af8-259">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="f2af8-259">The preceding code:</span></span>

* <span data-ttu-id="f2af8-260">Aktualizace `page` direktiv z `@page` k `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="f2af8-261">Přidá verze skryté řádku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-261">Adds a hidden row version.</span></span> <span data-ttu-id="f2af8-262">`RowVersion` je nutné přidat tak příspěvek zpět váže hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-262">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="f2af8-263">Zobrazí poslední bajt `RowVersion` pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="f2af8-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="f2af8-264">Nahradí `ViewData` pomocí silných `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="f2af8-265">Testování je v konfliktu s stránky pro úpravu souběžnosti</span><span class="sxs-lookup"><span data-stu-id="f2af8-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="f2af8-266">Otevřete dvě instance prohlížeče úpravy na anglické oddělení:</span><span class="sxs-lookup"><span data-stu-id="f2af8-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="f2af8-267">Spusťte aplikaci a vyberte oddělení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="f2af8-268">Klikněte pravým tlačítkem myši **upravit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě**.</span><span class="sxs-lookup"><span data-stu-id="f2af8-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="f2af8-269">Na první kartě klikněte **upravit** hypertextového odkazu pro anglickou oddělení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="f2af8-270">Záložkách prohlížeče dvě zobrazení stejné informace.</span><span class="sxs-lookup"><span data-stu-id="f2af8-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="f2af8-271">Změňte název na první záložce prohlížeče a klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="f2af8-271">Change the name in the first browser tab and click **Save**.</span></span>

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="f2af8-273">Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru.</span><span class="sxs-lookup"><span data-stu-id="f2af8-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="f2af8-274">Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.</span><span class="sxs-lookup"><span data-stu-id="f2af8-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="f2af8-275">Změňte jiné pole na druhé záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f2af8-275">Change a different field in the second browser tab.</span></span>

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="f2af8-277">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="f2af8-277">Click **Save**.</span></span> <span data-ttu-id="f2af8-278">Zobrazí se chybové zprávy pro všechna pole, která neodpovídají hodnotám databáze:</span><span class="sxs-lookup"><span data-stu-id="f2af8-278">You see error messages for all fields that don't match the database values:</span></span>

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error30.png)

<span data-ttu-id="f2af8-280">Toto okno prohlížeče neměli v úmyslu změnit název pole.</span><span class="sxs-lookup"><span data-stu-id="f2af8-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="f2af8-281">Zkopírujte a vložte do pole název aktuální hodnotu (jazyky).</span><span class="sxs-lookup"><span data-stu-id="f2af8-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="f2af8-282">Karta navýšení kapacity. Ověřování na straně klienta odebere chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="f2af8-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="f2af8-283">Klikněte na tlačítko **Uložit** znovu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-283">Click **Save** again.</span></span> <span data-ttu-id="f2af8-284">Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f2af8-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="f2af8-285">Zobrazí uložené hodnoty v indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="f2af8-286">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="f2af8-286">Update the Delete page</span></span>

<span data-ttu-id="f2af8-287">Aktualizujte *stránky/oddělení/odstraňte. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f2af8-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="f2af8-288">Na stránce odstranit rozpozná konfliktů souběžnosti, pokud entita změněna po načtení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="f2af8-289">`Department.RowVersion` verze řádku je, když se entita načetla.</span><span class="sxs-lookup"><span data-stu-id="f2af8-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="f2af8-290">EF Core vytvoří příkaz SQL DELETE, obsahuje klauzuli WHERE s `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="f2af8-291">Pokud vliv na výsledky příkazu SQL odstranit v nulový počet řádků:</span><span class="sxs-lookup"><span data-stu-id="f2af8-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="f2af8-292">Příkaz v příkazu SQL DELETE se v databázi neshoduje `RowVersion`. `RowVersion`</span><span class="sxs-lookup"><span data-stu-id="f2af8-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="f2af8-293">Je vyvolána výjimka DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="f2af8-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="f2af8-294">`OnGetAsync` volá se `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="f2af8-295">Aktualizace stránky odstranit Razor</span><span class="sxs-lookup"><span data-stu-id="f2af8-295">Update the Delete Razor page</span></span>

<span data-ttu-id="f2af8-296">Aktualizace *Pages/Departments/Delete.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f2af8-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="f2af8-297">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="f2af8-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="f2af8-298">Aktualizace `page` direktiv z `@page` k `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="f2af8-299">Přidá chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-299">Adds an error message.</span></span>
* <span data-ttu-id="f2af8-300">Nahradí celý název v FirstMidName **správce** pole.</span><span class="sxs-lookup"><span data-stu-id="f2af8-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="f2af8-301">Změny `RowVersion` k zobrazení poslední bajt.</span><span class="sxs-lookup"><span data-stu-id="f2af8-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="f2af8-302">Přidá verze skryté řádku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-302">Adds a hidden row version.</span></span> <span data-ttu-id="f2af8-303">`RowVersion`je nutné přidat, aby postgit přidat zpátky vazby hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f2af8-303">`RowVersion` must be added so postgit add back binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="f2af8-304">Konflikty testů v souběžnosti</span><span class="sxs-lookup"><span data-stu-id="f2af8-304">Test concurrency conflicts</span></span>

<span data-ttu-id="f2af8-305">Vytvořte test oddělení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-305">Create a test department.</span></span>

<span data-ttu-id="f2af8-306">Otevřete dvě instance prohlížeče DELETE na oddělení testu:</span><span class="sxs-lookup"><span data-stu-id="f2af8-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="f2af8-307">Spusťte aplikaci a vyberte oddělení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="f2af8-308">Klikněte pravým tlačítkem myši **odstranit** hypertextového odkazu pro oddělení test a vyberte **otevřít na nové kartě**.</span><span class="sxs-lookup"><span data-stu-id="f2af8-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="f2af8-309">Klikněte na tlačítko **upravit** hypertextového odkazu pro oddělení testu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="f2af8-310">Záložkách prohlížeče dvě zobrazení stejné informace.</span><span class="sxs-lookup"><span data-stu-id="f2af8-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="f2af8-311">Rozpočet na první záložce prohlížeče a klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="f2af8-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="f2af8-312">Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru.</span><span class="sxs-lookup"><span data-stu-id="f2af8-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="f2af8-313">Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.</span><span class="sxs-lookup"><span data-stu-id="f2af8-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="f2af8-314">Odstraňte test oddělení na druhé kartě. Chyba souběžnosti se zobrazuje s aktuálními hodnotami z databáze.</span><span class="sxs-lookup"><span data-stu-id="f2af8-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="f2af8-315">Kliknutím na **odstranit** odstraní entitu, není-li `RowVersion` byl updated.department byl odstraněn.</span><span class="sxs-lookup"><span data-stu-id="f2af8-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f2af8-316">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f2af8-316">Additional resources</span></span>

* [<span data-ttu-id="f2af8-317">Tokeny souběžnosti v EF Core</span><span class="sxs-lookup"><span data-stu-id="f2af8-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="f2af8-318">Popisovač souběžnosti v EF Core</span><span class="sxs-lookup"><span data-stu-id="f2af8-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="f2af8-319">Ladění zdrojového kódu ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="f2af8-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/aspnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="f2af8-320">Další postup</span><span class="sxs-lookup"><span data-stu-id="f2af8-320">Next steps</span></span>

<span data-ttu-id="f2af8-321">Toto je poslední kurz v řadě.</span><span class="sxs-lookup"><span data-stu-id="f2af8-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="f2af8-322">Další témata jsou popsaná v tématu [verze MVC této série kurzů](xref:data/ef-mvc/index).</span><span class="sxs-lookup"><span data-stu-id="f2af8-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f2af8-323">Předchozí kurz</span><span class="sxs-lookup"><span data-stu-id="f2af8-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f2af8-324">Tento kurz ukazuje, jak řešit konflikty při více uživateli aktualizovat entitu současně (ve stejnou dobu).</span><span class="sxs-lookup"><span data-stu-id="f2af8-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="f2af8-325">Pokud narazíte na potíže nelze vyřešit, [stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="f2af8-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="f2af8-326">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f2af8-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="f2af8-327">Konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="f2af8-327">Concurrency conflicts</span></span>

<span data-ttu-id="f2af8-328">Dojde ke konfliktu souběžnosti při:</span><span class="sxs-lookup"><span data-stu-id="f2af8-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="f2af8-329">Uživatel přejde na stránku upravit pro entitu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="f2af8-330">Jiný uživatel aktualizuje stejné entity před první uživatel změny jsou zapsána do databáze.</span><span class="sxs-lookup"><span data-stu-id="f2af8-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="f2af8-331">Pokud zjišťování souběžnosti není povolena, když dojde k souběžných aktualizací:</span><span class="sxs-lookup"><span data-stu-id="f2af8-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="f2af8-332">Poslední aktualizace wins.</span><span class="sxs-lookup"><span data-stu-id="f2af8-332">The last update wins.</span></span> <span data-ttu-id="f2af8-333">To znamená poslední aktualizace hodnoty se uloží do databáze.</span><span class="sxs-lookup"><span data-stu-id="f2af8-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="f2af8-334">První z aktuální aktualizace budou ztraceny.</span><span class="sxs-lookup"><span data-stu-id="f2af8-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="f2af8-335">Optimistická souběžnost</span><span class="sxs-lookup"><span data-stu-id="f2af8-335">Optimistic concurrency</span></span>

<span data-ttu-id="f2af8-336">Optimistického řízení souběžnosti umožňuje konfliktů souběžnosti, která se provede, a potom reaguje správně při dělají.</span><span class="sxs-lookup"><span data-stu-id="f2af8-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="f2af8-337">Například Jana návštěv stránky pro úpravu oddělení a změny rozpočet anglické oddělení od $350,000.00 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="f2af8-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="f2af8-339">Předtím, než Jan klikne **Uložit**, Jan navštíví na stejnou stránku a změny pole Datum zahájení 9/1/2013 z 9/1/2007.</span><span class="sxs-lookup"><span data-stu-id="f2af8-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Změna počátečního data a 2013](concurrency/_static/change-date.png)

<span data-ttu-id="f2af8-341">Jan klikne **Uložit** první a zobrazí ji změnit, pokud prohlížeč zobrazí indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Změnit na hodnotu nula rozpočtu](concurrency/_static/budget-zero.png)

<span data-ttu-id="f2af8-343">Jan klikne **Uložit** na stránce Upravit, která stále zobrazuje rozpočtu 350,000.00 $.</span><span class="sxs-lookup"><span data-stu-id="f2af8-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="f2af8-344">Co bude dál se určuje podle způsobu zpracování konfliktů souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="f2af8-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="f2af8-345">Optimistického řízení souběžnosti zahrnuje následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="f2af8-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="f2af8-346">Můžete sledovat, které vlastnosti uživatele byl změněn a aktualizovat pouze odpovídající sloupce v databázi.</span><span class="sxs-lookup"><span data-stu-id="f2af8-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="f2af8-347">Ve scénáři bude ztracena žádná data.</span><span class="sxs-lookup"><span data-stu-id="f2af8-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="f2af8-348">Různé vlastnosti byly aktualizovány dva uživatelé.</span><span class="sxs-lookup"><span data-stu-id="f2af8-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="f2af8-349">Při příštím někdo přejde z anglické oddělení, zobrazí se Jana a John's na změny.</span><span class="sxs-lookup"><span data-stu-id="f2af8-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="f2af8-350">Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě.</span><span class="sxs-lookup"><span data-stu-id="f2af8-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="f2af8-351">Tento přístup:</span><span class="sxs-lookup"><span data-stu-id="f2af8-351">This approach:</span></span>
 
  * <span data-ttu-id="f2af8-352">Nelze nedošlo ke ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f2af8-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="f2af8-353">Není obecně praktické ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f2af8-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="f2af8-354">Vyžaduje udržování významné stavu, aby bylo možné udržovat přehled o všech načtených hodnoty a nové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f2af8-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="f2af8-355">Zachování velké množství stavu může ovlivnit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="f2af8-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="f2af8-356">Můžete zvýšit složitost aplikace ve srovnání s detekce souběžnosti s entitou.</span><span class="sxs-lookup"><span data-stu-id="f2af8-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="f2af8-357">Můžete nechat John's na změnu Jana změna přepsána.</span><span class="sxs-lookup"><span data-stu-id="f2af8-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="f2af8-358">Při příštím někdo přejde z anglické oddělení, zobrazí se 9/1/2013 a počet získaných $350,000.00 hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="f2af8-359">Tento přístup se nazývá *Wins, klient* nebo *poslední ve službě Wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="f2af8-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="f2af8-360">(Všechny hodnoty z klienta přednost co je v úložišti.) Pokud tak učiníte nemusíte vytvářet kód pro zpracování souběžnosti, Wins, klient probíhá automaticky.</span><span class="sxs-lookup"><span data-stu-id="f2af8-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="f2af8-361">John's na změnu může zabránit aktualizují v databázi.</span><span class="sxs-lookup"><span data-stu-id="f2af8-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="f2af8-362">Obvykle by aplikace:</span><span class="sxs-lookup"><span data-stu-id="f2af8-362">Typically, the app would:</span></span>

  * <span data-ttu-id="f2af8-363">Zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-363">Display an error message.</span></span>
  * <span data-ttu-id="f2af8-364">Zobrazit aktuální stav dat.</span><span class="sxs-lookup"><span data-stu-id="f2af8-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="f2af8-365">Povolit uživateli, který chcete znovu použít změny.</span><span class="sxs-lookup"><span data-stu-id="f2af8-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="f2af8-366">Tento postup se nazývá *Store Wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="f2af8-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="f2af8-367">(Hodnoty úložiště dat přednost hodnoty odeslány klientem.) Implementace služby Wins Store scénář v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="f2af8-368">Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění.</span><span class="sxs-lookup"><span data-stu-id="f2af8-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="f2af8-369">Ošetření souběžnosti</span><span class="sxs-lookup"><span data-stu-id="f2af8-369">Handling concurrency</span></span> 

<span data-ttu-id="f2af8-370">Když je vlastnost nakonfigurovaný jako [tokenem souběžnosti](/ef/core/modeling/concurrency):</span><span class="sxs-lookup"><span data-stu-id="f2af8-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="f2af8-371">EF Core ověřuje, že vlastnost byl změněn po načtení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="f2af8-372">Kontrola dochází při [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) nebo [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) je volána.</span><span class="sxs-lookup"><span data-stu-id="f2af8-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="f2af8-373">Pokud vlastnost byl změněn po načtení, [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="f2af8-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) is thrown.</span></span> 

<span data-ttu-id="f2af8-374">Databáze a datový model musí být nakonfigurované pro podporu vyvolání `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="f2af8-375">Zjišťování konfliktů souběžnosti u vlastnosti</span><span class="sxs-lookup"><span data-stu-id="f2af8-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="f2af8-376">Konflikty souběžnosti lze zjistit pomocí na úrovni vlastnost [atribut ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) atribut.</span><span class="sxs-lookup"><span data-stu-id="f2af8-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribute.</span></span> <span data-ttu-id="f2af8-377">Atribut lze použít na více vlastností v modelu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="f2af8-378">Další informace najdete v tématu [anotací dat – atribut ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="f2af8-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="f2af8-379">`[ConcurrencyCheck]` Atribut není použit v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="f2af8-380">Zjišťování konfliktů souběžnosti na řádek</span><span class="sxs-lookup"><span data-stu-id="f2af8-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="f2af8-381">K detekci konfliktů souběžnosti [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) sledování sloupec se přidá do modelu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="f2af8-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="f2af8-382">`rowversion` :</span></span>

* <span data-ttu-id="f2af8-383">SQL Server je konkrétní.</span><span class="sxs-lookup"><span data-stu-id="f2af8-383">Is SQL Server specific.</span></span> <span data-ttu-id="f2af8-384">Ostatní databáze neposkytují podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="f2af8-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="f2af8-385">Slouží k určení, že entita nebyl změněn od načtení z databáze.</span><span class="sxs-lookup"><span data-stu-id="f2af8-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="f2af8-386">Databáze generuje sekvenční `rowversion` aktualizovat číslo, které se zvýší pokaždé, když na řádek.</span><span class="sxs-lookup"><span data-stu-id="f2af8-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="f2af8-387">V `Update` nebo `Delete` příkazu `Where` klauzule obsahuje hodnotu načtených `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="f2af8-388">Pokud došlo ke změně aktualizuje řádek:</span><span class="sxs-lookup"><span data-stu-id="f2af8-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="f2af8-389">`rowversion` neodpovídá hodnotě načtených.</span><span class="sxs-lookup"><span data-stu-id="f2af8-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="f2af8-390">`Update` Nebo `Delete` příkazů nelze nalézt řádek, protože `Where` klauzule obsahuje načetly `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="f2af8-391">A `DbUpdateConcurrencyException` je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="f2af8-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="f2af8-392">V EF Core, když nebyly aktualizovány žádné řádky pomocí `Update` nebo `Delete` příkaz, je vyvolána výjimka souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="f2af8-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="f2af8-393">Přidání vlastnosti sledování do entity oddělení</span><span class="sxs-lookup"><span data-stu-id="f2af8-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="f2af8-394">V *Models/Department.cs*, přidání vlastnosti sledování do s názvem RowVersion:</span><span class="sxs-lookup"><span data-stu-id="f2af8-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="f2af8-395">[Časové razítko](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) atribut určuje, že je součástí tohoto sloupce `Where` klauzuli `Update` a `Delete` příkazy.</span><span class="sxs-lookup"><span data-stu-id="f2af8-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="f2af8-396">Atribut se nazývá `Timestamp` protože předchozích verzí SQL serveru použít SQL `timestamp` datového typu než SQL `rowversion` typ nahradili jsme ho.</span><span class="sxs-lookup"><span data-stu-id="f2af8-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="f2af8-397">Rozhraní fluent API můžete také zadat vlastnosti sledování:</span><span class="sxs-lookup"><span data-stu-id="f2af8-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="f2af8-398">Následující kód ukazuje část generovaných EF Core, když se aktualizuje název oddělení T-SQL:</span><span class="sxs-lookup"><span data-stu-id="f2af8-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="f2af8-399">Předchozí zvýrazněný kód ukazuje `WHERE` obsahující klauzuli `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="f2af8-400">Pokud databáze `RowVersion` není roven `RowVersion` parametr (`@p2`), jsou aktualizovány žádné řádky.</span><span class="sxs-lookup"><span data-stu-id="f2af8-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="f2af8-401">Následující zvýrazněný kód ukazuje T-SQL, která ověřuje, že byl aktualizován přesně jeden řádek:</span><span class="sxs-lookup"><span data-stu-id="f2af8-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="f2af8-402">[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) vrací počet řádků, které jsou ovlivněny poslední příkaz.</span><span class="sxs-lookup"><span data-stu-id="f2af8-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="f2af8-403">V žádné řádky jsou aktualizovány, vyvolá EF Core `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="f2af8-404">Uvidíte, že v okně výstupu sady Visual Studio generuje EF Core T-SQL.</span><span class="sxs-lookup"><span data-stu-id="f2af8-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="f2af8-405">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="f2af8-405">Update the DB</span></span>

<span data-ttu-id="f2af8-406">Přidávání `RowVersion` změní vlastnost databáze modelu, který vyžaduje migraci.</span><span class="sxs-lookup"><span data-stu-id="f2af8-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="f2af8-407">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="f2af8-407">Build the project.</span></span> <span data-ttu-id="f2af8-408">V příkazovém okně zadejte následující údaje:</span><span class="sxs-lookup"><span data-stu-id="f2af8-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="f2af8-409">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="f2af8-409">The preceding commands:</span></span>

* <span data-ttu-id="f2af8-410">Přidá *migrace / {čas stamp}_RowVersion.cs* souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="f2af8-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="f2af8-411">Aktualizace *Migrations/SchoolContextModelSnapshot.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="f2af8-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="f2af8-412">Tato aktualizace přidává následující zvýrazněný kód do `BuildModel` metody:</span><span class="sxs-lookup"><span data-stu-id="f2af8-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="f2af8-413">Spuštění migrace k aktualizaci databáze.</span><span class="sxs-lookup"><span data-stu-id="f2af8-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="f2af8-414">Vygenerované uživatelské rozhraní modelu oddělení</span><span class="sxs-lookup"><span data-stu-id="f2af8-414">Scaffold the Departments model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f2af8-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2af8-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="f2af8-416">Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-student-pages) a použít `Department` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f2af8-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2af8-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="f2af8-418">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f2af8-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="f2af8-419">Předchozí příkaz scaffold `Department` modelu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="f2af8-420">Otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f2af8-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="f2af8-421">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="f2af8-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="f2af8-422">Aktualizace oddělení indexovou stránku</span><span class="sxs-lookup"><span data-stu-id="f2af8-422">Update the Departments Index page</span></span>

<span data-ttu-id="f2af8-423">Generování uživatelského rozhraní engine vytvoření `RowVersion` by neměl být zobrazen sloupec pro indexovou stránku, ale toto pole.</span><span class="sxs-lookup"><span data-stu-id="f2af8-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="f2af8-424">V tomto kurzu, poslední bajt `RowVersion` zobrazí se vám může pomoci souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="f2af8-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="f2af8-425">Poslední bajt nemusí být jedinečný.</span><span class="sxs-lookup"><span data-stu-id="f2af8-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="f2af8-426">Skutečné aplikace nezobrazily `RowVersion` nebo posledního bajtu `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="f2af8-427">Aktualizace indexovou stránku:</span><span class="sxs-lookup"><span data-stu-id="f2af8-427">Update the Index page:</span></span>

* <span data-ttu-id="f2af8-428">Nahraďte indexem oddělení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="f2af8-429">Nahraďte kód obsahující `RowVersion` s poslední bajt `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="f2af8-430">Nahraďte FirstMidName jméno a příjmení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="f2af8-431">Následující kód ukazuje aktualizovanou stránku:</span><span class="sxs-lookup"><span data-stu-id="f2af8-431">The following markup shows the updated page:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="f2af8-432">Aktualizace modelu stránky úpravy</span><span class="sxs-lookup"><span data-stu-id="f2af8-432">Update the Edit page model</span></span>

<span data-ttu-id="f2af8-433">*Pages\Departments\Edit.cshtml.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f2af8-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="f2af8-434">Ke zjištění problému souběžnosti, [původní hodnota](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) je aktualizován `rowVersion` hodnotu z entity se načetla.</span><span class="sxs-lookup"><span data-stu-id="f2af8-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="f2af8-435">EF Core generuje příkazu SQL UPDATE s klauzulí WHERE, který obsahuje původní `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="f2af8-436">Pokud žádné řádky jsou ovlivněny příkazu UPDATE (žádné řádky mít původní `RowVersion` hodnota), `DbUpdateConcurrencyException` je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="f2af8-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="f2af8-437">V předchozím kódu `Department.RowVersion` je hodnota, pokud se entita načetla.</span><span class="sxs-lookup"><span data-stu-id="f2af8-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="f2af8-438">`OriginalValue` je hodnota v databázi při `FirstOrDefaultAsync` byla volána v této metodě.</span><span class="sxs-lookup"><span data-stu-id="f2af8-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="f2af8-439">Následující kód načte hodnoty klienta (hodnoty, publikuje se do této metody) a hodnoty DB:</span><span class="sxs-lookup"><span data-stu-id="f2af8-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="f2af8-440">Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišné od odeslání do `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="f2af8-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="f2af8-441">Následující zvýrazněný kód nastaví `RowVersion` z databáze načíst hodnotu na novou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="f2af8-442">Při příštím kliknutí na tlačítko **Uložit**, pouze souběžnosti chyby, ke kterým dochází, protože poslední zobrazení stránky pro úpravu bude zachycena.</span><span class="sxs-lookup"><span data-stu-id="f2af8-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="f2af8-443">`ModelState.Remove` Příkazu se totiž `ModelState` má starý `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="f2af8-444">Na stránce Razor `ModelState` hodnota pole má přednost před hodnoty vlastností modelu Pokud jsou obě přítomny.</span><span class="sxs-lookup"><span data-stu-id="f2af8-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="f2af8-445">Aktualizace stránky pro úpravu</span><span class="sxs-lookup"><span data-stu-id="f2af8-445">Update the Edit page</span></span>

<span data-ttu-id="f2af8-446">Aktualizace *Pages/Departments/Edit.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f2af8-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="f2af8-447">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="f2af8-447">The preceding markup:</span></span>

* <span data-ttu-id="f2af8-448">Aktualizace `page` direktiv z `@page` k `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="f2af8-449">Přidá verze skryté řádku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-449">Adds a hidden row version.</span></span> <span data-ttu-id="f2af8-450">`RowVersion` je nutné přidat tak příspěvek zpět váže hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="f2af8-451">Zobrazí poslední bajt `RowVersion` pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="f2af8-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="f2af8-452">Nahradí `ViewData` pomocí silných `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="f2af8-453">Testování je v konfliktu s stránky pro úpravu souběžnosti</span><span class="sxs-lookup"><span data-stu-id="f2af8-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="f2af8-454">Otevřete dvě instance prohlížeče úpravy na anglické oddělení:</span><span class="sxs-lookup"><span data-stu-id="f2af8-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="f2af8-455">Spusťte aplikaci a vyberte oddělení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="f2af8-456">Klikněte pravým tlačítkem myši **upravit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě**.</span><span class="sxs-lookup"><span data-stu-id="f2af8-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="f2af8-457">Na první kartě klikněte **upravit** hypertextového odkazu pro anglickou oddělení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="f2af8-458">Záložkách prohlížeče dvě zobrazení stejné informace.</span><span class="sxs-lookup"><span data-stu-id="f2af8-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="f2af8-459">Změňte název na první záložce prohlížeče a klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="f2af8-459">Change the name in the first browser tab and click **Save**.</span></span>

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="f2af8-461">Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru.</span><span class="sxs-lookup"><span data-stu-id="f2af8-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="f2af8-462">Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.</span><span class="sxs-lookup"><span data-stu-id="f2af8-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="f2af8-463">Změňte jiné pole na druhé záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f2af8-463">Change a different field in the second browser tab.</span></span>

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="f2af8-465">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="f2af8-465">Click **Save**.</span></span> <span data-ttu-id="f2af8-466">Zobrazí se chybové zprávy pro všechna pole, která se neshodují s hodnotami DB:</span><span class="sxs-lookup"><span data-stu-id="f2af8-466">You see error messages for all fields that don't match the DB values:</span></span>

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error.png)

<span data-ttu-id="f2af8-468">Toto okno prohlížeče neměli v úmyslu změnit název pole.</span><span class="sxs-lookup"><span data-stu-id="f2af8-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="f2af8-469">Zkopírujte a vložte do pole název aktuální hodnotu (jazyky).</span><span class="sxs-lookup"><span data-stu-id="f2af8-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="f2af8-470">Karta navýšení kapacity. Ověřování na straně klienta odebere chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="f2af8-470">Tab out. Client-side validation removes the error message.</span></span>

![Oddělení upravit stránku chybová zpráva](concurrency/_static/cv.png)

<span data-ttu-id="f2af8-472">Klikněte na tlačítko **Uložit** znovu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-472">Click **Save** again.</span></span> <span data-ttu-id="f2af8-473">Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f2af8-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="f2af8-474">Zobrazí uložené hodnoty v indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="f2af8-475">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="f2af8-475">Update the Delete page</span></span>

<span data-ttu-id="f2af8-476">Aktualizace modelu odstranění stránky s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f2af8-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="f2af8-477">Na stránce odstranit rozpozná konfliktů souběžnosti, pokud entita změněna po načtení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="f2af8-478">`Department.RowVersion` verze řádku je, když se entita načetla.</span><span class="sxs-lookup"><span data-stu-id="f2af8-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="f2af8-479">EF Core vytvoří příkaz SQL DELETE, obsahuje klauzuli WHERE s `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="f2af8-480">Pokud vliv na výsledky příkazu SQL odstranit v nulový počet řádků:</span><span class="sxs-lookup"><span data-stu-id="f2af8-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="f2af8-481">`RowVersion` v odstranit SQL příkaz neodpovídá `RowVersion` v databázi.</span><span class="sxs-lookup"><span data-stu-id="f2af8-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="f2af8-482">Je vyvolána výjimka DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="f2af8-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="f2af8-483">`OnGetAsync` volá se `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="f2af8-484">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="f2af8-484">Update the Delete page</span></span>

<span data-ttu-id="f2af8-485">Aktualizace *Pages/Departments/Delete.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f2af8-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="f2af8-486">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="f2af8-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="f2af8-487">Aktualizace `page` direktiv z `@page` k `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="f2af8-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="f2af8-488">Přidá chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-488">Adds an error message.</span></span>
* <span data-ttu-id="f2af8-489">Nahradí celý název v FirstMidName **správce** pole.</span><span class="sxs-lookup"><span data-stu-id="f2af8-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="f2af8-490">Změny `RowVersion` k zobrazení poslední bajt.</span><span class="sxs-lookup"><span data-stu-id="f2af8-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="f2af8-491">Přidá verze skryté řádku.</span><span class="sxs-lookup"><span data-stu-id="f2af8-491">Adds a hidden row version.</span></span> <span data-ttu-id="f2af8-492">`RowVersion` je nutné přidat tak příspěvek zpět váže hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="f2af8-493">Konflikty souběžnosti testu se stránkou Delete</span><span class="sxs-lookup"><span data-stu-id="f2af8-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="f2af8-494">Vytvořte test oddělení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-494">Create a test department.</span></span>

<span data-ttu-id="f2af8-495">Otevřete dvě instance prohlížeče DELETE na oddělení testu:</span><span class="sxs-lookup"><span data-stu-id="f2af8-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="f2af8-496">Spusťte aplikaci a vyberte oddělení.</span><span class="sxs-lookup"><span data-stu-id="f2af8-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="f2af8-497">Klikněte pravým tlačítkem myši **odstranit** hypertextového odkazu pro oddělení test a vyberte **otevřít na nové kartě**.</span><span class="sxs-lookup"><span data-stu-id="f2af8-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="f2af8-498">Klikněte na tlačítko **upravit** hypertextového odkazu pro oddělení testu.</span><span class="sxs-lookup"><span data-stu-id="f2af8-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="f2af8-499">Záložkách prohlížeče dvě zobrazení stejné informace.</span><span class="sxs-lookup"><span data-stu-id="f2af8-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="f2af8-500">Rozpočet na první záložce prohlížeče a klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="f2af8-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="f2af8-501">Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru.</span><span class="sxs-lookup"><span data-stu-id="f2af8-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="f2af8-502">Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.</span><span class="sxs-lookup"><span data-stu-id="f2af8-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="f2af8-503">Odstraňte test oddělení na druhé kartě. Došlo k chybě souběžnosti je zobrazení pomocí aktuálních hodnot z databáze.</span><span class="sxs-lookup"><span data-stu-id="f2af8-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="f2af8-504">Kliknutím na **odstranit** odstraní entitu, není-li `RowVersion` byl updated.department byl odstraněn.</span><span class="sxs-lookup"><span data-stu-id="f2af8-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

<span data-ttu-id="f2af8-505">Zobrazit [dědičnosti](xref:data/ef-mvc/inheritance) o tom, jak dědit datový model.</span><span class="sxs-lookup"><span data-stu-id="f2af8-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="f2af8-506">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f2af8-506">Additional resources</span></span>

* [<span data-ttu-id="f2af8-507">Tokeny souběžnosti v EF Core</span><span class="sxs-lookup"><span data-stu-id="f2af8-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="f2af8-508">Popisovač souběžnosti v EF Core</span><span class="sxs-lookup"><span data-stu-id="f2af8-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="f2af8-509">Verze tohoto kurzu pro YouTube (zpracování konfliktů souběžnosti)</span><span class="sxs-lookup"><span data-stu-id="f2af8-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="f2af8-510">Verze tohoto kurzu pro YouTube (část 2)</span><span class="sxs-lookup"><span data-stu-id="f2af8-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="f2af8-511">Verze tohoto kurzu pro YouTube (část 3)</span><span class="sxs-lookup"><span data-stu-id="f2af8-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="f2af8-512">Předchozí</span><span class="sxs-lookup"><span data-stu-id="f2af8-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

