---
title: Stránky Razor s EF Core v ASP.NET Core - souběžnosti - 8 8
author: rick-anderson
description: Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: 8430f8e720870a7b541655ea8bcfe2f67c942bb3
ms.sourcegitcommit: c5339594101d30b189f61761275b7d310e80d18a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2019
ms.locfileid: "66458424"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a><span data-ttu-id="09f64-103">Stránky Razor s EF Core v ASP.NET Core - souběžnosti - 8 8</span><span class="sxs-lookup"><span data-stu-id="09f64-103">Razor Pages with EF Core in ASP.NET Core - Concurrency - 8 of 8</span></span>

<span data-ttu-id="09f64-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Petr Dykstra](https://github.com/tdykstra), a [Jan Macek P](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="09f64-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="09f64-105">Tento kurz ukazuje, jak řešit konflikty při více uživateli aktualizovat entitu současně (ve stejnou dobu).</span><span class="sxs-lookup"><span data-stu-id="09f64-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="09f64-106">Pokud narazíte na potíže nelze vyřešit, [stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="09f64-106">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="09f64-107">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="09f64-107">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="09f64-108">Konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="09f64-108">Concurrency conflicts</span></span>

<span data-ttu-id="09f64-109">Dojde ke konfliktu souběžnosti při:</span><span class="sxs-lookup"><span data-stu-id="09f64-109">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="09f64-110">Uživatel přejde na stránku upravit pro entitu.</span><span class="sxs-lookup"><span data-stu-id="09f64-110">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="09f64-111">Jiný uživatel aktualizuje stejné entity před první uživatel změny jsou zapsána do databáze.</span><span class="sxs-lookup"><span data-stu-id="09f64-111">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="09f64-112">Pokud zjišťování souběžnosti není povolena, když dojde k souběžných aktualizací:</span><span class="sxs-lookup"><span data-stu-id="09f64-112">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="09f64-113">Poslední aktualizace wins.</span><span class="sxs-lookup"><span data-stu-id="09f64-113">The last update wins.</span></span> <span data-ttu-id="09f64-114">To znamená poslední aktualizace hodnoty se uloží do databáze.</span><span class="sxs-lookup"><span data-stu-id="09f64-114">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="09f64-115">První z aktuální aktualizace budou ztraceny.</span><span class="sxs-lookup"><span data-stu-id="09f64-115">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="09f64-116">Optimistická souběžnost</span><span class="sxs-lookup"><span data-stu-id="09f64-116">Optimistic concurrency</span></span>

<span data-ttu-id="09f64-117">Optimistického řízení souběžnosti umožňuje konfliktů souběžnosti, která se provede, a potom reaguje správně při dělají.</span><span class="sxs-lookup"><span data-stu-id="09f64-117">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="09f64-118">Například Jana návštěv stránky pro úpravu oddělení a změny rozpočet anglické oddělení od $350,000.00 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="09f64-118">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="09f64-120">Předtím, než Jan klikne **Uložit**, Jan navštíví na stejnou stránku a změny pole Datum zahájení 9/1/2013 z 9/1/2007.</span><span class="sxs-lookup"><span data-stu-id="09f64-120">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Změna počátečního data a 2013](concurrency/_static/change-date.png)

<span data-ttu-id="09f64-122">Jan klikne **Uložit** první a zobrazí ji změnit, pokud prohlížeč zobrazí indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="09f64-122">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Změnit na hodnotu nula rozpočtu](concurrency/_static/budget-zero.png)

<span data-ttu-id="09f64-124">Jan klikne **Uložit** na stránce Upravit, která stále zobrazuje rozpočtu 350,000.00 $.</span><span class="sxs-lookup"><span data-stu-id="09f64-124">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="09f64-125">Co bude dál se určuje podle způsobu zpracování konfliktů souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="09f64-125">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="09f64-126">Optimistického řízení souběžnosti zahrnuje následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="09f64-126">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="09f64-127">Můžete sledovat, které vlastnosti uživatele byl změněn a aktualizovat pouze odpovídající sloupce v databázi.</span><span class="sxs-lookup"><span data-stu-id="09f64-127">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="09f64-128">Ve scénáři bude ztracena žádná data.</span><span class="sxs-lookup"><span data-stu-id="09f64-128">In the scenario, no data would be lost.</span></span> <span data-ttu-id="09f64-129">Různé vlastnosti byly aktualizovány dva uživatelé.</span><span class="sxs-lookup"><span data-stu-id="09f64-129">Different properties were updated by the two users.</span></span> <span data-ttu-id="09f64-130">Při příštím někdo přejde z anglické oddělení, zobrazí se Jana a John's na změny.</span><span class="sxs-lookup"><span data-stu-id="09f64-130">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="09f64-131">Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě.</span><span class="sxs-lookup"><span data-stu-id="09f64-131">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="09f64-132">Tento přístup:</span><span class="sxs-lookup"><span data-stu-id="09f64-132">This approach:</span></span>
 
  * <span data-ttu-id="09f64-133">Nelze nedošlo ke ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="09f64-133">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="09f64-134">Není obecně praktické ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="09f64-134">Is generally not practical in a web app.</span></span> <span data-ttu-id="09f64-135">Vyžaduje udržování významné stavu, aby bylo možné udržovat přehled o všech načtených hodnoty a nové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="09f64-135">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="09f64-136">Zachování velké množství stavu může ovlivnit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="09f64-136">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="09f64-137">Můžete zvýšit složitost aplikace ve srovnání s detekce souběžnosti s entitou.</span><span class="sxs-lookup"><span data-stu-id="09f64-137">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="09f64-138">Můžete nechat John's na změnu Jana změna přepsána.</span><span class="sxs-lookup"><span data-stu-id="09f64-138">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="09f64-139">Při příštím někdo přejde z anglické oddělení, zobrazí se 9/1/2013 a počet získaných $350,000.00 hodnotu.</span><span class="sxs-lookup"><span data-stu-id="09f64-139">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="09f64-140">Tento přístup se nazývá *Wins, klient* nebo *poslední ve službě Wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="09f64-140">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="09f64-141">(Všechny hodnoty z klienta přednost co je v úložišti.) Pokud tak učiníte nemusíte vytvářet kód pro zpracování souběžnosti, Wins, klient probíhá automaticky.</span><span class="sxs-lookup"><span data-stu-id="09f64-141">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="09f64-142">John's na změnu může zabránit aktualizují v databázi.</span><span class="sxs-lookup"><span data-stu-id="09f64-142">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="09f64-143">Obvykle by aplikace:</span><span class="sxs-lookup"><span data-stu-id="09f64-143">Typically, the app would:</span></span>

  * <span data-ttu-id="09f64-144">Zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="09f64-144">Display an error message.</span></span>
  * <span data-ttu-id="09f64-145">Zobrazit aktuální stav dat.</span><span class="sxs-lookup"><span data-stu-id="09f64-145">Show the current state of the data.</span></span>
  * <span data-ttu-id="09f64-146">Povolit uživateli, který chcete znovu použít změny.</span><span class="sxs-lookup"><span data-stu-id="09f64-146">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="09f64-147">Tento postup se nazývá *Store Wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="09f64-147">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="09f64-148">(Hodnoty úložiště dat přednost hodnoty odeslány klientem.) Implementace služby Wins Store scénář v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="09f64-148">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="09f64-149">Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění.</span><span class="sxs-lookup"><span data-stu-id="09f64-149">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="09f64-150">Ošetření souběžnosti</span><span class="sxs-lookup"><span data-stu-id="09f64-150">Handling concurrency</span></span> 

<span data-ttu-id="09f64-151">Když je vlastnost nakonfigurovaný jako [tokenem souběžnosti](/ef/core/modeling/concurrency):</span><span class="sxs-lookup"><span data-stu-id="09f64-151">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="09f64-152">EF Core ověřuje, že vlastnost byl změněn po načtení.</span><span class="sxs-lookup"><span data-stu-id="09f64-152">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="09f64-153">Kontrola dochází při [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) nebo [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) je volána.</span><span class="sxs-lookup"><span data-stu-id="09f64-153">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="09f64-154">Pokud vlastnost byl změněn po načtení, [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="09f64-154">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) is thrown.</span></span> 

<span data-ttu-id="09f64-155">Databáze a datový model musí být nakonfigurované pro podporu vyvolání `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="09f64-155">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="09f64-156">Zjišťování konfliktů souběžnosti u vlastnosti</span><span class="sxs-lookup"><span data-stu-id="09f64-156">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="09f64-157">Konflikty souběžnosti lze zjistit pomocí na úrovni vlastnost [atribut ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) atribut.</span><span class="sxs-lookup"><span data-stu-id="09f64-157">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribute.</span></span> <span data-ttu-id="09f64-158">Atribut lze použít na více vlastností v modelu.</span><span class="sxs-lookup"><span data-stu-id="09f64-158">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="09f64-159">Další informace najdete v tématu [anotací dat – atribut ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="09f64-159">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="09f64-160">`[ConcurrencyCheck]` Atribut není použit v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="09f64-160">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="09f64-161">Zjišťování konfliktů souběžnosti na řádek</span><span class="sxs-lookup"><span data-stu-id="09f64-161">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="09f64-162">K detekci konfliktů souběžnosti [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) sledování sloupec se přidá do modelu.</span><span class="sxs-lookup"><span data-stu-id="09f64-162">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="09f64-163">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="09f64-163">`rowversion` :</span></span>

* <span data-ttu-id="09f64-164">SQL Server je konkrétní.</span><span class="sxs-lookup"><span data-stu-id="09f64-164">Is SQL Server specific.</span></span> <span data-ttu-id="09f64-165">Ostatní databáze neposkytují podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="09f64-165">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="09f64-166">Slouží k určení, že entita nebyl změněn od načtení z databáze.</span><span class="sxs-lookup"><span data-stu-id="09f64-166">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="09f64-167">Databáze generuje sekvenční `rowversion` aktualizovat číslo, které se zvýší pokaždé, když na řádek.</span><span class="sxs-lookup"><span data-stu-id="09f64-167">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="09f64-168">V `Update` nebo `Delete` příkazu `Where` klauzule obsahuje hodnotu načtených `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="09f64-168">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="09f64-169">Pokud došlo ke změně aktualizuje řádek:</span><span class="sxs-lookup"><span data-stu-id="09f64-169">If the row being updated has changed:</span></span>

* <span data-ttu-id="09f64-170">`rowversion` neodpovídá hodnotě načtených.</span><span class="sxs-lookup"><span data-stu-id="09f64-170">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="09f64-171">`Update` Nebo `Delete` příkazů nelze nalézt řádek, protože `Where` klauzule obsahuje načetly `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="09f64-171">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="09f64-172">A `DbUpdateConcurrencyException` je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="09f64-172">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="09f64-173">V EF Core, když nebyly aktualizovány žádné řádky pomocí `Update` nebo `Delete` příkaz, je vyvolána výjimka souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="09f64-173">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="09f64-174">Přidání vlastnosti sledování do entity oddělení</span><span class="sxs-lookup"><span data-stu-id="09f64-174">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="09f64-175">V *Models/Department.cs*, přidání vlastnosti sledování do s názvem RowVersion:</span><span class="sxs-lookup"><span data-stu-id="09f64-175">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="09f64-176">[Časové razítko](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) atribut určuje, že je součástí tohoto sloupce `Where` klauzuli `Update` a `Delete` příkazy.</span><span class="sxs-lookup"><span data-stu-id="09f64-176">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="09f64-177">Atribut se nazývá `Timestamp` protože předchozích verzí SQL serveru použít SQL `timestamp` datového typu než SQL `rowversion` typ nahradili jsme ho.</span><span class="sxs-lookup"><span data-stu-id="09f64-177">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="09f64-178">Rozhraní fluent API můžete také zadat vlastnosti sledování:</span><span class="sxs-lookup"><span data-stu-id="09f64-178">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="09f64-179">Následující kód ukazuje část generovaných EF Core, když se aktualizuje název oddělení T-SQL:</span><span class="sxs-lookup"><span data-stu-id="09f64-179">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/sql.txt?highlight=2-3)]

<span data-ttu-id="09f64-180">Předchozí zvýrazněný kód ukazuje `WHERE` obsahující klauzuli `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="09f64-180">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="09f64-181">Pokud databáze `RowVersion` není roven `RowVersion` parametr (`@p2`), jsou aktualizovány žádné řádky.</span><span class="sxs-lookup"><span data-stu-id="09f64-181">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="09f64-182">Následující zvýrazněný kód ukazuje T-SQL, která ověřuje, že byl aktualizován přesně jeden řádek:</span><span class="sxs-lookup"><span data-stu-id="09f64-182">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/sql.txt?highlight=4-6)]

<span data-ttu-id="09f64-183">[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) vrací počet řádků, které jsou ovlivněny poslední příkaz.</span><span class="sxs-lookup"><span data-stu-id="09f64-183">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="09f64-184">V žádné řádky jsou aktualizovány, vyvolá EF Core `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="09f64-184">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="09f64-185">Uvidíte, že v okně výstupu sady Visual Studio generuje EF Core T-SQL.</span><span class="sxs-lookup"><span data-stu-id="09f64-185">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="09f64-186">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="09f64-186">Update the DB</span></span>

<span data-ttu-id="09f64-187">Přidávání `RowVersion` změní vlastnost databáze modelu, který vyžaduje migraci.</span><span class="sxs-lookup"><span data-stu-id="09f64-187">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="09f64-188">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="09f64-188">Build the project.</span></span> <span data-ttu-id="09f64-189">V příkazovém okně zadejte následující údaje:</span><span class="sxs-lookup"><span data-stu-id="09f64-189">Enter the following in a command window:</span></span>

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="09f64-190">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="09f64-190">The preceding commands:</span></span>

* <span data-ttu-id="09f64-191">Přidá *migrace / {čas stamp}_RowVersion.cs* souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="09f64-191">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="09f64-192">Aktualizace *Migrations/SchoolContextModelSnapshot.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="09f64-192">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="09f64-193">Tato aktualizace přidává následující zvýrazněný kód do `BuildModel` metody:</span><span class="sxs-lookup"><span data-stu-id="09f64-193">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot2.cs?name=snippet&highlight=14-16)]

* <span data-ttu-id="09f64-194">Spuštění migrace k aktualizaci databáze.</span><span class="sxs-lookup"><span data-stu-id="09f64-194">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="09f64-195">Vygenerované uživatelské rozhraní modelu oddělení</span><span class="sxs-lookup"><span data-stu-id="09f64-195">Scaffold the Departments model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="09f64-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09f64-196">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="09f64-197">Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Department` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="09f64-197">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Department` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="09f64-198">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="09f64-198">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="09f64-199">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="09f64-199">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="09f64-200">Předchozí příkaz scaffold `Department` modelu.</span><span class="sxs-lookup"><span data-stu-id="09f64-200">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="09f64-201">Otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="09f64-201">Open the project in Visual Studio.</span></span>

<span data-ttu-id="09f64-202">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="09f64-202">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="09f64-203">Aktualizace oddělení indexovou stránku</span><span class="sxs-lookup"><span data-stu-id="09f64-203">Update the Departments Index page</span></span>

<span data-ttu-id="09f64-204">Generování uživatelského rozhraní engine vytvoření `RowVersion` by neměl být zobrazen sloupec pro indexovou stránku, ale toto pole.</span><span class="sxs-lookup"><span data-stu-id="09f64-204">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="09f64-205">V tomto kurzu, poslední bajt `RowVersion` zobrazí se vám může pomoci souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="09f64-205">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="09f64-206">Poslední bajt nemusí být jedinečný.</span><span class="sxs-lookup"><span data-stu-id="09f64-206">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="09f64-207">Skutečné aplikace nezobrazily `RowVersion` nebo posledního bajtu `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="09f64-207">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="09f64-208">Aktualizace indexovou stránku:</span><span class="sxs-lookup"><span data-stu-id="09f64-208">Update the Index page:</span></span>

* <span data-ttu-id="09f64-209">Nahraďte indexem oddělení.</span><span class="sxs-lookup"><span data-stu-id="09f64-209">Replace Index with Departments.</span></span>
* <span data-ttu-id="09f64-210">Nahraďte kód obsahující `RowVersion` s poslední bajt `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="09f64-210">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="09f64-211">Nahraďte FirstMidName jméno a příjmení.</span><span class="sxs-lookup"><span data-stu-id="09f64-211">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="09f64-212">Následující kód ukazuje aktualizovanou stránku:</span><span class="sxs-lookup"><span data-stu-id="09f64-212">The following markup shows the updated page:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="09f64-213">Aktualizace modelu stránky úpravy</span><span class="sxs-lookup"><span data-stu-id="09f64-213">Update the Edit page model</span></span>

<span data-ttu-id="09f64-214">Aktualizace *Pages\Departments\Edit.cshtml.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="09f64-214">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="09f64-215">Ke zjištění problému souběžnosti, [původní hodnota](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) je aktualizován `rowVersion` hodnotu z entity se načetla.</span><span class="sxs-lookup"><span data-stu-id="09f64-215">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="09f64-216">EF Core generuje příkazu SQL UPDATE s klauzulí WHERE, který obsahuje původní `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="09f64-216">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="09f64-217">Pokud žádné řádky jsou ovlivněny příkazu UPDATE (žádné řádky mít původní `RowVersion` hodnota), `DbUpdateConcurrencyException` je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="09f64-217">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="09f64-218">V předchozím kódu `Department.RowVersion` je hodnota, pokud se entita načetla.</span><span class="sxs-lookup"><span data-stu-id="09f64-218">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="09f64-219">`OriginalValue` je hodnota v databázi při `FirstOrDefaultAsync` byla volána v této metodě.</span><span class="sxs-lookup"><span data-stu-id="09f64-219">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="09f64-220">Následující kód načte hodnoty klienta (hodnoty, publikuje se do této metody) a hodnoty DB:</span><span class="sxs-lookup"><span data-stu-id="09f64-220">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="09f64-221">Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má DB hodnoty liší od co byla publikována `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="09f64-221">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="09f64-222">Následující zvýrazněný kód nastaví `RowVersion` z databáze načíst hodnotu na novou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="09f64-222">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="09f64-223">Při příštím kliknutí na tlačítko **Uložit**, pouze souběžnosti chyby, ke kterým dochází, protože poslední zobrazení stránky pro úpravu bude zachycena.</span><span class="sxs-lookup"><span data-stu-id="09f64-223">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="09f64-224">`ModelState.Remove` Příkazu se totiž `ModelState` má starý `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="09f64-224">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="09f64-225">Na stránce Razor `ModelState` hodnota pole má přednost před hodnoty vlastností modelu Pokud jsou obě přítomny.</span><span class="sxs-lookup"><span data-stu-id="09f64-225">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="09f64-226">Aktualizace stránky pro úpravu</span><span class="sxs-lookup"><span data-stu-id="09f64-226">Update the Edit page</span></span>

<span data-ttu-id="09f64-227">Aktualizace *Pages/Departments/Edit.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="09f64-227">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="09f64-228">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="09f64-228">The preceding markup:</span></span>

* <span data-ttu-id="09f64-229">Aktualizace `page` direktiv z `@page` k `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="09f64-229">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="09f64-230">Přidá verze skryté řádku.</span><span class="sxs-lookup"><span data-stu-id="09f64-230">Adds a hidden row version.</span></span> <span data-ttu-id="09f64-231">`RowVersion` je nutné přidat tak příspěvek zpět váže hodnotu.</span><span class="sxs-lookup"><span data-stu-id="09f64-231">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="09f64-232">Zobrazí poslední bajt `RowVersion` pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="09f64-232">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="09f64-233">Nahradí `ViewData` pomocí silných `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="09f64-233">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="09f64-234">Testování je v konfliktu s stránky pro úpravu souběžnosti</span><span class="sxs-lookup"><span data-stu-id="09f64-234">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="09f64-235">Otevřete dvě instance prohlížeče úpravy na anglické oddělení:</span><span class="sxs-lookup"><span data-stu-id="09f64-235">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="09f64-236">Spusťte aplikaci a vyberte oddělení.</span><span class="sxs-lookup"><span data-stu-id="09f64-236">Run the app and select Departments.</span></span>
* <span data-ttu-id="09f64-237">Klikněte pravým tlačítkem myši **upravit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě**.</span><span class="sxs-lookup"><span data-stu-id="09f64-237">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="09f64-238">Na první kartě klikněte **upravit** hypertextového odkazu pro anglickou oddělení.</span><span class="sxs-lookup"><span data-stu-id="09f64-238">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="09f64-239">Záložkách prohlížeče dvě zobrazení stejné informace.</span><span class="sxs-lookup"><span data-stu-id="09f64-239">The two browser tabs display the same information.</span></span>

<span data-ttu-id="09f64-240">Změňte název na první záložce prohlížeče a klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="09f64-240">Change the name in the first browser tab and click **Save**.</span></span>

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="09f64-242">Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru.</span><span class="sxs-lookup"><span data-stu-id="09f64-242">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="09f64-243">Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.</span><span class="sxs-lookup"><span data-stu-id="09f64-243">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="09f64-244">Změňte jiné pole na druhé záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="09f64-244">Change a different field in the second browser tab.</span></span>

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="09f64-246">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="09f64-246">Click **Save**.</span></span> <span data-ttu-id="09f64-247">Zobrazí se chybové zprávy pro všechna pole, která se neshodují s hodnotami DB:</span><span class="sxs-lookup"><span data-stu-id="09f64-247">You see error messages for all fields that don't match the DB values:</span></span>

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error.png)

<span data-ttu-id="09f64-249">Toto okno prohlížeče neměli v úmyslu změnit název pole.</span><span class="sxs-lookup"><span data-stu-id="09f64-249">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="09f64-250">Zkopírujte a vložte do pole název aktuální hodnotu (jazyky).</span><span class="sxs-lookup"><span data-stu-id="09f64-250">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="09f64-251">Karta navýšení kapacity. Ověřování na straně klienta odebere chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="09f64-251">Tab out. Client-side validation removes the error message.</span></span>

![Oddělení upravit stránku chybová zpráva](concurrency/_static/cv.png)

<span data-ttu-id="09f64-253">Klikněte na tlačítko **Uložit** znovu.</span><span class="sxs-lookup"><span data-stu-id="09f64-253">Click **Save** again.</span></span> <span data-ttu-id="09f64-254">Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="09f64-254">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="09f64-255">Zobrazí uložené hodnoty v indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="09f64-255">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="09f64-256">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="09f64-256">Update the Delete page</span></span>

<span data-ttu-id="09f64-257">Aktualizace modelu odstranění stránky s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="09f64-257">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="09f64-258">Na stránce odstranit rozpozná konfliktů souběžnosti, pokud entita změněna po načtení.</span><span class="sxs-lookup"><span data-stu-id="09f64-258">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="09f64-259">`Department.RowVersion` verze řádku je, když se entita načetla.</span><span class="sxs-lookup"><span data-stu-id="09f64-259">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="09f64-260">EF Core vytvoří příkaz SQL DELETE, obsahuje klauzuli WHERE s `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="09f64-260">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="09f64-261">Pokud vliv na výsledky příkazu SQL odstranit v nulový počet řádků:</span><span class="sxs-lookup"><span data-stu-id="09f64-261">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="09f64-262">`RowVersion` v odstranit SQL příkaz neodpovídá `RowVersion` v databázi.</span><span class="sxs-lookup"><span data-stu-id="09f64-262">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="09f64-263">Je vyvolána výjimka DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="09f64-263">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="09f64-264">`OnGetAsync` volá se `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="09f64-264">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="09f64-265">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="09f64-265">Update the Delete page</span></span>

<span data-ttu-id="09f64-266">Aktualizace *Pages/Departments/Delete.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="09f64-266">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="09f64-267">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="09f64-267">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="09f64-268">Aktualizace `page` direktiv z `@page` k `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="09f64-268">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="09f64-269">Přidá chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="09f64-269">Adds an error message.</span></span>
* <span data-ttu-id="09f64-270">Nahradí celý název v FirstMidName **správce** pole.</span><span class="sxs-lookup"><span data-stu-id="09f64-270">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="09f64-271">Změny `RowVersion` k zobrazení poslední bajt.</span><span class="sxs-lookup"><span data-stu-id="09f64-271">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="09f64-272">Přidá verze skryté řádku.</span><span class="sxs-lookup"><span data-stu-id="09f64-272">Adds a hidden row version.</span></span> <span data-ttu-id="09f64-273">`RowVersion` je nutné přidat tak příspěvek zpět váže hodnotu.</span><span class="sxs-lookup"><span data-stu-id="09f64-273">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="09f64-274">Konflikty souběžnosti testu se stránkou Delete</span><span class="sxs-lookup"><span data-stu-id="09f64-274">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="09f64-275">Vytvořte test oddělení.</span><span class="sxs-lookup"><span data-stu-id="09f64-275">Create a test department.</span></span>

<span data-ttu-id="09f64-276">Otevřete dvě instance prohlížeče DELETE na oddělení testu:</span><span class="sxs-lookup"><span data-stu-id="09f64-276">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="09f64-277">Spusťte aplikaci a vyberte oddělení.</span><span class="sxs-lookup"><span data-stu-id="09f64-277">Run the app and select Departments.</span></span>
* <span data-ttu-id="09f64-278">Klikněte pravým tlačítkem myši **odstranit** hypertextového odkazu pro oddělení test a vyberte **otevřít na nové kartě**.</span><span class="sxs-lookup"><span data-stu-id="09f64-278">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="09f64-279">Klikněte na tlačítko **upravit** hypertextového odkazu pro oddělení testu.</span><span class="sxs-lookup"><span data-stu-id="09f64-279">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="09f64-280">Záložkách prohlížeče dvě zobrazení stejné informace.</span><span class="sxs-lookup"><span data-stu-id="09f64-280">The two browser tabs display the same information.</span></span>

<span data-ttu-id="09f64-281">Rozpočet na první záložce prohlížeče a klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="09f64-281">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="09f64-282">Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru.</span><span class="sxs-lookup"><span data-stu-id="09f64-282">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="09f64-283">Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.</span><span class="sxs-lookup"><span data-stu-id="09f64-283">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="09f64-284">Odstraňte test oddělení na druhé kartě. Došlo k chybě souběžnosti je zobrazení pomocí aktuálních hodnot z databáze.</span><span class="sxs-lookup"><span data-stu-id="09f64-284">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="09f64-285">Kliknutím na **odstranit** odstraní entitu, není-li `RowVersion` byl updated.department byl odstraněn.</span><span class="sxs-lookup"><span data-stu-id="09f64-285">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

<span data-ttu-id="09f64-286">Zobrazit [dědičnosti](xref:data/ef-mvc/inheritance) o tom, jak dědit datový model.</span><span class="sxs-lookup"><span data-stu-id="09f64-286">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="09f64-287">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="09f64-287">Additional resources</span></span>

* [<span data-ttu-id="09f64-288">Tokeny souběžnosti v EF Core</span><span class="sxs-lookup"><span data-stu-id="09f64-288">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="09f64-289">Popisovač souběžnosti v EF Core</span><span class="sxs-lookup"><span data-stu-id="09f64-289">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="09f64-290">YouTube verzi tohoto kurzu (zpracování konfliktů souběžnosti)</span><span class="sxs-lookup"><span data-stu-id="09f64-290">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="09f64-291">YouTube verzi tohoto kurzu (část 2)</span><span class="sxs-lookup"><span data-stu-id="09f64-291">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="09f64-292">YouTube verzi tohoto kurzu (část 3)</span><span class="sxs-lookup"><span data-stu-id="09f64-292">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="09f64-293">Předchozí</span><span class="sxs-lookup"><span data-stu-id="09f64-293">Previous</span></span>](xref:data/ef-rp/update-related-data)
