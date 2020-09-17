---
title: Část 8, Razor stránky s EF Core v ASP.NET Core-concurrency
author: rick-anderson
description: Část 8 Razor stránek a Entity Framework řady kurzů.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/concurrency
ms.openlocfilehash: 0f0f1a9c70a2d6725cbb68ac62850cf6aa332d36
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721837"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="651e1-103">Část 8, Razor stránky s EF Core v ASP.NET Core-concurrency</span><span class="sxs-lookup"><span data-stu-id="651e1-103">Part 8, Razor Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="651e1-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra)a [Jan P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="651e1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="651e1-105">V tomto kurzu se dozvíte, jak řešit konflikty, když více uživatelů aktualizuje entitu souběžně (ve stejnou dobu).</span><span class="sxs-lookup"><span data-stu-id="651e1-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="651e1-106">Konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="651e1-106">Concurrency conflicts</span></span>

<span data-ttu-id="651e1-107">Ke konfliktu souběžnosti dojde v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="651e1-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="651e1-108">Uživatel přejde na stránku pro úpravy pro entitu.</span><span class="sxs-lookup"><span data-stu-id="651e1-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="651e1-109">Jiný uživatel aktualizuje stejnou entitu před zapsáním změny prvního uživatele do databáze.</span><span class="sxs-lookup"><span data-stu-id="651e1-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="651e1-110">Pokud detekce souběžnosti není povolená, kdokoli aktualizuje databázi poslední přepsání změn provedených ostatními uživateli.</span><span class="sxs-lookup"><span data-stu-id="651e1-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="651e1-111">Pokud je toto riziko přijatelné, náklady na programování pro souběžnost můžou převážit výhody.</span><span class="sxs-lookup"><span data-stu-id="651e1-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="651e1-112">Pesimistická souběžnost (uzamykání)</span><span class="sxs-lookup"><span data-stu-id="651e1-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="651e1-113">Jedním ze způsobů, jak zabránit konfliktům souběžnosti, je použití zámků databáze.</span><span class="sxs-lookup"><span data-stu-id="651e1-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="651e1-114">Tato metoda se nazývá pesimistická souběžnost.</span><span class="sxs-lookup"><span data-stu-id="651e1-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="651e1-115">Předtím, než aplikace přečte řádek databáze, který je v úmyslu aktualizovat, si vyžádá zámek.</span><span class="sxs-lookup"><span data-stu-id="651e1-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="651e1-116">Když je řádek uzamčený pro přístup k aktualizacím, žádní jiní uživatelé nemůžou Uzamknout řádek, dokud se neuvolní první zámek.</span><span class="sxs-lookup"><span data-stu-id="651e1-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="651e1-117">Správa zámků má nevýhody.</span><span class="sxs-lookup"><span data-stu-id="651e1-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="651e1-118">Může být komplexní pro program a může způsobit problémy s výkonem při zvýšení počtu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="651e1-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="651e1-119">Entity Framework Core neposkytuje žádnou integrovanou podporu pro IT a v tomto kurzu se nezobrazuje, jak ho implementovat.</span><span class="sxs-lookup"><span data-stu-id="651e1-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="651e1-120">Optimistická souběžnost</span><span class="sxs-lookup"><span data-stu-id="651e1-120">Optimistic concurrency</span></span>

<span data-ttu-id="651e1-121">Optimistická souběžnost umožňuje konfliktům souběžnosti a pak správné chování při jejich provádění.</span><span class="sxs-lookup"><span data-stu-id="651e1-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="651e1-122">Jana například navštíví stránku pro úpravy oddělení a změní rozpočet pro anglické oddělení z $350 000,00 na $0,00.</span><span class="sxs-lookup"><span data-stu-id="651e1-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Změna rozpočtu na 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="651e1-124">Předtím, než Jana klikne na **Uložit**, Jan navštíví stejnou stránku a změní pole počáteční datum z 9/1/2007 na 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="651e1-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Změna počátečního data na 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="651e1-126">Jana klikne na **Uložit** jako první a projeví se jeho změna, protože prohlížeč zobrazí stránku indexu s nulovou hodnotou rozpočtu.</span><span class="sxs-lookup"><span data-stu-id="651e1-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="651e1-127">Jan klikne na **Uložit** na stránce pro úpravy, která stále zobrazuje rozpočet $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="651e1-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="651e1-128">Co se stane dále, podle toho, jak zpracovávat konflikty souběžnosti:</span><span class="sxs-lookup"><span data-stu-id="651e1-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="651e1-129">Můžete sledovat, kterou vlastnost uživatel změnil, a aktualizovat pouze odpovídající sloupce v databázi.</span><span class="sxs-lookup"><span data-stu-id="651e1-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="651e1-130">V tomto scénáři by se neztratila žádná data.</span><span class="sxs-lookup"><span data-stu-id="651e1-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="651e1-131">Dva uživatelé aktualizovali různé vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="651e1-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="651e1-132">Když někdo příště prochází v anglickém oddělení, uvidí změny Jana i Jan.</span><span class="sxs-lookup"><span data-stu-id="651e1-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="651e1-133">Tato metoda aktualizace může snížit počet konfliktů, které by mohly vést ke ztrátě dat.</span><span class="sxs-lookup"><span data-stu-id="651e1-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="651e1-134">Tento přístup má některé nevýhody:</span><span class="sxs-lookup"><span data-stu-id="651e1-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="651e1-135">Nelze zabránit ztrátě dat, pokud jsou provedeny konkurenční změny stejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="651e1-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="651e1-136">Většinou není v rámci webové aplikace praktické.</span><span class="sxs-lookup"><span data-stu-id="651e1-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="651e1-137">Aby bylo možné sledovat všechny načtené hodnoty a nové hodnoty, je nutné zachovat značný stav.</span><span class="sxs-lookup"><span data-stu-id="651e1-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="651e1-138">Udržování velkých objemů stavu může ovlivnit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="651e1-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="651e1-139">Může zvýšit složitost aplikace v porovnání s detekcí souběžnosti u entity.</span><span class="sxs-lookup"><span data-stu-id="651e1-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="651e1-140">Změnu můžete nechat v případě, že se změní Jan.</span><span class="sxs-lookup"><span data-stu-id="651e1-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="651e1-141">Když někdo příště prochází v anglickém oddělení, uvidí 9/1/2013 a načtenou hodnotu $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="651e1-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="651e1-142">Tento přístup se nazývá *klient WINS* nebo *Poslední ve scénáři služby WINS* .</span><span class="sxs-lookup"><span data-stu-id="651e1-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="651e1-143">(Všechny hodnoty z klienta mají přednost před tím, co je v úložišti dat.) Pokud neprovedete žádné kódování pro zpracování souběžnosti, dojde k automatickému provedení služby WINS klienta.</span><span class="sxs-lookup"><span data-stu-id="651e1-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="651e1-144">Můžete zabránit tomu, aby se změnila aktualizace od Jan v databázi.</span><span class="sxs-lookup"><span data-stu-id="651e1-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="651e1-145">Obvykle by aplikace měla:</span><span class="sxs-lookup"><span data-stu-id="651e1-145">Typically, the app would:</span></span>

  * <span data-ttu-id="651e1-146">Zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="651e1-146">Display an error message.</span></span>
  * <span data-ttu-id="651e1-147">Zobrazí aktuální stav dat.</span><span class="sxs-lookup"><span data-stu-id="651e1-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="651e1-148">Povolí uživateli znovu použít změny.</span><span class="sxs-lookup"><span data-stu-id="651e1-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="651e1-149">To se označuje jako scénář *služby WINS pro Store* .</span><span class="sxs-lookup"><span data-stu-id="651e1-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="651e1-150">(Hodnoty úložiště dat mají přednost před hodnotami odeslanými klientem.) Scénář služby WINS pro Store implementujete v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="651e1-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="651e1-151">Tato metoda zajistí, že nedojde k přepsání žádné změny bez upozornění uživatele.</span><span class="sxs-lookup"><span data-stu-id="651e1-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="651e1-152">Zjišťování konfliktů v EF Core</span><span class="sxs-lookup"><span data-stu-id="651e1-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="651e1-153">EF Core vyvolá `DbConcurrencyException` výjimky, když detekuje konflikty.</span><span class="sxs-lookup"><span data-stu-id="651e1-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="651e1-154">Aby bylo možné povolit detekci konfliktů, musí být datový model nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="651e1-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="651e1-155">Mezi možnosti povolení detekce konfliktů patří následující:</span><span class="sxs-lookup"><span data-stu-id="651e1-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="651e1-156">Nakonfigurujte EF Core pro zahrnutí původních hodnot sloupců nakonfigurovaných jako [tokeny souběžnosti](/ef/core/modeling/concurrency) v klauzuli WHERE příkazů Update a DELETE.</span><span class="sxs-lookup"><span data-stu-id="651e1-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="651e1-157">Když `SaveChanges` je volána, klauzule WHERE hledá původní hodnoty všech vlastností pokomentovaných s <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> atributem.</span><span class="sxs-lookup"><span data-stu-id="651e1-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribute.</span></span> <span data-ttu-id="651e1-158">Příkaz Update nenajde řádek, který se má aktualizovat, pokud se některá z vlastností tokenu souběžnosti změnila od prvního načtení řádku.</span><span class="sxs-lookup"><span data-stu-id="651e1-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="651e1-159">EF Core interpretuje, že jako konflikt souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="651e1-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="651e1-160">U databázových tabulek, které mají mnoho sloupců, může tento přístup mít za následek velmi velké klauzule WHERE a může vyžadovat velké množství stavu.</span><span class="sxs-lookup"><span data-stu-id="651e1-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="651e1-161">Proto se tento přístup obecně nedoporučuje a nejedná se o metodu používanou v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="651e1-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="651e1-162">V tabulce databáze zahrňte sloupec sledování, který se dá použít k určení, kdy došlo ke změně řádku.</span><span class="sxs-lookup"><span data-stu-id="651e1-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="651e1-163">V databázi SQL Server je datový typ sloupce sledování `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="651e1-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="651e1-164">`rowversion`Hodnota je sekvenční číslo, které se zvýší pokaždé, když se řádek aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="651e1-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="651e1-165">V příkazu Update nebo DELETE zahrnuje klauzule WHERE původní hodnotu sloupce sledování (číslo verze původního řádku).</span><span class="sxs-lookup"><span data-stu-id="651e1-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="651e1-166">Pokud byl aktualizovaný řádek změněn jiným uživatelem, hodnota ve `rowversion` sloupci se liší od původní hodnoty.</span><span class="sxs-lookup"><span data-stu-id="651e1-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="651e1-167">V takovém případě příkaz Update nebo DELETE nemůže najít řádek, který se má aktualizovat z klauzule WHERE.</span><span class="sxs-lookup"><span data-stu-id="651e1-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="651e1-168">EF Core vyvolá výjimku souběžnosti, pokud nejsou žádné řádky ovlivněny příkazem Update nebo DELETE.</span><span class="sxs-lookup"><span data-stu-id="651e1-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="651e1-169">Přidat vlastnost sledování</span><span class="sxs-lookup"><span data-stu-id="651e1-169">Add a tracking property</span></span>

<span data-ttu-id="651e1-170">V *modelu/oddělení. cs*přidejte vlastnost sledování s názvem rowversion:</span><span class="sxs-lookup"><span data-stu-id="651e1-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="651e1-171"><xref:System.ComponentModel.DataAnnotations.TimestampAttribute>Atribut je to, co identifikuje sloupec jako sloupec sledování souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="651e1-171">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="651e1-172">Rozhraní Fluent API je alternativní způsob, jak určit vlastnost sledování:</span><span class="sxs-lookup"><span data-stu-id="651e1-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="651e1-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="651e1-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="651e1-174">U databáze SQL Server je `[Timestamp]` atribut vlastnosti entity definovaný jako bajtové pole:</span><span class="sxs-lookup"><span data-stu-id="651e1-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="651e1-175">Způsobí, že bude sloupec zahrnut v klauzulích DELETE a UPDATE WHERE.</span><span class="sxs-lookup"><span data-stu-id="651e1-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="651e1-176">Nastaví typ sloupce v databázi na [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="651e1-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="651e1-177">Databáze generuje číslo verze sekvenčního řádku, které se zvýší pokaždé, když se řádek aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="651e1-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="651e1-178">V `Update` příkazu nebo `Delete` `Where` zahrnuje klauzule hodnotu načtené verze řádku.</span><span class="sxs-lookup"><span data-stu-id="651e1-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="651e1-179">Pokud se aktualizovaný řádek od načtení změnil:</span><span class="sxs-lookup"><span data-stu-id="651e1-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="651e1-180">Hodnota verze aktuálního řádku se neshoduje s načtenou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="651e1-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="651e1-181">`Update`Příkazy nebo `Delete` nenaleznou řádek, protože `Where` klauzule vyhledává hodnotu načtené verze řádku.</span><span class="sxs-lookup"><span data-stu-id="651e1-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="651e1-182">`DbUpdateConcurrencyException`Je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="651e1-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="651e1-183">Následující kód ukazuje část T-SQL vygenerované EF Core, když je název oddělení aktualizovaný:</span><span class="sxs-lookup"><span data-stu-id="651e1-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="651e1-184">Předchozí zvýrazněný kód ukazuje `WHERE` klauzuli obsahující `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="651e1-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="651e1-185">Pokud se databáze `RowVersion` neshoduje s `RowVersion` parametrem ( `@p2` ), žádné řádky se neaktualizují.</span><span class="sxs-lookup"><span data-stu-id="651e1-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="651e1-186">Následující zvýrazněný kód ukazuje T-SQL, který ověří právě jeden řádek, který byl aktualizován:</span><span class="sxs-lookup"><span data-stu-id="651e1-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="651e1-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Vrátí počet řádků ovlivněných posledním příkazem.</span><span class="sxs-lookup"><span data-stu-id="651e1-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="651e1-188">Pokud nejsou aktualizovány žádné řádky, EF Core vyvolá `DbUpdateConcurrencyException` .</span><span class="sxs-lookup"><span data-stu-id="651e1-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="651e1-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="651e1-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="651e1-190">Pro databázi SQLite je `[Timestamp]` atribut vlastnosti entity definovaný jako bajtové pole:</span><span class="sxs-lookup"><span data-stu-id="651e1-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="651e1-191">Způsobí, že bude sloupec zahrnut v klauzulích DELETE a UPDATE WHERE.</span><span class="sxs-lookup"><span data-stu-id="651e1-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="651e1-192">Provede mapování na typ sloupce objektu BLOB.</span><span class="sxs-lookup"><span data-stu-id="651e1-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="651e1-193">Když se aktualizuje řádek, triggery databáze aktualizují sloupec RowVersion pomocí nového náhodného bajtového pole.</span><span class="sxs-lookup"><span data-stu-id="651e1-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="651e1-194">V `Update` příkazu OR `Delete` `Where` zahrnuje klauzule načtenou hodnotu sloupce rowversion.</span><span class="sxs-lookup"><span data-stu-id="651e1-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="651e1-195">Pokud se aktualizovaný řádek od načtení změnil:</span><span class="sxs-lookup"><span data-stu-id="651e1-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="651e1-196">Hodnota verze aktuálního řádku se neshoduje s načtenou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="651e1-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="651e1-197">`Update`Příkaz nebo `Delete` nenalezne řádek, protože `Where` klauzule vyhledá původní hodnotu verze řádku.</span><span class="sxs-lookup"><span data-stu-id="651e1-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="651e1-198">`DbUpdateConcurrencyException`Je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="651e1-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="651e1-199">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="651e1-199">Update the database</span></span>

<span data-ttu-id="651e1-200">Přidáním `RowVersion` vlastnosti se změní datový model, který vyžaduje migraci.</span><span class="sxs-lookup"><span data-stu-id="651e1-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="651e1-201">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="651e1-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="651e1-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="651e1-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="651e1-203">V PMC spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="651e1-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="651e1-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="651e1-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="651e1-205">V terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="651e1-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="651e1-206">Tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="651e1-206">This command:</span></span>

* <span data-ttu-id="651e1-207">Vytvoří migrační soubor *_RowVersion. cs migrace/{časového razítka}* .</span><span class="sxs-lookup"><span data-stu-id="651e1-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="651e1-208">Aktualizuje soubor *migrations/SchoolContextModelSnapshot. cs* .</span><span class="sxs-lookup"><span data-stu-id="651e1-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="651e1-209">Aktualizace přidá do metody následující zvýrazněný kód `BuildModel` :</span><span class="sxs-lookup"><span data-stu-id="651e1-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="651e1-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="651e1-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="651e1-211">V PMC spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="651e1-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="651e1-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="651e1-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="651e1-213">Otevřete `Migrations/<timestamp>_RowVersion.cs` soubor a přidejte zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="651e1-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="651e1-214">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="651e1-214">The preceding code:</span></span>

  * <span data-ttu-id="651e1-215">Aktualizuje existující řádky s náhodnými hodnotami objektů BLOB.</span><span class="sxs-lookup"><span data-stu-id="651e1-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="651e1-216">Pokaždé, když se aktualizuje řádek, přidá aktivační procedury databáze, které nastaví sloupec RowVersion na náhodný hodnotu BLOB.</span><span class="sxs-lookup"><span data-stu-id="651e1-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="651e1-217">V terminálu spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="651e1-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="651e1-218">Stránky oddělení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="651e1-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="651e1-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="651e1-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="651e1-220">Postupujte podle pokynů v části [stránky pro studenty](xref:data/ef-rp/intro#scaffold-student-pages) s těmito výjimkami:</span><span class="sxs-lookup"><span data-stu-id="651e1-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="651e1-221">Vytvořte složku *stránky nebo oddělení* .</span><span class="sxs-lookup"><span data-stu-id="651e1-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="651e1-222">Použijte `Department` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="651e1-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="651e1-223">Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.</span><span class="sxs-lookup"><span data-stu-id="651e1-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="651e1-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="651e1-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="651e1-225">Vytvořte složku *stránky nebo oddělení* .</span><span class="sxs-lookup"><span data-stu-id="651e1-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="651e1-226">Spusťte následující příkaz pro generování uživatelského rozhraní stránek oddělení.</span><span class="sxs-lookup"><span data-stu-id="651e1-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="651e1-227">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="651e1-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="651e1-228">**V systému Linux nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="651e1-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="651e1-229">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="651e1-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="651e1-230">Aktualizace stránky indexu</span><span class="sxs-lookup"><span data-stu-id="651e1-230">Update the Index page</span></span>

<span data-ttu-id="651e1-231">Nástroj pro generování uživatelského rozhraní vytvořil `RowVersion` sloupec pro stránku indexu, ale toto pole by se v produkční aplikaci nezobrazovalo.</span><span class="sxs-lookup"><span data-stu-id="651e1-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="651e1-232">V tomto kurzu se zobrazí poslední bajt, `RowVersion` který vám ukáže, jak funguje zpracování souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="651e1-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="651e1-233">Poslední bajt není zaručený jako jedinečný.</span><span class="sxs-lookup"><span data-stu-id="651e1-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="651e1-234">Aktualizovat stránku *Pages\Departments\Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="651e1-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="651e1-235">Nahraďte index "odděleními".</span><span class="sxs-lookup"><span data-stu-id="651e1-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="651e1-236">Změňte kód obsahující `RowVersion` tak, aby zobrazoval pouze poslední bajt pole bajtů.</span><span class="sxs-lookup"><span data-stu-id="651e1-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="651e1-237">Nahraďte FirstMidName řetězcem FullName.</span><span class="sxs-lookup"><span data-stu-id="651e1-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="651e1-238">Následující kód ukazuje aktualizovanou stránku:</span><span class="sxs-lookup"><span data-stu-id="651e1-238">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="651e1-239">Aktualizace modelu úprav stránek</span><span class="sxs-lookup"><span data-stu-id="651e1-239">Update the Edit page model</span></span>

<span data-ttu-id="651e1-240">*Pages\Departments\Edit.cshtml.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="651e1-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="651e1-241"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue>Je aktualizována `rowVersion` hodnotou z entity, když byla načtena v `OnGet` metodě.</span><span class="sxs-lookup"><span data-stu-id="651e1-241">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="651e1-242">EF Core generuje příkaz SQL UPDATE s klauzulí WHERE obsahující původní `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="651e1-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="651e1-243">Pokud nejsou žádné řádky ovlivněny příkazem UPDATE (žádné řádky nemají původní `RowVersion` hodnotu), `DbUpdateConcurrencyException` je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="651e1-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="651e1-244">V předchozím zvýrazněném kódu:</span><span class="sxs-lookup"><span data-stu-id="651e1-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="651e1-245">Hodnota v `Department.RowVersion` poli je to, co bylo v entitě v okamžiku, kdy byla původně načtena v žádosti o získání stránky pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="651e1-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="651e1-246">Hodnota je poskytnuta `OnPost` metodě pomocí skrytého pole na Razor stránce, které zobrazuje entitu, kterou chcete upravit.</span><span class="sxs-lookup"><span data-stu-id="651e1-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="651e1-247">Hodnota skrytého pole je zkopírována do `Department.RowVersion` pořadače modelu.</span><span class="sxs-lookup"><span data-stu-id="651e1-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="651e1-248">`OriginalValue` je to, co EF Core bude používat v klauzuli WHERE.</span><span class="sxs-lookup"><span data-stu-id="651e1-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="651e1-249">Předtím, než se spustí zvýrazněný řádek kódu, `OriginalValue` má hodnotu, která byla v databázi `FirstOrDefaultAsync` volána v této metodě, která se může lišit od toho, co bylo zobrazeno na stránce pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="651e1-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="651e1-250">Zvýrazněný kód zajišťuje, aby EF Core používal původní `RowVersion` hodnotu ze zobrazené `Department` entity v klauzuli WHERE příkazu SQL Update.</span><span class="sxs-lookup"><span data-stu-id="651e1-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="651e1-251">Když dojde k chybě souběžnosti, následující zvýrazněný kód Získá hodnoty klienta (hodnoty odeslané do této metody) a hodnoty databáze.</span><span class="sxs-lookup"><span data-stu-id="651e1-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="651e1-252">Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišné od odeslání do `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="651e1-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="651e1-253">Následující zvýrazněný kód nastaví `RowVersion` hodnotu na novou hodnotu načtenou z databáze.</span><span class="sxs-lookup"><span data-stu-id="651e1-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="651e1-254">Až uživatel příště klikne na možnost **Uložit**, bude zachycena pouze chyba souběžnosti, ke kterým dochází od posledního zobrazení stránky pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="651e1-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="651e1-255">`ModelState.Remove`Příkaz je vyžadován, protože `ModelState` má starou `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="651e1-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="651e1-256">Na Razor stránce `ModelState` má hodnota pro pole přednost před hodnotami vlastností modelu, pokud jsou oba přítomny.</span><span class="sxs-lookup"><span data-stu-id="651e1-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-edit-page"></a><span data-ttu-id="651e1-257">Aktualizace stránky pro úpravy</span><span class="sxs-lookup"><span data-stu-id="651e1-257">Update the Edit page</span></span>

<span data-ttu-id="651e1-258">Aktualizovat *stránky/oddělení/upravit. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="651e1-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="651e1-259">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="651e1-259">The preceding code:</span></span>

* <span data-ttu-id="651e1-260">Aktualizuje `page` direktivu z `@page` na `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="651e1-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="651e1-261">Přidá verzi skrytého řádku.</span><span class="sxs-lookup"><span data-stu-id="651e1-261">Adds a hidden row version.</span></span> <span data-ttu-id="651e1-262">`RowVersion` je nutné přidat, aby postback navázal hodnotu.</span><span class="sxs-lookup"><span data-stu-id="651e1-262">`RowVersion` must be added so postback binds the value.</span></span>
* <span data-ttu-id="651e1-263">Zobrazí poslední bajt `RowVersion` pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="651e1-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="651e1-264">Nahrazuje `ViewData` silným typem `InstructorNameSL` .</span><span class="sxs-lookup"><span data-stu-id="651e1-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="651e1-265">Souběžnost testů v konfliktu se stránkou pro úpravy</span><span class="sxs-lookup"><span data-stu-id="651e1-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="651e1-266">V anglickém oddělení otevřete dvě instance pro úpravy v angličtině:</span><span class="sxs-lookup"><span data-stu-id="651e1-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="651e1-267">Spusťte aplikaci a vyberte oddělení.</span><span class="sxs-lookup"><span data-stu-id="651e1-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="651e1-268">Klikněte pravým tlačítkem **na hypertextový** odkaz pro jazykové oddělení a vyberte **otevřít na nové kartě**.</span><span class="sxs-lookup"><span data-stu-id="651e1-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="651e1-269">Na první kartě klikněte na odkaz **Upravit** pro anglické oddělení.</span><span class="sxs-lookup"><span data-stu-id="651e1-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="651e1-270">Dvě karty prohlížeče zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="651e1-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="651e1-271">Změňte název na první kartě prohlížeče a klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="651e1-271">Change the name in the first browser tab and click **Save**.</span></span>

![Stránka pro úpravy v oddělení 1 po změně](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="651e1-273">Prohlížeč zobrazí stránku index se změněnou hodnotou a aktualizovaným indikátorem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="651e1-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="651e1-274">Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazuje na druhém panelu pro odeslání na druhé kartě.</span><span class="sxs-lookup"><span data-stu-id="651e1-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="651e1-275">Změňte jiné pole na druhé kartě prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="651e1-275">Change a different field in the second browser tab.</span></span>

![Stránka pro úpravy v oddělení 2 po změně](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="651e1-277">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="651e1-277">Click **Save**.</span></span> <span data-ttu-id="651e1-278">Zobrazí se chybové zprávy pro všechna pole, která neodpovídají hodnotám databáze:</span><span class="sxs-lookup"><span data-stu-id="651e1-278">You see error messages for all fields that don't match the database values:</span></span>

![Chybová zpráva stránky pro úpravu oddělení](concurrency/_static/edit-error30.png)

<span data-ttu-id="651e1-280">Toto okno prohlížeče nemá v úmyslu změnit pole název.</span><span class="sxs-lookup"><span data-stu-id="651e1-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="651e1-281">Zkopírujte a vložte aktuální hodnotu (jazyky) do pole název.</span><span class="sxs-lookup"><span data-stu-id="651e1-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="651e1-282">Tabulátor. Ověřování na straně klienta odebere chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="651e1-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="651e1-283">Znovu klikněte na **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="651e1-283">Click **Save** again.</span></span> <span data-ttu-id="651e1-284">Hodnota, kterou jste zadali na druhé záložce prohlížeče, se uloží.</span><span class="sxs-lookup"><span data-stu-id="651e1-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="651e1-285">Uložené hodnoty se zobrazí na stránce index.</span><span class="sxs-lookup"><span data-stu-id="651e1-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="651e1-286">Aktualizace odstranění modelu stránky</span><span class="sxs-lookup"><span data-stu-id="651e1-286">Update the Delete page model</span></span>

<span data-ttu-id="651e1-287">Aktualizujte *stránky/oddělení/odstraňte. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="651e1-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="651e1-288">Stránka odstranit detekuje konflikty souběžnosti, když se entita po jejím načtení změnila.</span><span class="sxs-lookup"><span data-stu-id="651e1-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="651e1-289">`Department.RowVersion` je verze řádku v případě, že byla entita načtena.</span><span class="sxs-lookup"><span data-stu-id="651e1-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="651e1-290">Když EF Core vytvoří příkaz SQL DELETE, zahrnuje klauzuli WHERE s `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="651e1-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="651e1-291">Pokud příkaz SQL DELETE má vliv na nulové řádky:</span><span class="sxs-lookup"><span data-stu-id="651e1-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="651e1-292">`RowVersion`Příkaz v příkazu SQL DELETE se v databázi neshoduje `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="651e1-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="651e1-293">Je vyvolána výjimka DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="651e1-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="651e1-294">`OnGetAsync` je volána pomocí `concurrencyError` .</span><span class="sxs-lookup"><span data-stu-id="651e1-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="651e1-295">Aktualizace stránky pro odstranění</span><span class="sxs-lookup"><span data-stu-id="651e1-295">Update the Delete page</span></span>

<span data-ttu-id="651e1-296">Aktualizovat *stránky/oddělení/odstranit. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="651e1-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="651e1-297">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="651e1-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="651e1-298">Aktualizuje `page` direktivu z `@page` na `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="651e1-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="651e1-299">Přidá chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="651e1-299">Adds an error message.</span></span>
* <span data-ttu-id="651e1-300">Nahradí FirstMidName pomocí FullName v poli **správce** .</span><span class="sxs-lookup"><span data-stu-id="651e1-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="651e1-301">Změny `RowVersion` pro zobrazení posledního bajtu</span><span class="sxs-lookup"><span data-stu-id="651e1-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="651e1-302">Přidá verzi skrytého řádku.</span><span class="sxs-lookup"><span data-stu-id="651e1-302">Adds a hidden row version.</span></span> <span data-ttu-id="651e1-303">`RowVersion` je nutné přidat, aby postback navázal hodnotu.</span><span class="sxs-lookup"><span data-stu-id="651e1-303">`RowVersion` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="651e1-304">Konflikty testů v souběžnosti</span><span class="sxs-lookup"><span data-stu-id="651e1-304">Test concurrency conflicts</span></span>

<span data-ttu-id="651e1-305">Vytvořte testovací oddělení.</span><span class="sxs-lookup"><span data-stu-id="651e1-305">Create a test department.</span></span>

<span data-ttu-id="651e1-306">V testovacím středisku otevřete dvě instance pro odstraňování těchto prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="651e1-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="651e1-307">Spusťte aplikaci a vyberte oddělení.</span><span class="sxs-lookup"><span data-stu-id="651e1-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="651e1-308">Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** pro testovací oddělení a vyberte **otevřít na nové kartě**.</span><span class="sxs-lookup"><span data-stu-id="651e1-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="651e1-309">Klikněte na odkaz **Upravit** pro testovací oddělení.</span><span class="sxs-lookup"><span data-stu-id="651e1-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="651e1-310">Dvě karty prohlížeče zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="651e1-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="651e1-311">Změňte rozpočet na první kartě prohlížeče a klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="651e1-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="651e1-312">Prohlížeč zobrazí stránku index se změněnou hodnotou a aktualizovaným indikátorem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="651e1-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="651e1-313">Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazuje na druhém panelu pro odeslání na druhé kartě.</span><span class="sxs-lookup"><span data-stu-id="651e1-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="651e1-314">Odstraňte testovací oddělení z druhé karty. Chyba souběžnosti se zobrazuje s aktuálními hodnotami z databáze.</span><span class="sxs-lookup"><span data-stu-id="651e1-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="651e1-315">Kliknutím na **Odstranit** odstraníte entitu, pokud `RowVersion` se neaktualizovala.</span><span class="sxs-lookup"><span data-stu-id="651e1-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="651e1-316">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="651e1-316">Additional resources</span></span>

* [<span data-ttu-id="651e1-317">Tokeny souběžnosti v EF Core</span><span class="sxs-lookup"><span data-stu-id="651e1-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="651e1-318">Zpracování souběžnosti v EF Core</span><span class="sxs-lookup"><span data-stu-id="651e1-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="651e1-319">Ladění zdrojového kódu ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="651e1-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="651e1-320">Další kroky</span><span class="sxs-lookup"><span data-stu-id="651e1-320">Next steps</span></span>

<span data-ttu-id="651e1-321">Toto je poslední kurz v řadě.</span><span class="sxs-lookup"><span data-stu-id="651e1-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="651e1-322">Další témata jsou popsaná v tématu [verze MVC této série kurzů](xref:data/ef-mvc/index).</span><span class="sxs-lookup"><span data-stu-id="651e1-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="651e1-323">Předchozí kurz</span><span class="sxs-lookup"><span data-stu-id="651e1-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="651e1-324">V tomto kurzu se dozvíte, jak řešit konflikty, když více uživatelů aktualizuje entitu souběžně (ve stejnou dobu).</span><span class="sxs-lookup"><span data-stu-id="651e1-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="651e1-325">Pokud narazíte na problémy, které nemůžete vyřešit, [Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="651e1-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="651e1-326">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="651e1-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="651e1-327">Konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="651e1-327">Concurrency conflicts</span></span>

<span data-ttu-id="651e1-328">Ke konfliktu souběžnosti dojde v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="651e1-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="651e1-329">Uživatel přejde na stránku pro úpravy pro entitu.</span><span class="sxs-lookup"><span data-stu-id="651e1-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="651e1-330">Jiný uživatel aktualizuje stejnou entitu před zapsáním změny prvního uživatele do databáze.</span><span class="sxs-lookup"><span data-stu-id="651e1-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="651e1-331">Pokud detekce souběžnosti není povolená, když dojde k souběžným aktualizacím:</span><span class="sxs-lookup"><span data-stu-id="651e1-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="651e1-332">Poslední aktualizace služby WINS.</span><span class="sxs-lookup"><span data-stu-id="651e1-332">The last update wins.</span></span> <span data-ttu-id="651e1-333">To znamená, že poslední hodnoty aktualizace se uloží do databáze.</span><span class="sxs-lookup"><span data-stu-id="651e1-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="651e1-334">První z aktuálních aktualizací bude ztracena.</span><span class="sxs-lookup"><span data-stu-id="651e1-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="651e1-335">Optimistická souběžnost</span><span class="sxs-lookup"><span data-stu-id="651e1-335">Optimistic concurrency</span></span>

<span data-ttu-id="651e1-336">Optimistická souběžnost umožňuje konfliktům souběžnosti a pak správné chování při jejich provádění.</span><span class="sxs-lookup"><span data-stu-id="651e1-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="651e1-337">Jana například navštíví stránku pro úpravy oddělení a změní rozpočet pro anglické oddělení z $350 000,00 na $0,00.</span><span class="sxs-lookup"><span data-stu-id="651e1-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="651e1-339">Předtím, než Jana klikne na **Uložit**, Jan navštíví stejnou stránku a změní pole počáteční datum z 9/1/2007 na 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="651e1-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Změna počátečního data na 2013](concurrency/_static/change-date.png)

<span data-ttu-id="651e1-341">Jana klikne na **Uložit** a uvidí jeho změnu, když prohlížeč zobrazí stránku rejstřík.</span><span class="sxs-lookup"><span data-stu-id="651e1-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Rozpočet se změnil na nula.](concurrency/_static/budget-zero.png)

<span data-ttu-id="651e1-343">Jan klikne na **Uložit** na stránce pro úpravy, která stále zobrazuje rozpočet $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="651e1-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="651e1-344">Co se stane dál, určíte tak, jak můžete zpracovávat konflikty souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="651e1-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="651e1-345">Optimistická souběžnost zahrnuje následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="651e1-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="651e1-346">Můžete sledovat, kterou vlastnost uživatel změnil, a aktualizovat pouze odpovídající sloupce v databázi.</span><span class="sxs-lookup"><span data-stu-id="651e1-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="651e1-347">V tomto scénáři by se neztratila žádná data.</span><span class="sxs-lookup"><span data-stu-id="651e1-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="651e1-348">Dva uživatelé aktualizovali různé vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="651e1-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="651e1-349">Když někdo příště prochází v anglickém oddělení, uvidí změny Jana i Jan.</span><span class="sxs-lookup"><span data-stu-id="651e1-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="651e1-350">Tato metoda aktualizace může snížit počet konfliktů, které by mohly vést ke ztrátě dat.</span><span class="sxs-lookup"><span data-stu-id="651e1-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="651e1-351">Tento přístup:</span><span class="sxs-lookup"><span data-stu-id="651e1-351">This approach:</span></span>
 
  * <span data-ttu-id="651e1-352">Nelze zabránit ztrátě dat, pokud jsou provedeny konkurenční změny stejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="651e1-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="651e1-353">Většinou není v rámci webové aplikace praktické.</span><span class="sxs-lookup"><span data-stu-id="651e1-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="651e1-354">Aby bylo možné sledovat všechny načtené hodnoty a nové hodnoty, je nutné zachovat značný stav.</span><span class="sxs-lookup"><span data-stu-id="651e1-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="651e1-355">Udržování velkých objemů stavu může ovlivnit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="651e1-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="651e1-356">Může zvýšit složitost aplikace v porovnání s detekcí souběžnosti u entity.</span><span class="sxs-lookup"><span data-stu-id="651e1-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="651e1-357">Změnu můžete nechat v případě, že se změní Jan.</span><span class="sxs-lookup"><span data-stu-id="651e1-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="651e1-358">Když někdo příště prochází v anglickém oddělení, uvidí 9/1/2013 a načtenou hodnotu $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="651e1-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="651e1-359">Tento přístup se nazývá *klient WINS* nebo *Poslední ve scénáři služby WINS* .</span><span class="sxs-lookup"><span data-stu-id="651e1-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="651e1-360">(Všechny hodnoty z klienta mají přednost před tím, co je v úložišti dat.) Pokud neprovedete žádné kódování pro zpracování souběžnosti, dojde k automatickému provedení služby WINS klienta.</span><span class="sxs-lookup"><span data-stu-id="651e1-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="651e1-361">Můžete zabránit tomu, aby se změny od Jan aktualizovaly v databázi.</span><span class="sxs-lookup"><span data-stu-id="651e1-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="651e1-362">Obvykle by aplikace měla:</span><span class="sxs-lookup"><span data-stu-id="651e1-362">Typically, the app would:</span></span>

  * <span data-ttu-id="651e1-363">Zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="651e1-363">Display an error message.</span></span>
  * <span data-ttu-id="651e1-364">Zobrazí aktuální stav dat.</span><span class="sxs-lookup"><span data-stu-id="651e1-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="651e1-365">Povolí uživateli znovu použít změny.</span><span class="sxs-lookup"><span data-stu-id="651e1-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="651e1-366">To se označuje jako scénář *služby WINS pro Store* .</span><span class="sxs-lookup"><span data-stu-id="651e1-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="651e1-367">(Hodnoty úložiště dat mají přednost před hodnotami odeslanými klientem.) Scénář služby WINS pro Store implementujete v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="651e1-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="651e1-368">Tato metoda zajistí, že nedojde k přepsání žádné změny bez upozornění uživatele.</span><span class="sxs-lookup"><span data-stu-id="651e1-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="651e1-369">Zpracování souběžnosti</span><span class="sxs-lookup"><span data-stu-id="651e1-369">Handling concurrency</span></span> 

<span data-ttu-id="651e1-370">Když je vlastnost konfigurovaná jako [Token souběžnosti](/ef/core/modeling/concurrency):</span><span class="sxs-lookup"><span data-stu-id="651e1-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="651e1-371">EF Core ověří, že se vlastnost po načtení nezměnila.</span><span class="sxs-lookup"><span data-stu-id="651e1-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="651e1-372">K ověření dochází při volání [metody SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) nebo [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) .</span><span class="sxs-lookup"><span data-stu-id="651e1-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="651e1-373">Pokud byla vlastnost po načtení změněna, je vyvolána výjimka [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) .</span><span class="sxs-lookup"><span data-stu-id="651e1-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) is thrown.</span></span> 

<span data-ttu-id="651e1-374">DATABÁZE a datový model musí být nakonfigurovány tak, aby podporovaly vyvolání `DbUpdateConcurrencyException` .</span><span class="sxs-lookup"><span data-stu-id="651e1-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="651e1-375">Zjištění konfliktů souběžnosti u vlastnosti</span><span class="sxs-lookup"><span data-stu-id="651e1-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="651e1-376">Konflikty souběžnosti lze zjistit na úrovni vlastnosti pomocí atributu [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) .</span><span class="sxs-lookup"><span data-stu-id="651e1-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="651e1-377">Atribut lze použít na více vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="651e1-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="651e1-378">Další informace najdete v tématu [data anotaces – ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="651e1-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="651e1-379">`[ConcurrencyCheck]`Atribut se v tomto kurzu nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="651e1-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="651e1-380">Zjištění konfliktů souběžnosti na řádku</span><span class="sxs-lookup"><span data-stu-id="651e1-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="651e1-381">K detekci konfliktů souběžnosti se do modelu přidá sloupec sledování [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) .</span><span class="sxs-lookup"><span data-stu-id="651e1-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="651e1-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="651e1-382">`rowversion` :</span></span>

* <span data-ttu-id="651e1-383">Je SQL Server specifické.</span><span class="sxs-lookup"><span data-stu-id="651e1-383">Is SQL Server specific.</span></span> <span data-ttu-id="651e1-384">Jiné databáze nemusí poskytovat podobnou funkci.</span><span class="sxs-lookup"><span data-stu-id="651e1-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="651e1-385">Slouží k určení, že entita nebyla od načtení z databáze změněna.</span><span class="sxs-lookup"><span data-stu-id="651e1-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="651e1-386">DATABÁZE generuje pořadové `rowversion` číslo, které se zvýší při každé aktualizaci řádku.</span><span class="sxs-lookup"><span data-stu-id="651e1-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="651e1-387">V `Update` příkazu nebo `Delete` `Where` obsahuje klauzule načtenou hodnotu `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="651e1-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="651e1-388">Pokud se změnil řádek, který se má aktualizovat:</span><span class="sxs-lookup"><span data-stu-id="651e1-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="651e1-389">`rowversion` neodpovídá načtené hodnotě.</span><span class="sxs-lookup"><span data-stu-id="651e1-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="651e1-390">`Update`Příkazy nebo `Delete` nenaleznou řádek, protože `Where` klauzule zahrnuje načtenou hodnotu `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="651e1-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="651e1-391">`DbUpdateConcurrencyException`Je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="651e1-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="651e1-392">Pokud se v EF Core neaktualizovaly žádné řádky `Update` `Delete` příkazem nebo, je vyvolána výjimka souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="651e1-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="651e1-393">Přidat vlastnost sledování k entitě oddělení</span><span class="sxs-lookup"><span data-stu-id="651e1-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="651e1-394">V *modelu/oddělení. cs*přidejte vlastnost sledování s názvem rowversion:</span><span class="sxs-lookup"><span data-stu-id="651e1-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="651e1-395">Atribut [timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) určuje, že tento sloupec je zahrnut v `Where` klauzuli `Update` `Delete` příkazů a.</span><span class="sxs-lookup"><span data-stu-id="651e1-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="651e1-396">Atribut je volán, `Timestamp` protože předchozí verze SQL Server používaly `timestamp` datový typ SQL předtím, než `rowversion` jej nahradil typ SQL.</span><span class="sxs-lookup"><span data-stu-id="651e1-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="651e1-397">Rozhraní Fluent API může také určovat vlastnost sledování:</span><span class="sxs-lookup"><span data-stu-id="651e1-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="651e1-398">Následující kód ukazuje část T-SQL vygenerované EF Core, když je název oddělení aktualizovaný:</span><span class="sxs-lookup"><span data-stu-id="651e1-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="651e1-399">Předchozí zvýrazněný kód ukazuje `WHERE` klauzuli obsahující `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="651e1-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="651e1-400">Pokud se databáze `RowVersion` neshoduje s `RowVersion` parametrem ( `@p2` ), žádné řádky se neaktualizují.</span><span class="sxs-lookup"><span data-stu-id="651e1-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="651e1-401">Následující zvýrazněný kód ukazuje T-SQL, který ověří právě jeden řádek, který byl aktualizován:</span><span class="sxs-lookup"><span data-stu-id="651e1-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="651e1-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Vrátí počet řádků ovlivněných posledním příkazem.</span><span class="sxs-lookup"><span data-stu-id="651e1-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="651e1-403">V Neaktualizování řádků EF Core vyvolá `DbUpdateConcurrencyException` .</span><span class="sxs-lookup"><span data-stu-id="651e1-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="651e1-404">V okně výstup aplikace Visual Studio můžete zobrazit EF Core T-SQL.</span><span class="sxs-lookup"><span data-stu-id="651e1-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="651e1-405">Aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="651e1-405">Update the DB</span></span>

<span data-ttu-id="651e1-406">Přidáním `RowVersion` vlastnosti se změní model databáze, který vyžaduje migraci.</span><span class="sxs-lookup"><span data-stu-id="651e1-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="651e1-407">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="651e1-407">Build the project.</span></span> <span data-ttu-id="651e1-408">V příkazovém okně zadejte následující:</span><span class="sxs-lookup"><span data-stu-id="651e1-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="651e1-409">Předchozí příkazy:</span><span class="sxs-lookup"><span data-stu-id="651e1-409">The preceding commands:</span></span>

* <span data-ttu-id="651e1-410">Přidá migrační soubor *_RowVersion. cs migrace/{časového razítka}* .</span><span class="sxs-lookup"><span data-stu-id="651e1-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="651e1-411">Aktualizuje soubor *migrations/SchoolContextModelSnapshot. cs* .</span><span class="sxs-lookup"><span data-stu-id="651e1-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="651e1-412">Aktualizace přidá do metody následující zvýrazněný kód `BuildModel` :</span><span class="sxs-lookup"><span data-stu-id="651e1-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="651e1-413">Spustí migrace, aby se aktualizovala databáze.</span><span class="sxs-lookup"><span data-stu-id="651e1-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="651e1-414">Generování uživatelského rozhraní modelu oddělení</span><span class="sxs-lookup"><span data-stu-id="651e1-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="651e1-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="651e1-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="651e1-416">Postupujte podle pokynů v [části generátor a model student](xref:data/ef-rp/intro#scaffold-student-pages) a použijte `Department` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="651e1-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="651e1-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="651e1-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="651e1-418">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="651e1-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="651e1-419">Předchozí příkaz vygeneruje `Department` model.</span><span class="sxs-lookup"><span data-stu-id="651e1-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="651e1-420">Otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="651e1-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="651e1-421">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="651e1-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="651e1-422">Aktualizovat stránku indexu oddělení</span><span class="sxs-lookup"><span data-stu-id="651e1-422">Update the Departments Index page</span></span>

<span data-ttu-id="651e1-423">Modul generování uživatelského rozhraní vytvořil `RowVersion` sloupec pro stránku indexu, ale toto pole by se nemělo zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="651e1-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="651e1-424">V tomto kurzu se zobrazí poslední bajt, `RowVersion` který vám pomůže pochopit souběžnost.</span><span class="sxs-lookup"><span data-stu-id="651e1-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="651e1-425">Poslední bajt není zaručený jako jedinečný.</span><span class="sxs-lookup"><span data-stu-id="651e1-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="651e1-426">Skutečná aplikace se nezobrazila `RowVersion` nebo poslední bajt `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="651e1-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="651e1-427">Aktualizujte stránku indexu:</span><span class="sxs-lookup"><span data-stu-id="651e1-427">Update the Index page:</span></span>

* <span data-ttu-id="651e1-428">Nahraďte index "odděleními".</span><span class="sxs-lookup"><span data-stu-id="651e1-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="651e1-429">Nahraďte značku obsahující `RowVersion` Poslední bajt `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="651e1-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="651e1-430">Nahraďte FirstMidName řetězcem FullName.</span><span class="sxs-lookup"><span data-stu-id="651e1-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="651e1-431">Následující kód ukazuje aktualizovanou stránku:</span><span class="sxs-lookup"><span data-stu-id="651e1-431">The following markup shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="651e1-432">Aktualizace modelu úprav stránek</span><span class="sxs-lookup"><span data-stu-id="651e1-432">Update the Edit page model</span></span>

<span data-ttu-id="651e1-433">*Pages\Departments\Edit.cshtml.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="651e1-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="651e1-434">Aby se zjistil problém souběžnosti, aktualizuje se [původní](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) `rowVersion` hodnotou z entity, kterou načetla.</span><span class="sxs-lookup"><span data-stu-id="651e1-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="651e1-435">EF Core generuje příkaz SQL UPDATE s klauzulí WHERE obsahující původní `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="651e1-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="651e1-436">Pokud nejsou žádné řádky ovlivněny příkazem UPDATE (žádné řádky nemají původní `RowVersion` hodnotu), `DbUpdateConcurrencyException` je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="651e1-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="651e1-437">V předchozím kódu `Department.RowVersion` je hodnota, když byla entita načtena.</span><span class="sxs-lookup"><span data-stu-id="651e1-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="651e1-438">`OriginalValue` je hodnota v databázi, která `FirstOrDefaultAsync` byla volána v této metodě.</span><span class="sxs-lookup"><span data-stu-id="651e1-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="651e1-439">Následující kód Získá hodnoty klienta (hodnoty odeslané do této metody) a hodnoty databáze:</span><span class="sxs-lookup"><span data-stu-id="651e1-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="651e1-440">Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišné od odeslání do `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="651e1-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="651e1-441">Následující zvýrazněný kód nastaví `RowVersion` hodnotu na novou hodnotu načtenou z databáze.</span><span class="sxs-lookup"><span data-stu-id="651e1-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="651e1-442">Až uživatel příště klikne na možnost **Uložit**, bude zachycena pouze chyba souběžnosti, ke kterým dochází od posledního zobrazení stránky pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="651e1-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="651e1-443">`ModelState.Remove`Příkaz je vyžadován, protože `ModelState` má starou `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="651e1-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="651e1-444">Na Razor stránce `ModelState` má hodnota pro pole přednost před hodnotami vlastností modelu, pokud jsou oba přítomny.</span><span class="sxs-lookup"><span data-stu-id="651e1-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="651e1-445">Aktualizace stránky pro úpravy</span><span class="sxs-lookup"><span data-stu-id="651e1-445">Update the Edit page</span></span>

<span data-ttu-id="651e1-446">Aktualizovat *stránky/oddělení/upravit. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="651e1-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="651e1-447">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="651e1-447">The preceding markup:</span></span>

* <span data-ttu-id="651e1-448">Aktualizuje `page` direktivu z `@page` na `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="651e1-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="651e1-449">Přidá verzi skrytého řádku.</span><span class="sxs-lookup"><span data-stu-id="651e1-449">Adds a hidden row version.</span></span> <span data-ttu-id="651e1-450">`RowVersion` je nutné přidat, aby postback znovu váže hodnotu.</span><span class="sxs-lookup"><span data-stu-id="651e1-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="651e1-451">Zobrazí poslední bajt `RowVersion` pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="651e1-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="651e1-452">Nahrazuje `ViewData` silným typem `InstructorNameSL` .</span><span class="sxs-lookup"><span data-stu-id="651e1-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="651e1-453">Souběžnost testů v konfliktu se stránkou pro úpravy</span><span class="sxs-lookup"><span data-stu-id="651e1-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="651e1-454">V anglickém oddělení otevřete dvě instance pro úpravy v angličtině:</span><span class="sxs-lookup"><span data-stu-id="651e1-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="651e1-455">Spusťte aplikaci a vyberte oddělení.</span><span class="sxs-lookup"><span data-stu-id="651e1-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="651e1-456">Klikněte pravým tlačítkem **na hypertextový** odkaz pro jazykové oddělení a vyberte **otevřít na nové kartě**.</span><span class="sxs-lookup"><span data-stu-id="651e1-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="651e1-457">Na první kartě klikněte na odkaz **Upravit** pro anglické oddělení.</span><span class="sxs-lookup"><span data-stu-id="651e1-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="651e1-458">Dvě karty prohlížeče zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="651e1-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="651e1-459">Změňte název na první kartě prohlížeče a klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="651e1-459">Change the name in the first browser tab and click **Save**.</span></span>

![Stránka pro úpravy v oddělení 1 po změně](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="651e1-461">Prohlížeč zobrazí stránku index se změněnou hodnotou a aktualizovaným indikátorem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="651e1-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="651e1-462">Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazuje na druhém panelu pro odeslání na druhé kartě.</span><span class="sxs-lookup"><span data-stu-id="651e1-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="651e1-463">Změňte jiné pole na druhé kartě prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="651e1-463">Change a different field in the second browser tab.</span></span>

![Stránka pro úpravy v oddělení 2 po změně](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="651e1-465">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="651e1-465">Click **Save**.</span></span> <span data-ttu-id="651e1-466">Zobrazí se chybové zprávy pro všechna pole, která neodpovídají hodnotám databáze:</span><span class="sxs-lookup"><span data-stu-id="651e1-466">You see error messages for all fields that don't match the DB values:</span></span>

![Chybová zpráva stránky pro úpravu oddělení](concurrency/_static/edit-error.png)

<span data-ttu-id="651e1-468">Toto okno prohlížeče nemá v úmyslu změnit pole název.</span><span class="sxs-lookup"><span data-stu-id="651e1-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="651e1-469">Zkopírujte a vložte aktuální hodnotu (jazyky) do pole název.</span><span class="sxs-lookup"><span data-stu-id="651e1-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="651e1-470">Tabulátor. Ověřování na straně klienta odebere chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="651e1-470">Tab out. Client-side validation removes the error message.</span></span>

![Chybová zpráva stránky pro úpravu oddělení](concurrency/_static/cv.png)

<span data-ttu-id="651e1-472">Znovu klikněte na **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="651e1-472">Click **Save** again.</span></span> <span data-ttu-id="651e1-473">Hodnota, kterou jste zadali na druhé záložce prohlížeče, se uloží.</span><span class="sxs-lookup"><span data-stu-id="651e1-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="651e1-474">Uložené hodnoty se zobrazí na stránce index.</span><span class="sxs-lookup"><span data-stu-id="651e1-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="651e1-475">Aktualizace stránky pro odstranění</span><span class="sxs-lookup"><span data-stu-id="651e1-475">Update the Delete page</span></span>

<span data-ttu-id="651e1-476">Aktualizujte odstranit model stránky pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="651e1-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="651e1-477">Stránka odstranit detekuje konflikty souběžnosti, když se entita po jejím načtení změnila.</span><span class="sxs-lookup"><span data-stu-id="651e1-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="651e1-478">`Department.RowVersion` je verze řádku v případě, že byla entita načtena.</span><span class="sxs-lookup"><span data-stu-id="651e1-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="651e1-479">Když EF Core vytvoří příkaz SQL DELETE, zahrnuje klauzuli WHERE s `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="651e1-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="651e1-480">Pokud příkaz SQL DELETE má vliv na nulové řádky:</span><span class="sxs-lookup"><span data-stu-id="651e1-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="651e1-481">`RowVersion`Příkaz v příkazu SQL DELETE se v databázi neshoduje `RowVersion` .</span><span class="sxs-lookup"><span data-stu-id="651e1-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="651e1-482">Je vyvolána výjimka DbUpdateConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="651e1-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="651e1-483">`OnGetAsync` je volána pomocí `concurrencyError` .</span><span class="sxs-lookup"><span data-stu-id="651e1-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="651e1-484">Aktualizace stránky pro odstranění</span><span class="sxs-lookup"><span data-stu-id="651e1-484">Update the Delete page</span></span>

<span data-ttu-id="651e1-485">Aktualizovat *stránky/oddělení/odstranit. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="651e1-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="651e1-486">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="651e1-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="651e1-487">Aktualizuje `page` direktivu z `@page` na `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="651e1-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="651e1-488">Přidá chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="651e1-488">Adds an error message.</span></span>
* <span data-ttu-id="651e1-489">Nahradí FirstMidName pomocí FullName v poli **správce** .</span><span class="sxs-lookup"><span data-stu-id="651e1-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="651e1-490">Změny `RowVersion` pro zobrazení posledního bajtu</span><span class="sxs-lookup"><span data-stu-id="651e1-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="651e1-491">Přidá verzi skrytého řádku.</span><span class="sxs-lookup"><span data-stu-id="651e1-491">Adds a hidden row version.</span></span> <span data-ttu-id="651e1-492">`RowVersion` je nutné přidat, aby postback znovu váže hodnotu.</span><span class="sxs-lookup"><span data-stu-id="651e1-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="651e1-493">Souběžnost testů v konfliktu se stránkou odstranit</span><span class="sxs-lookup"><span data-stu-id="651e1-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="651e1-494">Vytvořte testovací oddělení.</span><span class="sxs-lookup"><span data-stu-id="651e1-494">Create a test department.</span></span>

<span data-ttu-id="651e1-495">V testovacím středisku otevřete dvě instance pro odstraňování těchto prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="651e1-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="651e1-496">Spusťte aplikaci a vyberte oddělení.</span><span class="sxs-lookup"><span data-stu-id="651e1-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="651e1-497">Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** pro testovací oddělení a vyberte **otevřít na nové kartě**.</span><span class="sxs-lookup"><span data-stu-id="651e1-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="651e1-498">Klikněte na odkaz **Upravit** pro testovací oddělení.</span><span class="sxs-lookup"><span data-stu-id="651e1-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="651e1-499">Dvě karty prohlížeče zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="651e1-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="651e1-500">Změňte rozpočet na první kartě prohlížeče a klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="651e1-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="651e1-501">Prohlížeč zobrazí stránku index se změněnou hodnotou a aktualizovaným indikátorem rowVersion.</span><span class="sxs-lookup"><span data-stu-id="651e1-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="651e1-502">Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazuje na druhém panelu pro odeslání na druhé kartě.</span><span class="sxs-lookup"><span data-stu-id="651e1-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="651e1-503">Odstraňte testovací oddělení z druhé karty. Chyba souběžnosti se zobrazuje s aktuálními hodnotami z databáze.</span><span class="sxs-lookup"><span data-stu-id="651e1-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="651e1-504">Kliknutím na **Odstranit** odstraníte entitu, pokud `RowVersion` se neaktualizovala.</span><span class="sxs-lookup"><span data-stu-id="651e1-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

<span data-ttu-id="651e1-505">Přečtěte si téma [dědičnosti](xref:data/ef-mvc/inheritance) způsobu dědění datového modelu.</span><span class="sxs-lookup"><span data-stu-id="651e1-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="651e1-506">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="651e1-506">Additional resources</span></span>

* [<span data-ttu-id="651e1-507">Tokeny souběžnosti v EF Core</span><span class="sxs-lookup"><span data-stu-id="651e1-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="651e1-508">Zpracování souběžnosti v EF Core</span><span class="sxs-lookup"><span data-stu-id="651e1-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="651e1-509">Verze tohoto kurzu pro YouTube (zpracování konfliktů souběžnosti)</span><span class="sxs-lookup"><span data-stu-id="651e1-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="651e1-510">Verze tohoto kurzu pro YouTube (část 2)</span><span class="sxs-lookup"><span data-stu-id="651e1-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="651e1-511">Verze tohoto kurzu pro YouTube (část 3)</span><span class="sxs-lookup"><span data-stu-id="651e1-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="651e1-512">Předchozí</span><span class="sxs-lookup"><span data-stu-id="651e1-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

