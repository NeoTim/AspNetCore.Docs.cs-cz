---
title: Razor Stránky s EF core v ASP.NET Core - Souběžnost - 8 z 8
author: rick-anderson
description: Tento kurz ukazuje, jak zpracovat konflikty, když více uživatelů aktualizovat stejnou entitu ve stejnou dobu.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: c4d43f26ba80e7922c3cbd37d9a5f8e1561b11ad
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656910"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a><span data-ttu-id="01bfd-103">Razor Stránky s EF core v ASP.NET Core - Souběžnost - 8 z 8</span><span class="sxs-lookup"><span data-stu-id="01bfd-103">Razor Pages with EF Core in ASP.NET Core - Concurrency - 8 of 8</span></span>

<span data-ttu-id="01bfd-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), a [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="01bfd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="01bfd-105">Tento kurz ukazuje, jak zpracovat konflikty, když více uživatelů aktualizovat entitu současně (ve stejnou dobu).</span><span class="sxs-lookup"><span data-stu-id="01bfd-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="01bfd-106">Konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="01bfd-106">Concurrency conflicts</span></span>

<span data-ttu-id="01bfd-107">Ke konfliktu souběžnosti dochází, když:</span><span class="sxs-lookup"><span data-stu-id="01bfd-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="01bfd-108">Uživatel přejde na stránku úprav entity.</span><span class="sxs-lookup"><span data-stu-id="01bfd-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="01bfd-109">Jiný uživatel aktualizuje stejnou entitu před zápisem změny prvního uživatele do databáze.</span><span class="sxs-lookup"><span data-stu-id="01bfd-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="01bfd-110">Pokud není povoleno zjišťování souběžnosti, ten, kdo aktualizuje databázi naposledy přepíše změny jiného uživatele.</span><span class="sxs-lookup"><span data-stu-id="01bfd-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="01bfd-111">Pokud je toto riziko přijatelné, náklady na programování souběžnosti mohou převážit nad přínosy.</span><span class="sxs-lookup"><span data-stu-id="01bfd-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="01bfd-112">Pesimistický souběžnost (zamykání)</span><span class="sxs-lookup"><span data-stu-id="01bfd-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="01bfd-113">Jedním ze způsobů, jak zabránit konfliktům souběžnosti, je použití uzamčení databáze.</span><span class="sxs-lookup"><span data-stu-id="01bfd-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="01bfd-114">Tomu se říká pesimistické souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="01bfd-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="01bfd-115">Předtím, než aplikace přečte řádek databáze, který má v úmyslu aktualizovat, požádá o zámek.</span><span class="sxs-lookup"><span data-stu-id="01bfd-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="01bfd-116">Jakmile je řádek uzamčen pro přístup k aktualizaci, žádný jiný uživatel nemá povoleno uzamknout řádek, dokud nebude vydán první zámek.</span><span class="sxs-lookup"><span data-stu-id="01bfd-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="01bfd-117">Správa zámků má nevýhody.</span><span class="sxs-lookup"><span data-stu-id="01bfd-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="01bfd-118">Může být složitý program a může způsobit problémy s výkonem, protože počet uživatelů se zvyšuje.</span><span class="sxs-lookup"><span data-stu-id="01bfd-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="01bfd-119">Entity Framework Core neposkytuje žádnou integrovanou podporu pro něj a tento kurz neukazuje, jak ji implementovat.</span><span class="sxs-lookup"><span data-stu-id="01bfd-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="01bfd-120">Optimistická souběžnost</span><span class="sxs-lookup"><span data-stu-id="01bfd-120">Optimistic concurrency</span></span>

<span data-ttu-id="01bfd-121">Optimistická souběžnost umožňuje konflikty souběžnosti a pak reaguje odpovídajícím způsobem, když to dělají.</span><span class="sxs-lookup"><span data-stu-id="01bfd-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="01bfd-122">Jana například navštíví stránku úprav oddělení a změní rozpočet pro anglické oddělení z 350 000,00 USD na 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="01bfd-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Změna rozpočtu na 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="01bfd-124">Před kliknutím na tlačítko **Uložit**jan navštíví stejnou stránku a změní pole Datum zahájení z 9.1.2007 na 1.</span><span class="sxs-lookup"><span data-stu-id="01bfd-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Změna počátečního data na rok 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="01bfd-126">Jana klikne na **Uložit** jako první a její změna se projeví, protože prohlížeč zobrazí jako částku rozpočtu stránku Rejstřík s nulou.</span><span class="sxs-lookup"><span data-stu-id="01bfd-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="01bfd-127">Jan klikne na **Uložit** na stránce Úpravy, která stále zobrazuje rozpočet 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="01bfd-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="01bfd-128">Co se stane dál, je určeno způsobem, jakým zpracováváte konflikty souběžnosti:</span><span class="sxs-lookup"><span data-stu-id="01bfd-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="01bfd-129">Můžete sledovat, kterou vlastnost uživatel upravil, a aktualizovat pouze odpovídající sloupce v databázi.</span><span class="sxs-lookup"><span data-stu-id="01bfd-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="01bfd-130">Ve scénáři by dojít ke ztrátě žádných dat.</span><span class="sxs-lookup"><span data-stu-id="01bfd-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="01bfd-131">Oba uživatelé aktualizovali různé vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="01bfd-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="01bfd-132">Až příště někdo prohledá anglické oddělení, uvidí změny Jane i Johna.</span><span class="sxs-lookup"><span data-stu-id="01bfd-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="01bfd-133">Tato metoda aktualizace může snížit počet konfliktů, které by mohly vést ke ztrátě dat.</span><span class="sxs-lookup"><span data-stu-id="01bfd-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="01bfd-134">Tento přístup má některé nevýhody:</span><span class="sxs-lookup"><span data-stu-id="01bfd-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="01bfd-135">Nelze se vyhnout ztrátě dat, pokud jsou ve stejné vlastnosti provedeny konkurenční změny.</span><span class="sxs-lookup"><span data-stu-id="01bfd-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="01bfd-136">Obecně není praktické ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="01bfd-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="01bfd-137">Vyžaduje udržování významného stavu, aby bylo možné sledovat všechny načtené hodnoty a nové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="01bfd-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="01bfd-138">Udržování velkého množství stavu může ovlivnit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="01bfd-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="01bfd-139">Může zvýšit složitost aplikace ve srovnání s detekcí souběžnosti na entitě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="01bfd-140">Můžeš nechat Johnovu změnu přepsat Janeinu změnu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="01bfd-141">Příště někdo prochází anglické oddělení, uvidí 9 / 1 / 2013 a přitažené za 350.000,00 dolarů hodnotu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="01bfd-142">Tento přístup se nazývá *klient wins* nebo poslední *ve wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="01bfd-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="01bfd-143">(Všechny hodnoty od klienta mají přednost před tím, co je v úložišti dat.) Pokud nechcete dělat žádné kódování pro zpracování souběžnosti, klient wins se stane automaticky.</span><span class="sxs-lookup"><span data-stu-id="01bfd-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="01bfd-144">Můžete zabránit janově změně v aktualizaci v databázi.</span><span class="sxs-lookup"><span data-stu-id="01bfd-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="01bfd-145">Aplikace by obvykle:</span><span class="sxs-lookup"><span data-stu-id="01bfd-145">Typically, the app would:</span></span>

  * <span data-ttu-id="01bfd-146">Zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-146">Display an error message.</span></span>
  * <span data-ttu-id="01bfd-147">Zobrazí aktuální stav dat.</span><span class="sxs-lookup"><span data-stu-id="01bfd-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="01bfd-148">Povolte uživateli znovu použít změny.</span><span class="sxs-lookup"><span data-stu-id="01bfd-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="01bfd-149">Tento scénář se nazývá scénář *Store Wins.*</span><span class="sxs-lookup"><span data-stu-id="01bfd-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="01bfd-150">(Hodnoty úložiště dat mají přednost před hodnotami odeslané klientem.) Implementovat úložiště wins scénář v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="01bfd-151">Tato metoda zajišťuje, že žádné změny jsou přepsány bez upozornění uživatele.</span><span class="sxs-lookup"><span data-stu-id="01bfd-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="01bfd-152">Zjišťování konfliktů v ef jádru</span><span class="sxs-lookup"><span data-stu-id="01bfd-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="01bfd-153">EF Core `DbConcurrencyException` vyvolá výjimky při zjišťování konfliktů.</span><span class="sxs-lookup"><span data-stu-id="01bfd-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="01bfd-154">Datový model musí být nakonfigurován tak, aby povoloval zjišťování konfliktů.</span><span class="sxs-lookup"><span data-stu-id="01bfd-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="01bfd-155">Možnosti povolení zjišťování konfliktů zahrnují následující:</span><span class="sxs-lookup"><span data-stu-id="01bfd-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="01bfd-156">Nakonfigurujte EF Core tak, aby zahrnoval původní hodnoty sloupců nakonfigurovaných jako [tokeny souběžnosti](/ef/core/modeling/concurrency) v příkazech Where update and Delete.</span><span class="sxs-lookup"><span data-stu-id="01bfd-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="01bfd-157">Když `SaveChanges` je volána, Where klauzule hledá původní hodnoty všechny vlastnosti s notated s [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="01bfd-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="01bfd-158">Příkaz update nenajde řádek aktualizovat, pokud některý z vlastností tokenu souběžnosti změnil od prvního čtení řádku.</span><span class="sxs-lookup"><span data-stu-id="01bfd-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="01bfd-159">EF Core interpretuje, že jako konflikt souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="01bfd-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="01bfd-160">Pro databázové tabulky, které mají mnoho sloupců, tento přístup může mít za následek velmi velké Where klauzule a může vyžadovat velké množství stavu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="01bfd-161">Proto tento přístup se obecně nedoporučuje a není metoda použitá v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="01bfd-162">V databázové tabulce zahrňte sledovací sloupec, který lze použít k určení, kdy byl řádek změněn.</span><span class="sxs-lookup"><span data-stu-id="01bfd-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="01bfd-163">V databázi serveru SQL Server je `rowversion`datový typ sledovacího sloupce .</span><span class="sxs-lookup"><span data-stu-id="01bfd-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="01bfd-164">Hodnota `rowversion` je pořadové číslo, které se při každé aktualizaci řádku zintáží.</span><span class="sxs-lookup"><span data-stu-id="01bfd-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="01bfd-165">V příkazu Aktualizovat nebo Odstranit obsahuje klauzule Where původní hodnotu sledovacího sloupce (původní číslo verze řádku).</span><span class="sxs-lookup"><span data-stu-id="01bfd-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="01bfd-166">Pokud byl řádek, který je aktualizován, změněn `rowversion` jiným uživatelem, hodnota ve sloupci se liší od původní hodnoty.</span><span class="sxs-lookup"><span data-stu-id="01bfd-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="01bfd-167">V takovém případě update nebo Delete prohlášení nelze najít řádek aktualizovat z důvodu Where klauzule.</span><span class="sxs-lookup"><span data-stu-id="01bfd-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="01bfd-168">EF Core vyvolá výjimku souběžnosti, když žádné řádky jsou ovlivněny Update nebo Delete příkaz.</span><span class="sxs-lookup"><span data-stu-id="01bfd-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="01bfd-169">Přidání vlastnosti sledování</span><span class="sxs-lookup"><span data-stu-id="01bfd-169">Add a tracking property</span></span>

<span data-ttu-id="01bfd-170">V *souboru Models/Department.cs*přidejte vlastnost sledování s názvem RowVersion:</span><span class="sxs-lookup"><span data-stu-id="01bfd-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="01bfd-171">Atribut [časové razítko](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) je co identifikuje sloupec jako sloupec sledování souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="01bfd-171">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="01bfd-172">Fluent API je alternativní způsob, jak určit vlastnost sledování:</span><span class="sxs-lookup"><span data-stu-id="01bfd-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="01bfd-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01bfd-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="01bfd-174">Pro databázi serveru `[Timestamp]` SQL Server atribut vlastnosti entity definované jako bajtové pole:</span><span class="sxs-lookup"><span data-stu-id="01bfd-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="01bfd-175">Způsobí, že sloupec, které mají být zahrnuty do DELETE a UPDATE WHERE klauzule.</span><span class="sxs-lookup"><span data-stu-id="01bfd-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="01bfd-176">Nastaví typ sloupce v databázi na [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="01bfd-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="01bfd-177">Databáze generuje číslo verze sekvenční řádek, který se zintácí při každé aktualizaci řádku.</span><span class="sxs-lookup"><span data-stu-id="01bfd-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="01bfd-178">V `Update` příkazu `Delete` nebo `Where` klauzule obsahuje načtenou hodnotu verze řádku.</span><span class="sxs-lookup"><span data-stu-id="01bfd-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="01bfd-179">Pokud se aktualizovaný řádek od načtení změnil:</span><span class="sxs-lookup"><span data-stu-id="01bfd-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="01bfd-180">Aktuální hodnota verze řádku neodpovídá načtené hodnotě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="01bfd-181">`Update` Příkazy `Delete` nebo nenajdou řádek, `Where` protože klauzule hledá hodnotu načtené verze řádku.</span><span class="sxs-lookup"><span data-stu-id="01bfd-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="01bfd-182">A `DbUpdateConcurrencyException` je hozen.</span><span class="sxs-lookup"><span data-stu-id="01bfd-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="01bfd-183">Následující kód ukazuje část T-SQL generované EF Core při aktualizaci názvu oddělení:</span><span class="sxs-lookup"><span data-stu-id="01bfd-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="01bfd-184">Předchozí zvýrazněný kód zobrazuje `WHERE` klauzuli `RowVersion`obsahující .</span><span class="sxs-lookup"><span data-stu-id="01bfd-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="01bfd-185">Pokud se `RowVersion` databáze nerovná `RowVersion` parametru (`@p2`), nebudou aktualizovány žádné řádky.</span><span class="sxs-lookup"><span data-stu-id="01bfd-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="01bfd-186">Následující zvýrazněný kód ukazuje T-SQL, který ověřuje přesně jeden řádek byl aktualizován:</span><span class="sxs-lookup"><span data-stu-id="01bfd-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="01bfd-187">[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) vrátí počet řádků ovlivněných posledním příkazem.</span><span class="sxs-lookup"><span data-stu-id="01bfd-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="01bfd-188">Pokud nejsou aktualizovány žádné řádky, `DbUpdateConcurrencyException`EF Core vyvolá .</span><span class="sxs-lookup"><span data-stu-id="01bfd-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="01bfd-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01bfd-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="01bfd-190">Pro databázi SQLite `[Timestamp]` atribut vlastnosti entity definované jako bajtové pole:</span><span class="sxs-lookup"><span data-stu-id="01bfd-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="01bfd-191">Způsobí, že sloupec, které mají být zahrnuty do DELETE a UPDATE WHERE klauzule.</span><span class="sxs-lookup"><span data-stu-id="01bfd-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="01bfd-192">Mapuje na typ sloupce BLOB.</span><span class="sxs-lookup"><span data-stu-id="01bfd-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="01bfd-193">Databáze aktivační události aktualizovat RowVersion sloupec s novým náhodným bajt pole při každé aktualizaci řádku.</span><span class="sxs-lookup"><span data-stu-id="01bfd-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="01bfd-194">V `Update` příkazu `Delete` `Where` nebo klauzule obsahuje načtenou hodnotu rowversion sloupce.</span><span class="sxs-lookup"><span data-stu-id="01bfd-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="01bfd-195">Pokud se aktualizovaný řádek od načtení změnil:</span><span class="sxs-lookup"><span data-stu-id="01bfd-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="01bfd-196">Aktuální hodnota verze řádku neodpovídá načtené hodnotě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="01bfd-197">Příkaz `Update` `Delete` or řádek nenajde, protože `Where` klauzule hledá původní hodnotu verze řádku.</span><span class="sxs-lookup"><span data-stu-id="01bfd-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="01bfd-198">A `DbUpdateConcurrencyException` je hozen.</span><span class="sxs-lookup"><span data-stu-id="01bfd-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="01bfd-199">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="01bfd-199">Update the database</span></span>

<span data-ttu-id="01bfd-200">Přidánívlastnosti `RowVersion` změní datový model, který vyžaduje migraci.</span><span class="sxs-lookup"><span data-stu-id="01bfd-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="01bfd-201">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="01bfd-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="01bfd-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01bfd-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="01bfd-203">Spusťte následující příkaz v PMC:</span><span class="sxs-lookup"><span data-stu-id="01bfd-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="01bfd-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01bfd-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="01bfd-205">V terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="01bfd-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="01bfd-206">Tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="01bfd-206">This command:</span></span>

* <span data-ttu-id="01bfd-207">Vytvoří migrační soubor *Migrace/{time stamp}_RowVersion.cs.*</span><span class="sxs-lookup"><span data-stu-id="01bfd-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="01bfd-208">Aktualizuje soubor *Migrations/SchoolContextModelSnapshot.cs.*</span><span class="sxs-lookup"><span data-stu-id="01bfd-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="01bfd-209">Aktualizace přidá do `BuildModel` metody následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="01bfd-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="01bfd-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01bfd-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="01bfd-211">Spusťte následující příkaz v PMC:</span><span class="sxs-lookup"><span data-stu-id="01bfd-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="01bfd-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01bfd-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="01bfd-213">Otevřete `Migrations/<timestamp>_RowVersion.cs` soubor a přidejte zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="01bfd-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="01bfd-214">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="01bfd-214">The preceding code:</span></span>

  * <span data-ttu-id="01bfd-215">Aktualizuje existující řádky s náhodnými hodnotami objektů blob.</span><span class="sxs-lookup"><span data-stu-id="01bfd-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="01bfd-216">Přidá aktivační události databáze, které nastaví sloupec RowVersion na hodnotu náhodného objektu blob při každé aktualizaci řádku.</span><span class="sxs-lookup"><span data-stu-id="01bfd-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="01bfd-217">V terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="01bfd-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="01bfd-218">Stránky oddělení lešení</span><span class="sxs-lookup"><span data-stu-id="01bfd-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="01bfd-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01bfd-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="01bfd-220">Postupujte podle pokynů na [stránkách Student lešení](xref:data/ef-rp/intro#scaffold-student-pages) s následujícími výjimkami:</span><span class="sxs-lookup"><span data-stu-id="01bfd-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="01bfd-221">Vytvořte složku *Stránky/oddělení.*</span><span class="sxs-lookup"><span data-stu-id="01bfd-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="01bfd-222">Slouží `Department` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="01bfd-223">Místo vytvoření nové třídy kontextu použijte existující třídu kontextu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="01bfd-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01bfd-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="01bfd-225">Vytvořte složku *Stránky/oddělení.*</span><span class="sxs-lookup"><span data-stu-id="01bfd-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="01bfd-226">Spusťte následující příkaz pro zakládání uživatelského lístku na stránkách oddělení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="01bfd-227">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="01bfd-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="01bfd-228">**Na Linuxu nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="01bfd-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="01bfd-229">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="01bfd-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="01bfd-230">Aktualizace stránky Rejstřík</span><span class="sxs-lookup"><span data-stu-id="01bfd-230">Update the Index page</span></span>

<span data-ttu-id="01bfd-231">Nástroj pro vytváření uživatelského `RowVersion` okna vytvořil sloupec pro stránku Index, ale toto pole by se v produkční aplikaci nezobrazovalo.</span><span class="sxs-lookup"><span data-stu-id="01bfd-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="01bfd-232">V tomto kurzu `RowVersion` je zobrazen poslední bajt, který vám pomůže zobrazit, jak funguje zpracování souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="01bfd-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="01bfd-233">Poslední bajt není zaručeno, že bude jedinečný sám o sobě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="01bfd-234">Aktualizovat *stránky\Departments\Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="01bfd-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="01bfd-235">Nahraďte index odděleními.</span><span class="sxs-lookup"><span data-stu-id="01bfd-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="01bfd-236">Změňte kód `RowVersion` obsahující tak, aby zobrazoval pouze poslední bajt bajtového pole.</span><span class="sxs-lookup"><span data-stu-id="01bfd-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="01bfd-237">Nahraďte FirstMidName fullname.</span><span class="sxs-lookup"><span data-stu-id="01bfd-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="01bfd-238">Následující kód zobrazuje aktualizovanou stránku:</span><span class="sxs-lookup"><span data-stu-id="01bfd-238">The following code shows the updated page:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="01bfd-239">Aktualizace modelu stránky Úpravy</span><span class="sxs-lookup"><span data-stu-id="01bfd-239">Update the Edit page model</span></span>

<span data-ttu-id="01bfd-240">Aktualizovat *stránky\Departments\Edit.cshtml.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="01bfd-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="01bfd-241">[OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) je aktualizován `rowVersion` s hodnotou z entity, když `OnGet` byl načten v metodě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-241">The [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="01bfd-242">EF Core generuje příkaz SQL UPDATE s klauzulí `RowVersion` WHERE obsahující původní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="01bfd-243">Pokud žádný řádek jsou ovlivněny příkazem UPDATE `RowVersion` (žádné `DbUpdateConcurrencyException` řádky mají původní hodnotu), je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="01bfd-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="01bfd-244">V předchozím zvýrazněném kódu:</span><span class="sxs-lookup"><span data-stu-id="01bfd-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="01bfd-245">Hodnota v `Department.RowVersion` je to, co bylo v entitě, když byla původně načtena na stránce Získat požadavek na úpravy.</span><span class="sxs-lookup"><span data-stu-id="01bfd-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="01bfd-246">Hodnota je poskytována `OnPost` metodě skrytým polem na stránce Razor, které zobrazuje entitu, která má být upravena.</span><span class="sxs-lookup"><span data-stu-id="01bfd-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="01bfd-247">Hodnota skrytého pole je `Department.RowVersion` zkopírována pořadačem modelu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="01bfd-248">`OriginalValue`je to, co EF Core použije v klauzuli Kde.</span><span class="sxs-lookup"><span data-stu-id="01bfd-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="01bfd-249">Před zvýrazněný řádek kódu spustí, má hodnotu, `OriginalValue` která `FirstOrDefaultAsync` byla v databázi, když byl volán v této metodě, které se mohou lišit od toho, co bylo zobrazeno na stránce Upravit.</span><span class="sxs-lookup"><span data-stu-id="01bfd-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="01bfd-250">Zvýrazněný kód zajišťuje, že EF `RowVersion` Core používá původní `Department` hodnotu ze zobrazené entity v klauzuli Where příkazu SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="01bfd-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="01bfd-251">Když dojde k chybě souběžnosti, následující zvýrazněný kód získá hodnoty klienta (hodnoty zaúčtované do této metody) a hodnoty databáze.</span><span class="sxs-lookup"><span data-stu-id="01bfd-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="01bfd-252">Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který `OnPostAsync`má hodnoty databáze odlišné od toho, co bylo zaúčtováno do :</span><span class="sxs-lookup"><span data-stu-id="01bfd-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="01bfd-253">Následující zvýrazněný kód `RowVersion` nastaví hodnotu na novou hodnotu načtenou z databáze.</span><span class="sxs-lookup"><span data-stu-id="01bfd-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="01bfd-254">Při příštím kliknutí uživatele na **uložit**budou zachyceny pouze chyby souběžnosti, ke kterým dojde od posledního zobrazení stránky Úpravy.</span><span class="sxs-lookup"><span data-stu-id="01bfd-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="01bfd-255">Příkaz `ModelState.Remove` je povinný, `ModelState` protože `RowVersion` má starou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="01bfd-256">Na stránce holicí strojek má `ModelState` hodnota pole přednost před hodnotami vlastností modelu, pokud jsou k dispozici obě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-razor-page"></a><span data-ttu-id="01bfd-257">Aktualizace stránky Razor</span><span class="sxs-lookup"><span data-stu-id="01bfd-257">Update the Razor page</span></span>

<span data-ttu-id="01bfd-258">Aktualizovat *stránky/oddělení/Edit.cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="01bfd-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="01bfd-259">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="01bfd-259">The preceding code:</span></span>

* <span data-ttu-id="01bfd-260">Aktualizuje `page` direktivu z `@page` na . `@page "{id:int}"`</span><span class="sxs-lookup"><span data-stu-id="01bfd-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="01bfd-261">Přidá verzi skrytého řádku.</span><span class="sxs-lookup"><span data-stu-id="01bfd-261">Adds a hidden row version.</span></span> <span data-ttu-id="01bfd-262">`RowVersion`musí být přidán, takže post zpět váže hodnotu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-262">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="01bfd-263">Zobrazí poslední bajt `RowVersion` pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="01bfd-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="01bfd-264">Nahradí `ViewData` se silným typem `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="01bfd-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="01bfd-265">Testování konfliktů souběžnosti se stránkou Úpravy</span><span class="sxs-lookup"><span data-stu-id="01bfd-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="01bfd-266">Otevřete dvě instance prohlížeče Upravit na anglickém oddělení:</span><span class="sxs-lookup"><span data-stu-id="01bfd-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="01bfd-267">Spusťte aplikaci a vyberte Oddělení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="01bfd-268">Klikněte pravým tlačítkem myši na hypertextový odkaz **Upravit** pro anglické oddělení a **na nové kartě**vyberte otevřít .</span><span class="sxs-lookup"><span data-stu-id="01bfd-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="01bfd-269">Na první kartě klikněte na hypertextový odkaz **Upravit** pro anglické oddělení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="01bfd-270">Na dvou kartách prohlížeče se zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="01bfd-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="01bfd-271">Změňte název na první kartě prohlížeče a klepněte na **tlačítko Uložit**.</span><span class="sxs-lookup"><span data-stu-id="01bfd-271">Change the name in the first browser tab and click **Save**.</span></span>

![Oddělení Upravit stránku 1 po změně](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="01bfd-273">Prohlížeč zobrazí stránku Index se změněnou hodnotou a aktualizovaným indikátorem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="01bfd-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="01bfd-274">Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazí na druhém postbacku na druhé kartě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="01bfd-275">Změňte jiné pole na druhé kartě prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="01bfd-275">Change a different field in the second browser tab.</span></span>

![Oddělení Upravit stránku 2 po změně](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="01bfd-277">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="01bfd-277">Click **Save**.</span></span> <span data-ttu-id="01bfd-278">Zobrazí se chybové zprávy pro všechna pole, která neodpovídají hodnotám databáze:</span><span class="sxs-lookup"><span data-stu-id="01bfd-278">You see error messages for all fields that don't match the database values:</span></span>

![Chybová zpráva Upravit stránku oddělení](concurrency/_static/edit-error30.png)

<span data-ttu-id="01bfd-280">Toto okno prohlížeče nemělo v úmyslu změnit pole Název.</span><span class="sxs-lookup"><span data-stu-id="01bfd-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="01bfd-281">Zkopírujte a vložte aktuální hodnotu (jazyky) do pole Název.</span><span class="sxs-lookup"><span data-stu-id="01bfd-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="01bfd-282">Vyjděte si. Ověření na straně klienta odebere chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="01bfd-283">Klikněte znovu na **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="01bfd-283">Click **Save** again.</span></span> <span data-ttu-id="01bfd-284">Hodnota zadaná na druhé kartě prohlížeče se uloží.</span><span class="sxs-lookup"><span data-stu-id="01bfd-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="01bfd-285">Uložené hodnoty se zobrazí na stránce Rejstřík.</span><span class="sxs-lookup"><span data-stu-id="01bfd-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="01bfd-286">Aktualizace stránky Odstranit</span><span class="sxs-lookup"><span data-stu-id="01bfd-286">Update the Delete page</span></span>

<span data-ttu-id="01bfd-287">Aktualizujte *stránky/oddělení/Delete.cshtml.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="01bfd-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="01bfd-288">Stránka Odstranit zjistí konflikty souběžnosti, když se entita po načtení změnila.</span><span class="sxs-lookup"><span data-stu-id="01bfd-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="01bfd-289">`Department.RowVersion`je verze řádku při načítání entity.</span><span class="sxs-lookup"><span data-stu-id="01bfd-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="01bfd-290">Když EF Core vytvoří příkaz SQL DELETE, `RowVersion`obsahuje klauzuli WHERE s .</span><span class="sxs-lookup"><span data-stu-id="01bfd-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="01bfd-291">Pokud příkaz SQL DELETE má za následek nula řádků ovlivněny:</span><span class="sxs-lookup"><span data-stu-id="01bfd-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="01bfd-292">Příkaz `RowVersion` DELETE v sql delete `RowVersion` se neshoduje v databázi.</span><span class="sxs-lookup"><span data-stu-id="01bfd-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="01bfd-293">Je vyvolána výjimka DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="01bfd-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="01bfd-294">`OnGetAsync`je volána `concurrencyError`s .</span><span class="sxs-lookup"><span data-stu-id="01bfd-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="01bfd-295">Aktualizace stránky Odstranit holicí strojek</span><span class="sxs-lookup"><span data-stu-id="01bfd-295">Update the Delete Razor page</span></span>

<span data-ttu-id="01bfd-296">Aktualizovat *stránky/oddělení/Delete.cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="01bfd-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="01bfd-297">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="01bfd-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="01bfd-298">Aktualizuje `page` direktivu z `@page` na . `@page "{id:int}"`</span><span class="sxs-lookup"><span data-stu-id="01bfd-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="01bfd-299">Přidá chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-299">Adds an error message.</span></span>
* <span data-ttu-id="01bfd-300">Nahradí firstmidname v poli **Správce** názvem FullName.</span><span class="sxs-lookup"><span data-stu-id="01bfd-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="01bfd-301">Změní `RowVersion` zobrazení posledního bajtu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="01bfd-302">Přidá verzi skrytého řádku.</span><span class="sxs-lookup"><span data-stu-id="01bfd-302">Adds a hidden row version.</span></span> <span data-ttu-id="01bfd-303">`RowVersion`musí být přidán, takže postgit add back váže hodnotu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-303">`RowVersion` must be added so postgit add back binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="01bfd-304">Testovat konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="01bfd-304">Test concurrency conflicts</span></span>

<span data-ttu-id="01bfd-305">Vytvořte testovací oddělení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-305">Create a test department.</span></span>

<span data-ttu-id="01bfd-306">Otevřete dvě instance prohlížeče Delete na testovacím oddělení:</span><span class="sxs-lookup"><span data-stu-id="01bfd-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="01bfd-307">Spusťte aplikaci a vyberte Oddělení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="01bfd-308">Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** testovacího oddělení a **na nové kartě**vyberte otevřít .</span><span class="sxs-lookup"><span data-stu-id="01bfd-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="01bfd-309">Klikněte na hypertextový odkaz **Upravit** testovacího oddělení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="01bfd-310">Na dvou kartách prohlížeče se zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="01bfd-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="01bfd-311">Změňte rozpočet na první kartě prohlížeče a klepněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="01bfd-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="01bfd-312">Prohlížeč zobrazí stránku Index se změněnou hodnotou a aktualizovaným indikátorem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="01bfd-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="01bfd-313">Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazí na druhém postbacku na druhé kartě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="01bfd-314">Odstraňte testovací oddělení z druhé karty. Chyba souběžnosti se zobrazí s aktuálními hodnotami z databáze.</span><span class="sxs-lookup"><span data-stu-id="01bfd-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="01bfd-315">Kliknutím na **Odstranit** odstraníte `RowVersion` entitu, pokud nebyla aktualizována.oddělení bylo odstraněno.</span><span class="sxs-lookup"><span data-stu-id="01bfd-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01bfd-316">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="01bfd-316">Additional resources</span></span>

* [<span data-ttu-id="01bfd-317">Tokeny souběžnosti v jádru EF</span><span class="sxs-lookup"><span data-stu-id="01bfd-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="01bfd-318">Zpracování souběžnosti v jádru EF</span><span class="sxs-lookup"><span data-stu-id="01bfd-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="01bfd-319">Ladění ASP.NET zdroj Core 2.x</span><span class="sxs-lookup"><span data-stu-id="01bfd-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="01bfd-320">Další kroky</span><span class="sxs-lookup"><span data-stu-id="01bfd-320">Next steps</span></span>

<span data-ttu-id="01bfd-321">Toto je poslední výukový program v sérii.</span><span class="sxs-lookup"><span data-stu-id="01bfd-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="01bfd-322">Další témata jsou popsána ve [verzi MVC této série kurzů](xref:data/ef-mvc/index).</span><span class="sxs-lookup"><span data-stu-id="01bfd-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="01bfd-323">Předchozí kurz</span><span class="sxs-lookup"><span data-stu-id="01bfd-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="01bfd-324">Tento kurz ukazuje, jak zpracovat konflikty, když více uživatelů aktualizovat entitu současně (ve stejnou dobu).</span><span class="sxs-lookup"><span data-stu-id="01bfd-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="01bfd-325">Pokud narazíte na problémy, které nemůžete vyřešit, [stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="01bfd-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="01bfd-326">[Stáhnout pokyny](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="01bfd-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="01bfd-327">Konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="01bfd-327">Concurrency conflicts</span></span>

<span data-ttu-id="01bfd-328">Ke konfliktu souběžnosti dochází, když:</span><span class="sxs-lookup"><span data-stu-id="01bfd-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="01bfd-329">Uživatel přejde na stránku úprav entity.</span><span class="sxs-lookup"><span data-stu-id="01bfd-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="01bfd-330">Jiný uživatel aktualizuje stejnou entitu před první změna uživatele je zapsána do DB.</span><span class="sxs-lookup"><span data-stu-id="01bfd-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="01bfd-331">Pokud není povoleno zjišťování souběžnosti, pokud dojde k souběžným aktualizacím:</span><span class="sxs-lookup"><span data-stu-id="01bfd-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="01bfd-332">Poslední aktualizace vyhrává.</span><span class="sxs-lookup"><span data-stu-id="01bfd-332">The last update wins.</span></span> <span data-ttu-id="01bfd-333">To znamená, že poslední hodnoty aktualizace jsou uloženy do DB.</span><span class="sxs-lookup"><span data-stu-id="01bfd-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="01bfd-334">První z aktuálních aktualizací jsou ztraceny.</span><span class="sxs-lookup"><span data-stu-id="01bfd-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="01bfd-335">Optimistická souběžnost</span><span class="sxs-lookup"><span data-stu-id="01bfd-335">Optimistic concurrency</span></span>

<span data-ttu-id="01bfd-336">Optimistická souběžnost umožňuje konflikty souběžnosti a pak reaguje odpovídajícím způsobem, když to dělají.</span><span class="sxs-lookup"><span data-stu-id="01bfd-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="01bfd-337">Jana například navštíví stránku úprav oddělení a změní rozpočet pro anglické oddělení z 350 000,00 USD na 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="01bfd-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="01bfd-339">Před kliknutím na tlačítko **Uložit**jan navštíví stejnou stránku a změní pole Datum zahájení z 9.1.2007 na 1.</span><span class="sxs-lookup"><span data-stu-id="01bfd-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Změna počátečního data na rok 2013](concurrency/_static/change-date.png)

<span data-ttu-id="01bfd-341">Jan klikne na **Uložit** jako první a uvidí její změnu, když prohlížeč zobrazí stránku Rejstřík.</span><span class="sxs-lookup"><span data-stu-id="01bfd-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Rozpočet změněn na nulu](concurrency/_static/budget-zero.png)

<span data-ttu-id="01bfd-343">Jan klikne na **Uložit** na stránce Úpravy, která stále zobrazuje rozpočet 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="01bfd-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="01bfd-344">Co se stane dál, je určena tím, jak zpracovat konflikty souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="01bfd-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="01bfd-345">Optimistická souběžnost zahrnuje následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="01bfd-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="01bfd-346">Můžete sledovat, kterou vlastnost uživatel upravil, a aktualizovat pouze odpovídající sloupce v databázi.</span><span class="sxs-lookup"><span data-stu-id="01bfd-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="01bfd-347">Ve scénáři by dojít ke ztrátě žádných dat.</span><span class="sxs-lookup"><span data-stu-id="01bfd-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="01bfd-348">Oba uživatelé aktualizovali různé vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="01bfd-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="01bfd-349">Až příště někdo prohledá anglické oddělení, uvidí změny Jane i Johna.</span><span class="sxs-lookup"><span data-stu-id="01bfd-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="01bfd-350">Tato metoda aktualizace může snížit počet konfliktů, které by mohly vést ke ztrátě dat.</span><span class="sxs-lookup"><span data-stu-id="01bfd-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="01bfd-351">Tento přístup:</span><span class="sxs-lookup"><span data-stu-id="01bfd-351">This approach:</span></span>
 
  * <span data-ttu-id="01bfd-352">Nelze se vyhnout ztrátě dat, pokud jsou ve stejné vlastnosti provedeny konkurenční změny.</span><span class="sxs-lookup"><span data-stu-id="01bfd-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="01bfd-353">Obecně není praktické ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="01bfd-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="01bfd-354">Vyžaduje udržování významného stavu, aby bylo možné sledovat všechny načtené hodnoty a nové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="01bfd-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="01bfd-355">Udržování velkého množství stavu může ovlivnit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="01bfd-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="01bfd-356">Může zvýšit složitost aplikace ve srovnání s detekcí souběžnosti na entitě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="01bfd-357">Můžeš nechat Johnovu změnu přepsat Janeinu změnu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="01bfd-358">Příště někdo prochází anglické oddělení, uvidí 9 / 1 / 2013 a přitažené za 350.000,00 dolarů hodnotu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="01bfd-359">Tento přístup se nazývá *klient wins* nebo poslední *ve wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="01bfd-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="01bfd-360">(Všechny hodnoty od klienta mají přednost před tím, co je v úložišti dat.) Pokud nechcete dělat žádné kódování pro zpracování souběžnosti, klient wins se stane automaticky.</span><span class="sxs-lookup"><span data-stu-id="01bfd-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="01bfd-361">Můžete zabránit Janově změně v aktualizaci v databázi.</span><span class="sxs-lookup"><span data-stu-id="01bfd-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="01bfd-362">Aplikace by obvykle:</span><span class="sxs-lookup"><span data-stu-id="01bfd-362">Typically, the app would:</span></span>

  * <span data-ttu-id="01bfd-363">Zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-363">Display an error message.</span></span>
  * <span data-ttu-id="01bfd-364">Zobrazí aktuální stav dat.</span><span class="sxs-lookup"><span data-stu-id="01bfd-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="01bfd-365">Povolte uživateli znovu použít změny.</span><span class="sxs-lookup"><span data-stu-id="01bfd-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="01bfd-366">Tento scénář se nazývá scénář *Store Wins.*</span><span class="sxs-lookup"><span data-stu-id="01bfd-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="01bfd-367">(Hodnoty úložiště dat mají přednost před hodnotami odeslané klientem.) Implementovat úložiště wins scénář v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="01bfd-368">Tato metoda zajišťuje, že žádné změny jsou přepsány bez upozornění uživatele.</span><span class="sxs-lookup"><span data-stu-id="01bfd-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="01bfd-369">Zpracování souběžnosti</span><span class="sxs-lookup"><span data-stu-id="01bfd-369">Handling concurrency</span></span> 

<span data-ttu-id="01bfd-370">Když je vlastnost nakonfigurována jako [token souběžnosti](/ef/core/modeling/concurrency):</span><span class="sxs-lookup"><span data-stu-id="01bfd-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="01bfd-371">EF Core ověří, že vlastnost nebyla změněna po načtení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="01bfd-372">Ke kontrole dochází při [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) nebo [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) je volána.</span><span class="sxs-lookup"><span data-stu-id="01bfd-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="01bfd-373">Pokud vlastnost byla změněna po načtení, [dbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="01bfd-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) is thrown.</span></span> 

<span data-ttu-id="01bfd-374">DB a datový model musí být `DbUpdateConcurrencyException`nakonfigurovántak, aby podporoval y házení .</span><span class="sxs-lookup"><span data-stu-id="01bfd-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="01bfd-375">Zjišťování konfliktů souběžnosti ve vlastnosti</span><span class="sxs-lookup"><span data-stu-id="01bfd-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="01bfd-376">Konflikty souběžnosti lze zjistit na úrovni vlastnosti s atributem [ConcurrencyCheck.](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0)</span><span class="sxs-lookup"><span data-stu-id="01bfd-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribute.</span></span> <span data-ttu-id="01bfd-377">Atribut lze použít na více vlastností v modelu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="01bfd-378">Další informace naleznete [v tématu Data Anotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="01bfd-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="01bfd-379">Atribut `[ConcurrencyCheck]` se v tomto kurzu nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="01bfd-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="01bfd-380">Zjišťování konfliktů souběžnosti na řádku</span><span class="sxs-lookup"><span data-stu-id="01bfd-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="01bfd-381">Chcete-li zjistit konflikty souběžnosti, sloupec sledování [verze řádků](/sql/t-sql/data-types/rowversion-transact-sql) je přidán do modelu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="01bfd-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="01bfd-382">`rowversion` :</span></span>

* <span data-ttu-id="01bfd-383">Je sql server specifický.</span><span class="sxs-lookup"><span data-stu-id="01bfd-383">Is SQL Server specific.</span></span> <span data-ttu-id="01bfd-384">Jiné databáze nemusí poskytovat podobnou funkci.</span><span class="sxs-lookup"><span data-stu-id="01bfd-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="01bfd-385">Používá se k určení, že entita nebyla změněna od doby, kdy byla načtena z DB.</span><span class="sxs-lookup"><span data-stu-id="01bfd-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="01bfd-386">DB generuje pořadové `rowversion` číslo, které se při každé aktualizaci řádku zintátáží.</span><span class="sxs-lookup"><span data-stu-id="01bfd-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="01bfd-387">V `Update` příkazu `Delete` nebo `Where` klauzule obsahuje načtenou hodnotu `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="01bfd-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="01bfd-388">Pokud se aktualizovaný řádek změnil:</span><span class="sxs-lookup"><span data-stu-id="01bfd-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="01bfd-389">`rowversion`neodpovídá načtené hodnotě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="01bfd-390">`Update` Příkazy `Delete` nebo nenajdou řádek, `Where` protože klauzule `rowversion`obsahuje načtené .</span><span class="sxs-lookup"><span data-stu-id="01bfd-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="01bfd-391">A `DbUpdateConcurrencyException` je hozen.</span><span class="sxs-lookup"><span data-stu-id="01bfd-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="01bfd-392">V EF Core, když žádné řádky `Update` `Delete` byly aktualizovány příkazem nebo, je vyvolána výjimka souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="01bfd-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="01bfd-393">Přidání vlastnosti sledování do entity Oddělení</span><span class="sxs-lookup"><span data-stu-id="01bfd-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="01bfd-394">V *souboru Models/Department.cs*přidejte vlastnost sledování s názvem RowVersion:</span><span class="sxs-lookup"><span data-stu-id="01bfd-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="01bfd-395">Atribut [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) určuje, že tento sloupec `Where` je `Update` `Delete` součástí klauzule a příkazy.</span><span class="sxs-lookup"><span data-stu-id="01bfd-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="01bfd-396">Atribut je `Timestamp` volán, protože předchozí verze `timestamp` serveru SQL Server `rowversion` používaly datový typ SQL předtím, než jej nahradil typ SQL.</span><span class="sxs-lookup"><span data-stu-id="01bfd-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="01bfd-397">Fluent API můžete také určit vlastnost sledování:</span><span class="sxs-lookup"><span data-stu-id="01bfd-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="01bfd-398">Následující kód ukazuje část T-SQL generované EF Core při aktualizaci názvu oddělení:</span><span class="sxs-lookup"><span data-stu-id="01bfd-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="01bfd-399">Předchozí zvýrazněný kód zobrazuje `WHERE` klauzuli `RowVersion`obsahující .</span><span class="sxs-lookup"><span data-stu-id="01bfd-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="01bfd-400">Pokud DB `RowVersion` nerovná `RowVersion` parametr (`@p2`), žádné řádky jsou aktualizovány.</span><span class="sxs-lookup"><span data-stu-id="01bfd-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="01bfd-401">Následující zvýrazněný kód ukazuje T-SQL, který ověřuje přesně jeden řádek byl aktualizován:</span><span class="sxs-lookup"><span data-stu-id="01bfd-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="01bfd-402">[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) vrátí počet řádků ovlivněných posledním příkazem.</span><span class="sxs-lookup"><span data-stu-id="01bfd-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="01bfd-403">V žádné řádky jsou aktualizovány `DbUpdateConcurrencyException`EF Core vyvolá .</span><span class="sxs-lookup"><span data-stu-id="01bfd-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="01bfd-404">Můžete vidět T-SQL EF Core generuje ve výstupním okně Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="01bfd-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="01bfd-405">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="01bfd-405">Update the DB</span></span>

<span data-ttu-id="01bfd-406">Přidání `RowVersion` vlastnosti změní model DB, který vyžaduje migraci.</span><span class="sxs-lookup"><span data-stu-id="01bfd-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="01bfd-407">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="01bfd-407">Build the project.</span></span> <span data-ttu-id="01bfd-408">Do příkazového okna zadejte následující:</span><span class="sxs-lookup"><span data-stu-id="01bfd-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="01bfd-409">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="01bfd-409">The preceding commands:</span></span>

* <span data-ttu-id="01bfd-410">Přidá migrační soubor *Migrace/{time stamp}_RowVersion.cs.*</span><span class="sxs-lookup"><span data-stu-id="01bfd-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="01bfd-411">Aktualizuje soubor *Migrations/SchoolContextModelSnapshot.cs.*</span><span class="sxs-lookup"><span data-stu-id="01bfd-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="01bfd-412">Aktualizace přidá do `BuildModel` metody následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="01bfd-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="01bfd-413">Spustí migrace k aktualizaci DATABÁZE.</span><span class="sxs-lookup"><span data-stu-id="01bfd-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="01bfd-414">Lešení oddělení model</span><span class="sxs-lookup"><span data-stu-id="01bfd-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="01bfd-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01bfd-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="01bfd-416">Postupujte podle pokynů v [lešení model studenta](xref:data/ef-rp/intro#scaffold-student-pages) a použít `Department` pro model třídy.</span><span class="sxs-lookup"><span data-stu-id="01bfd-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="01bfd-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01bfd-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="01bfd-418">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="01bfd-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="01bfd-419">Předchozí příkaz uchylovací `Department` příkaz y model.</span><span class="sxs-lookup"><span data-stu-id="01bfd-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="01bfd-420">Otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="01bfd-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="01bfd-421">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="01bfd-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="01bfd-422">Aktualizace stránky Index oddělení</span><span class="sxs-lookup"><span data-stu-id="01bfd-422">Update the Departments Index page</span></span>

<span data-ttu-id="01bfd-423">Modul lešení vytvořil `RowVersion` sloupec pro stránku Index, ale toto pole by nemělo být zobrazeno.</span><span class="sxs-lookup"><span data-stu-id="01bfd-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="01bfd-424">V tomto kurzu `RowVersion` je zobrazen poslední bajt, který pomáhá pochopit souběžnost.</span><span class="sxs-lookup"><span data-stu-id="01bfd-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="01bfd-425">Poslední bajt není zaručeno, že je jedinečný.</span><span class="sxs-lookup"><span data-stu-id="01bfd-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="01bfd-426">Skutečná aplikace by se `RowVersion` nezobrazila `RowVersion`ani se nezobrazila poslední bajt aplikace .</span><span class="sxs-lookup"><span data-stu-id="01bfd-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="01bfd-427">Aktualizujte stránku Rejstříku:</span><span class="sxs-lookup"><span data-stu-id="01bfd-427">Update the Index page:</span></span>

* <span data-ttu-id="01bfd-428">Nahraďte index odděleními.</span><span class="sxs-lookup"><span data-stu-id="01bfd-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="01bfd-429">Nahraďte značky `RowVersion` obsahující poslední bajt `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="01bfd-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="01bfd-430">Nahraďte FirstMidName fullname.</span><span class="sxs-lookup"><span data-stu-id="01bfd-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="01bfd-431">Následující značky zobrazují aktualizovanou stránku:</span><span class="sxs-lookup"><span data-stu-id="01bfd-431">The following markup shows the updated page:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="01bfd-432">Aktualizace modelu stránky Úpravy</span><span class="sxs-lookup"><span data-stu-id="01bfd-432">Update the Edit page model</span></span>

<span data-ttu-id="01bfd-433">Aktualizovat *stránky\Departments\Edit.cshtml.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="01bfd-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="01bfd-434">Chcete-li zjistit problém souběžnosti, [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) je aktualizován s hodnotou `rowVersion` z entity, která byla načtena.</span><span class="sxs-lookup"><span data-stu-id="01bfd-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="01bfd-435">EF Core generuje příkaz SQL UPDATE s klauzulí `RowVersion` WHERE obsahující původní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="01bfd-436">Pokud žádný řádek jsou ovlivněny příkazem UPDATE `RowVersion` (žádné `DbUpdateConcurrencyException` řádky mají původní hodnotu), je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="01bfd-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="01bfd-437">V předchozím kódu `Department.RowVersion` je hodnota při načtení entity.</span><span class="sxs-lookup"><span data-stu-id="01bfd-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="01bfd-438">`OriginalValue`je hodnota v DB, když `FirstOrDefaultAsync` byl volán v této metodě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="01bfd-439">Následující kód získá hodnoty klienta (hodnoty zaúčtované do této metody) a hodnoty DB:</span><span class="sxs-lookup"><span data-stu-id="01bfd-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="01bfd-440">Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který `OnPostAsync`má hodnoty DB odlišné od toho, co bylo zaúčtováno do :</span><span class="sxs-lookup"><span data-stu-id="01bfd-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="01bfd-441">Následující zvýrazněný kód `RowVersion` nastaví hodnotu na novou hodnotu načtenou z DB.</span><span class="sxs-lookup"><span data-stu-id="01bfd-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="01bfd-442">Při příštím kliknutí uživatele na **uložit**budou zachyceny pouze chyby souběžnosti, ke kterým dojde od posledního zobrazení stránky Úpravy.</span><span class="sxs-lookup"><span data-stu-id="01bfd-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="01bfd-443">Příkaz `ModelState.Remove` je povinný, `ModelState` protože `RowVersion` má starou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="01bfd-444">Na stránce holicí strojek má `ModelState` hodnota pole přednost před hodnotami vlastností modelu, pokud jsou k dispozici obě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="01bfd-445">Aktualizace stránky Úpravy</span><span class="sxs-lookup"><span data-stu-id="01bfd-445">Update the Edit page</span></span>

<span data-ttu-id="01bfd-446">Aktualizujte *stránky/oddělení/Edit.cshtml* pomocí následujících značek:</span><span class="sxs-lookup"><span data-stu-id="01bfd-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="01bfd-447">Předchozí značka:</span><span class="sxs-lookup"><span data-stu-id="01bfd-447">The preceding markup:</span></span>

* <span data-ttu-id="01bfd-448">Aktualizuje `page` direktivu z `@page` na . `@page "{id:int}"`</span><span class="sxs-lookup"><span data-stu-id="01bfd-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="01bfd-449">Přidá verzi skrytého řádku.</span><span class="sxs-lookup"><span data-stu-id="01bfd-449">Adds a hidden row version.</span></span> <span data-ttu-id="01bfd-450">`RowVersion`musí být přidán, takže post zpět váže hodnotu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="01bfd-451">Zobrazí poslední bajt `RowVersion` pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="01bfd-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="01bfd-452">Nahradí `ViewData` se silným typem `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="01bfd-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="01bfd-453">Testování konfliktů souběžnosti se stránkou Úpravy</span><span class="sxs-lookup"><span data-stu-id="01bfd-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="01bfd-454">Otevřete dvě instance prohlížeče Upravit na anglickém oddělení:</span><span class="sxs-lookup"><span data-stu-id="01bfd-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="01bfd-455">Spusťte aplikaci a vyberte Oddělení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="01bfd-456">Klikněte pravým tlačítkem myši na hypertextový odkaz **Upravit** pro anglické oddělení a **na nové kartě**vyberte otevřít .</span><span class="sxs-lookup"><span data-stu-id="01bfd-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="01bfd-457">Na první kartě klikněte na hypertextový odkaz **Upravit** pro anglické oddělení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="01bfd-458">Na dvou kartách prohlížeče se zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="01bfd-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="01bfd-459">Změňte název na první kartě prohlížeče a klepněte na **tlačítko Uložit**.</span><span class="sxs-lookup"><span data-stu-id="01bfd-459">Change the name in the first browser tab and click **Save**.</span></span>

![Oddělení Upravit stránku 1 po změně](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="01bfd-461">Prohlížeč zobrazí stránku Index se změněnou hodnotou a aktualizovaným indikátorem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="01bfd-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="01bfd-462">Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazí na druhém postbacku na druhé kartě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="01bfd-463">Změňte jiné pole na druhé kartě prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="01bfd-463">Change a different field in the second browser tab.</span></span>

![Oddělení Upravit stránku 2 po změně](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="01bfd-465">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="01bfd-465">Click **Save**.</span></span> <span data-ttu-id="01bfd-466">Zobrazí se chybové zprávy pro všechna pole, která neodpovídají hodnotám DB:</span><span class="sxs-lookup"><span data-stu-id="01bfd-466">You see error messages for all fields that don't match the DB values:</span></span>

![Chybová zpráva Upravit stránku oddělení](concurrency/_static/edit-error.png)

<span data-ttu-id="01bfd-468">Toto okno prohlížeče nemělo v úmyslu změnit pole Název.</span><span class="sxs-lookup"><span data-stu-id="01bfd-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="01bfd-469">Zkopírujte a vložte aktuální hodnotu (jazyky) do pole Název.</span><span class="sxs-lookup"><span data-stu-id="01bfd-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="01bfd-470">Vyjděte si. Ověření na straně klienta odebere chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-470">Tab out. Client-side validation removes the error message.</span></span>

![Chybová zpráva Upravit stránku oddělení](concurrency/_static/cv.png)

<span data-ttu-id="01bfd-472">Klikněte znovu na **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="01bfd-472">Click **Save** again.</span></span> <span data-ttu-id="01bfd-473">Hodnota zadaná na druhé kartě prohlížeče se uloží.</span><span class="sxs-lookup"><span data-stu-id="01bfd-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="01bfd-474">Uložené hodnoty se zobrazí na stránce Rejstřík.</span><span class="sxs-lookup"><span data-stu-id="01bfd-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="01bfd-475">Aktualizace stránky Odstranit</span><span class="sxs-lookup"><span data-stu-id="01bfd-475">Update the Delete page</span></span>

<span data-ttu-id="01bfd-476">Aktualizujte model stránky Odstranit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="01bfd-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="01bfd-477">Stránka Odstranit zjistí konflikty souběžnosti, když se entita po načtení změnila.</span><span class="sxs-lookup"><span data-stu-id="01bfd-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="01bfd-478">`Department.RowVersion`je verze řádku při načítání entity.</span><span class="sxs-lookup"><span data-stu-id="01bfd-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="01bfd-479">Když EF Core vytvoří příkaz SQL DELETE, `RowVersion`obsahuje klauzuli WHERE s .</span><span class="sxs-lookup"><span data-stu-id="01bfd-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="01bfd-480">Pokud příkaz SQL DELETE má za následek nula řádků ovlivněny:</span><span class="sxs-lookup"><span data-stu-id="01bfd-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="01bfd-481">Příkaz `RowVersion` DELETE v příkazu SQL `RowVersion` DELETE se neshoduje v databázi.</span><span class="sxs-lookup"><span data-stu-id="01bfd-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="01bfd-482">Je vyvolána výjimka DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="01bfd-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="01bfd-483">`OnGetAsync`je volána `concurrencyError`s .</span><span class="sxs-lookup"><span data-stu-id="01bfd-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="01bfd-484">Aktualizace stránky Odstranit</span><span class="sxs-lookup"><span data-stu-id="01bfd-484">Update the Delete page</span></span>

<span data-ttu-id="01bfd-485">Aktualizovat *stránky/oddělení/Delete.cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="01bfd-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="01bfd-486">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="01bfd-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="01bfd-487">Aktualizuje `page` direktivu z `@page` na . `@page "{id:int}"`</span><span class="sxs-lookup"><span data-stu-id="01bfd-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="01bfd-488">Přidá chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-488">Adds an error message.</span></span>
* <span data-ttu-id="01bfd-489">Nahradí firstmidname v poli **Správce** názvem FullName.</span><span class="sxs-lookup"><span data-stu-id="01bfd-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="01bfd-490">Změní `RowVersion` zobrazení posledního bajtu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="01bfd-491">Přidá verzi skrytého řádku.</span><span class="sxs-lookup"><span data-stu-id="01bfd-491">Adds a hidden row version.</span></span> <span data-ttu-id="01bfd-492">`RowVersion`musí být přidán, takže post zpět váže hodnotu.</span><span class="sxs-lookup"><span data-stu-id="01bfd-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="01bfd-493">Testování konfliktů souběžnosti se stránkou Odstranit</span><span class="sxs-lookup"><span data-stu-id="01bfd-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="01bfd-494">Vytvořte testovací oddělení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-494">Create a test department.</span></span>

<span data-ttu-id="01bfd-495">Otevřete dvě instance prohlížeče Delete na testovacím oddělení:</span><span class="sxs-lookup"><span data-stu-id="01bfd-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="01bfd-496">Spusťte aplikaci a vyberte Oddělení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="01bfd-497">Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** testovacího oddělení a **na nové kartě**vyberte otevřít .</span><span class="sxs-lookup"><span data-stu-id="01bfd-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="01bfd-498">Klikněte na hypertextový odkaz **Upravit** testovacího oddělení.</span><span class="sxs-lookup"><span data-stu-id="01bfd-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="01bfd-499">Na dvou kartách prohlížeče se zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="01bfd-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="01bfd-500">Změňte rozpočet na první kartě prohlížeče a klepněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="01bfd-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="01bfd-501">Prohlížeč zobrazí stránku Index se změněnou hodnotou a aktualizovaným indikátorem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="01bfd-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="01bfd-502">Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazí na druhém postbacku na druhé kartě.</span><span class="sxs-lookup"><span data-stu-id="01bfd-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="01bfd-503">Odstraňte testovací oddělení z druhé karty. Chyba souběžnosti se zobrazí s aktuálními hodnotami z DB.</span><span class="sxs-lookup"><span data-stu-id="01bfd-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="01bfd-504">Kliknutím na **Odstranit** odstraníte `RowVersion` entitu, pokud nebyla aktualizována.oddělení bylo odstraněno.</span><span class="sxs-lookup"><span data-stu-id="01bfd-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

<span data-ttu-id="01bfd-505">Viz [Dědičnost](xref:data/ef-mvc/inheritance) o tom, jak zdědit datový model.</span><span class="sxs-lookup"><span data-stu-id="01bfd-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="01bfd-506">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="01bfd-506">Additional resources</span></span>

* [<span data-ttu-id="01bfd-507">Tokeny souběžnosti v jádru EF</span><span class="sxs-lookup"><span data-stu-id="01bfd-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="01bfd-508">Zpracování souběžnosti v jádru EF</span><span class="sxs-lookup"><span data-stu-id="01bfd-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="01bfd-509">Verze tohoto kurzu pro YouTube (Zpracování konfliktů souběžnosti)</span><span class="sxs-lookup"><span data-stu-id="01bfd-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="01bfd-510">YouTube verze tohoto výukového programu (část 2)</span><span class="sxs-lookup"><span data-stu-id="01bfd-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="01bfd-511">YouTube verze tohoto výukového programu (část 3)</span><span class="sxs-lookup"><span data-stu-id="01bfd-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="01bfd-512">Předchozí</span><span class="sxs-lookup"><span data-stu-id="01bfd-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

