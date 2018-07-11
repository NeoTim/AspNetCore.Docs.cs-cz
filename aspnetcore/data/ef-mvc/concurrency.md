---
title: ASP.NET Core MVC s EF Core – souběžnosti - 8, 10.
author: rick-anderson
description: Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
ms.author: tdykstra
ms.date: 03/15/2017
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 9bf65621213c9657232dfff1701c9937d5105a9c
ms.sourcegitcommit: b8a2f14bf8dd346d7592977642b610bbcb0b0757
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38186634"
---
# <a name="aspnet-core-mvc-with-ef-core---concurrency---8-of-10"></a><span data-ttu-id="00427-103">ASP.NET Core MVC s EF Core – souběžnosti - 8, 10.</span><span class="sxs-lookup"><span data-stu-id="00427-103">ASP.NET Core MVC with EF Core - Concurrency - 8 of 10</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc-21.md)]

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="00427-104">Podle [Petr Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="00427-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="00427-105">Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvářet webové aplikace ASP.NET Core MVC pomocí Entity Framework Core a Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="00427-105">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="00427-106">Informace o této sérii kurzů, naleznete v tématu [z prvního kurzu této série](intro.md).</span><span class="sxs-lookup"><span data-stu-id="00427-106">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="00427-107">V předchozích kurzech jste zjistili, jak aktualizovat data.</span><span class="sxs-lookup"><span data-stu-id="00427-107">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="00427-108">Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="00427-108">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="00427-109">Vytvoříte webové stránky, které pracují s entitou oddělení a zpracování chyb, které souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="00427-109">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="00427-110">Upravit a odstranit stránky, včetně některé zprávy, které se zobrazí, pokud dojde ke konfliktu souběžnosti na následujících obrázcích.</span><span class="sxs-lookup"><span data-stu-id="00427-110">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Stránky pro úpravu oddělení](concurrency/_static/edit-error.png)

![Odstranění stránky oddělení](concurrency/_static/delete-error.png)

## <a name="concurrency-conflicts"></a><span data-ttu-id="00427-113">Konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="00427-113">Concurrency conflicts</span></span>

<span data-ttu-id="00427-114">Ke konfliktu souběžnosti dochází, když jeden uživatel zobrazuje entity data abyste ji mohli editovat a aktualizuje stejné entity data jiným uživatelem než změnu první uživatele je zapsána do databáze.</span><span class="sxs-lookup"><span data-stu-id="00427-114">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="00427-115">Pokud nepovolíte detekce takové konflikty je, kdo aktualizuje databázi poslední přepíše změny dalších uživatelů.</span><span class="sxs-lookup"><span data-stu-id="00427-115">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="00427-116">V mnoha aplikacích je přijatelné toto riziko: Pokud existuje několik uživatelů nebo několik aktualizací, nebo pokud není skutečně důležité, pokud některé změny budou přepsány, výhody vyváží nižší náklady na programování pro souběžnost.</span><span class="sxs-lookup"><span data-stu-id="00427-116">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="00427-117">V takovém případě není nutné nakonfigurovat aplikaci pro zpracování konfliktů souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="00427-117">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="00427-118">Pesimistická souběžnost (uzamčení)</span><span class="sxs-lookup"><span data-stu-id="00427-118">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="00427-119">Pokud vaše aplikace potřebuje se tak ztrátě dat ve scénářích souběžnosti, je to udělat jedním ze způsobů použití uzamčení databáze.</span><span class="sxs-lookup"><span data-stu-id="00427-119">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="00427-120">Tomu se říká Pesimistická souběžnost.</span><span class="sxs-lookup"><span data-stu-id="00427-120">This is called pessimistic concurrency.</span></span> <span data-ttu-id="00427-121">Například předtím, než se pustíte do čtení řádku z databáze, můžete požádat o zámek pro jen pro čtení nebo pro přístup k aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="00427-121">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="00427-122">Pokud řádek pro aktualizaci přístup, žádné uživatelé můžou zamknout řádku, buď pro jen pro čtení nebo aktualizaci přístup, protože by dostanou kopii dat, která se právě mění.</span><span class="sxs-lookup"><span data-stu-id="00427-122">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="00427-123">Pokud řádek pro přístup jen pro čtení, ostatní také zařízení Uzamknout pro přístup jen pro čtení, ale ne pro aktualizace.</span><span class="sxs-lookup"><span data-stu-id="00427-123">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="00427-124">Zámky pro správu má nevýhody.</span><span class="sxs-lookup"><span data-stu-id="00427-124">Managing locks has disadvantages.</span></span> <span data-ttu-id="00427-125">Může být složité do programu.</span><span class="sxs-lookup"><span data-stu-id="00427-125">It can be complex to program.</span></span> <span data-ttu-id="00427-126">Vyžaduje významné databáze správy zdrojů, a to může způsobit problémy s výkonem jako počet uživatelů aplikace zvyšuje.</span><span class="sxs-lookup"><span data-stu-id="00427-126">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="00427-127">Z těchto důvodů ne všechny systémy správy databáze nepodporují Pesimistická souběžnost.</span><span class="sxs-lookup"><span data-stu-id="00427-127">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="00427-128">Entity Framework Core obsahuje předdefinovanou podporu pro ni a v tomto kurzu nezobrazí způsobu jeho implementace.</span><span class="sxs-lookup"><span data-stu-id="00427-128">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="00427-129">Optimistická souběžnost</span><span class="sxs-lookup"><span data-stu-id="00427-129">Optimistic Concurrency</span></span>

<span data-ttu-id="00427-130">Alternativa k Pesimistická souběžnost je Optimistická souběžnost.</span><span class="sxs-lookup"><span data-stu-id="00427-130">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="00427-131">Povolení konfliktů souběžnosti, která se provede a reaguje správně, pokud tomu znamená, že optimistického řízení souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="00427-131">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="00427-132">Například Jana navštíví stránku Upravit oddělení a změní hodnotu rozpočtu pro anglickou oddělení z $350,000.00 na 0.00 $.</span><span class="sxs-lookup"><span data-stu-id="00427-132">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="00427-134">Předtím, než Jan klikne **Uložit**, Jan navštíví na stejnou stránku a změny pole Datum zahájení 9/1/2013 z 9/1/2007.</span><span class="sxs-lookup"><span data-stu-id="00427-134">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Změna počátečního data a 2013](concurrency/_static/change-date.png)

<span data-ttu-id="00427-136">Jan klikne **Uložit** první a jí změnit návratu na indexovou stránku v prohlížeči se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="00427-136">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Změnit na hodnotu nula rozpočtu](concurrency/_static/budget-zero.png)

<span data-ttu-id="00427-138">Pak Jan klikne **Uložit** na stránce Upravit, která stále zobrazuje rozpočtu 350,000.00 $.</span><span class="sxs-lookup"><span data-stu-id="00427-138">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="00427-139">Co bude dál se určuje podle způsobu zpracování konfliktů souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="00427-139">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="00427-140">Mezi možnosti patří následující:</span><span class="sxs-lookup"><span data-stu-id="00427-140">Some of the options include the following:</span></span>

* <span data-ttu-id="00427-141">Můžete sledovat, které vlastnosti uživatele byl změněn a aktualizovat pouze odpovídající sloupce v databázi.</span><span class="sxs-lookup"><span data-stu-id="00427-141">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="00427-142">V ukázkovém scénáři žádné by dojít ke ztrátě dat., protože různé vlastnosti byly aktualizovány dva uživatelé.</span><span class="sxs-lookup"><span data-stu-id="00427-142">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="00427-143">Při příštím někdo přejde z anglické oddělení, zobrazí se Jana a John's na změny – datum zahájení o 9/1/2013 a rozpočet nulové dolarů.</span><span class="sxs-lookup"><span data-stu-id="00427-143">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="00427-144">Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě, ale nemůže zamezení ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost entity.</span><span class="sxs-lookup"><span data-stu-id="00427-144">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="00427-145">Ať už rozhraní Entity Framework funguje tímto způsobem závisí na implementace aktualizace kódu.</span><span class="sxs-lookup"><span data-stu-id="00427-145">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="00427-146">Není často praktické ve webové aplikaci, protože to může vyžadovat spravovat velké množství stavu aby bylo možné udržovat přehled o všech původní hodnoty vlastností pro entitu a nové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="00427-146">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="00427-147">Správa velkého objemu stavu může ovlivnit výkon aplikace, protože ji vyžaduje prostředky serveru nebo musí být součástí webové stránky (například v skrytá pole) nebo do souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="00427-147">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="00427-148">Můžete nechat John's na změnu Jana změna přepsána.</span><span class="sxs-lookup"><span data-stu-id="00427-148">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="00427-149">Při příštím někdo přejde z anglické oddělení, zobrazí se 9/1/2013 a obnovený $350,000.00 hodnotu.</span><span class="sxs-lookup"><span data-stu-id="00427-149">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="00427-150">Tento postup se nazývá *Wins, klient* nebo *poslední ve službě Wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="00427-150">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="00427-151">(Všechny hodnoty z klienta přednost co je v úložišti.) Jak jsme uvedli v úvodu do této části, pokud tak učiníte vytvářet kód pro zpracování souběžnosti, k tomu dochází automaticky.</span><span class="sxs-lookup"><span data-stu-id="00427-151">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="00427-152">John's na změnu může zabránit aktualizují v databázi.</span><span class="sxs-lookup"><span data-stu-id="00427-152">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="00427-153">By obvykle zobrazí chybovou zprávu, zobrazí jeho aktuální stav dat a mu umožní se jeho změny znovu použijte, pokud chce je.</span><span class="sxs-lookup"><span data-stu-id="00427-153">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="00427-154">Tento postup se nazývá *Store Wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="00427-154">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="00427-155">(Hodnoty úložiště dat přednost hodnoty odeslány klientem.) V tomto kurzu budete implementovat scénář Store Wins.</span><span class="sxs-lookup"><span data-stu-id="00427-155">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="00427-156">Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění na co se děje.</span><span class="sxs-lookup"><span data-stu-id="00427-156">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="00427-157">Zjišťování konfliktů souběžnosti</span><span class="sxs-lookup"><span data-stu-id="00427-157">Detecting concurrency conflicts</span></span>

<span data-ttu-id="00427-158">Konflikty lze vyřešit zpracování `DbConcurrencyException` výjimky, které vyvolá rozhraní Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="00427-158">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="00427-159">Pokud chcete zjistit, kdy se má vyvolat tyto výjimky, musí být schopen rozpoznat konflikty Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="00427-159">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="00427-160">Proto je nutné nakonfigurovat databázi a datový model odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="00427-160">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="00427-161">Některé možnosti aktivace zjišťování konfliktů, patří:</span><span class="sxs-lookup"><span data-stu-id="00427-161">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="00427-162">V tabulce databáze zahrnují sledování sloupec, který slouží k určení, kdy změnil řádek.</span><span class="sxs-lookup"><span data-stu-id="00427-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="00427-163">Potom můžete nakonfigurovat rozhraní Entity Framework zahrnout sloupce Where – klauzule SQL aktualizace a odstranění příkazů.</span><span class="sxs-lookup"><span data-stu-id="00427-163">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="00427-164">Datový typ sloupce pro sledování je obvykle `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="00427-164">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="00427-165">`rowversion` Hodnotu pořadové číslo, které se zvýší při každé aktualizaci řádku.</span><span class="sxs-lookup"><span data-stu-id="00427-165">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="00427-166">V příkazu Update nebo Delete Where – klauzule obsahuje původní hodnota sloupce pro sledování (původní verze řádku).</span><span class="sxs-lookup"><span data-stu-id="00427-166">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="00427-167">Pokud se změnila řádek aktualizován jiným uživatelem, hodnota v `rowversion` sloupce se liší od původní hodnotu, aby příkazu Update nebo Delete nelze najít řádek aktualizovat z důvodu Where – klauzule.</span><span class="sxs-lookup"><span data-stu-id="00427-167">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="00427-168">Když najde Entity Framework, že byly aktualizovány žádné řádky podle Update nebo Delete příkazu (to znamená, když počet ovlivněných řádků je nula), interpretuje, který jako ke konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="00427-168">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="00427-169">Konfigurace rozhraní Entity Framework pro zahrnutí původní hodnota každý sloupec v tabulce v Where klauzule příkazy Update a Delete.</span><span class="sxs-lookup"><span data-stu-id="00427-169">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="00427-170">Jako první možnost, pokud něco v řádku změnilo od řádku se nejdřív přečíst Where – klauzule nevrátí řádek k aktualizaci, která nastavení interpretuje Entity Framework jako ke konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="00427-170">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="00427-171">Pro databázové tabulky, které mají mnoho sloupců, tento přístup může vést k velmi velké Where klauzule a můžete požadovat, že udržujete velké množství stavu.</span><span class="sxs-lookup"><span data-stu-id="00427-171">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="00427-172">Jak bylo uvedeno dříve, udržování velké množství stavu může ovlivnit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="00427-172">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="00427-173">Proto tento postup se obecně nedoporučuje, které není metoda použitá v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="00427-173">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="00427-174">Pokud chcete k implementaci tohoto přístupu se souběžností, budete muset označit všechny vlastnosti primárního klíče v entitě, kterou chcete sledovat souběžnosti pro tak, že přidáte `ConcurrencyCheck` atributu na ně.</span><span class="sxs-lookup"><span data-stu-id="00427-174">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="00427-175">Tato změna umožňuje rozhraní Entity Framework zahrňte všechny sloupce v klauzuli Where příkazu SQL příkazy Update a Delete.</span><span class="sxs-lookup"><span data-stu-id="00427-175">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="00427-176">Ve zbývající části tohoto kurzu přidáte `rowversion` vlastnosti sledování do entity oddělení, vytvořit kontroler a zobrazení a otestovat a ověřit, že vše funguje správně.</span><span class="sxs-lookup"><span data-stu-id="00427-176">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="00427-177">Přidání vlastnosti sledování do entity oddělení</span><span class="sxs-lookup"><span data-stu-id="00427-177">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="00427-178">V *Models/Department.cs*, přidání vlastnosti sledování do s názvem RowVersion:</span><span class="sxs-lookup"><span data-stu-id="00427-178">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="00427-179">`Timestamp` Atribut určuje, zda tento sloupec součástí Where klauzule příkazy Update a Delete odešlou do databáze.</span><span class="sxs-lookup"><span data-stu-id="00427-179">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="00427-180">Atribut se nazývá `Timestamp` protože předchozích verzí SQL serveru použít SQL `timestamp` datového typu než SQL `rowversion` nahradili jsme ho.</span><span class="sxs-lookup"><span data-stu-id="00427-180">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="00427-181">Typ formátu .NET pro `rowversion` bajtové pole.</span><span class="sxs-lookup"><span data-stu-id="00427-181">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="00427-182">Pokud chcete použít rozhraní fluent API, můžete použít `IsConcurrencyToken` – metoda (v *Data/SchoolContext.cs*) pro určení této vlastnosti sledování, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="00427-182">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="00427-183">Přidáním vlastnosti změnit model databáze, takže je třeba provést další migraci.</span><span class="sxs-lookup"><span data-stu-id="00427-183">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="00427-184">Uložte změny a sestavte projekt a potom zadejte následující příkazy v příkazovém okně:</span><span class="sxs-lookup"><span data-stu-id="00427-184">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```console
dotnet ef migrations add RowVersion
```

```console
dotnet ef database update
```

## <a name="create-a-departments-controller-and-views"></a><span data-ttu-id="00427-185">Vytvoření kontroleru oddělení a zobrazení</span><span class="sxs-lookup"><span data-stu-id="00427-185">Create a Departments controller and views</span></span>

<span data-ttu-id="00427-186">Stejně jako dříve pro studenty, kurzy a vyučující, generování uživatelského rozhraní oddělení kontroler a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="00427-186">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Oddělení vygenerované uživatelské rozhraní](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="00427-188">V *DepartmentsController.cs* souborů, změňte všechny čtyři výskyty "FirstMidName" na "FullName" tak, aby oddělení správce rozevírací seznamy bude obsahovat úplný název instruktorem, nikoli pouze poslední název.</span><span class="sxs-lookup"><span data-stu-id="00427-188">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-the-departments-index-view"></a><span data-ttu-id="00427-189">Aktualizace zobrazení Index oddělení</span><span class="sxs-lookup"><span data-stu-id="00427-189">Update the Departments Index view</span></span>

<span data-ttu-id="00427-190">Generování uživatelského rozhraní stroj vytvořil RowVersion sloupec v indexu zobrazení, ale nebude se zobrazovat toto pole.</span><span class="sxs-lookup"><span data-stu-id="00427-190">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="00427-191">Nahraďte kód v *Views/Departments/Index.cshtml* následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="00427-191">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="00427-192">To se změní na záhlaví "Oddělení", odstraní sloupec RowVersion a zobrazí jméno a příjmení namísto křestní jméno správce.</span><span class="sxs-lookup"><span data-stu-id="00427-192">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-the-edit-methods-in-the-departments-controller"></a><span data-ttu-id="00427-193">Aktualizace metod Edit v oddělení kontroleru</span><span class="sxs-lookup"><span data-stu-id="00427-193">Update the Edit methods in the Departments controller</span></span>

<span data-ttu-id="00427-194">V obou třídy MetadataExchangeClientMode `Edit` metoda a `Details` metodu, přidejte `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="00427-194">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="00427-195">V třídy MetadataExchangeClientMode `Edit` metodu, přidejte předběžné načítání pro správce.</span><span class="sxs-lookup"><span data-stu-id="00427-195">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading&highlight=2,3)]

<span data-ttu-id="00427-196">Nahraďte stávající kód httppost `Edit` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="00427-196">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="00427-197">Kód začíná pokusu o čtení z oddělení aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="00427-197">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="00427-198">Pokud `SingleOrDefaultAsync` metoda vrátí hodnotu null, z oddělení byla odstraněna jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="00427-198">If the `SingleOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="00427-199">V takovém případě kód používá hodnoty odeslaného formuláře vytvořit entitu oddělení tak, aby stránky pro úpravu můžete zobrazí znovu, zobrazí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="00427-199">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="00427-200">Jako alternativu nebude muset znovu vytvořit entity oddělení, pokud zobrazení pouze chybové zprávy bez opětovné zobrazení pole oddělení.</span><span class="sxs-lookup"><span data-stu-id="00427-200">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="00427-201">Zobrazení ukládá původní `RowVersion` hodnotu ve skrytém poli a tato metoda přijímá hodnotu do `rowVersion` parametru.</span><span class="sxs-lookup"><span data-stu-id="00427-201">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="00427-202">Před voláním `SaveChanges`, budete muset vytvořit z původní `RowVersion` hodnoty vlastnosti `OriginalValues` kolekce entity.</span><span class="sxs-lookup"><span data-stu-id="00427-202">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="00427-203">Pak při Entity Framework vytvoří příkaz SQL pro sadu VS11, tohoto příkazu bude obsahovat klauzuli WHERE, která hledá řádek, který má původní `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="00427-203">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="00427-204">Pokud žádné řádky jsou ovlivněny příkazu UPDATE (žádné řádky mít původní `RowVersion` hodnota), vyvolá rozhraní Entity Framework `DbUpdateConcurrencyException` výjimky.</span><span class="sxs-lookup"><span data-stu-id="00427-204">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="00427-205">Kód v bloku catch pro tuto výjimku získá ovlivněné oddělení entity, která má aktualizované hodnoty z `Entries` vlastnost v objektu výjimky.</span><span class="sxs-lookup"><span data-stu-id="00427-205">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="00427-206">`Entries` Kolekce bude obsahovat jen jeden `EntityEntry` objektu.</span><span class="sxs-lookup"><span data-stu-id="00427-206">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="00427-207">Tento objekt slouží k získání nové hodnoty zadané uživatelem a hodnot v aktuální databázi.</span><span class="sxs-lookup"><span data-stu-id="00427-207">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="00427-208">Kód přidá vlastní chybovou zprávu pro každý sloupec, který má různých hodnot v databázi z co uživatel zadaný v úpravách stránky (pouze jedno pole je tady pro zkrácení).</span><span class="sxs-lookup"><span data-stu-id="00427-208">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="00427-209">Nakonec kód nastaví `RowVersion` hodnotu `departmentToUpdate` na novou hodnotu načtených z databáze.</span><span class="sxs-lookup"><span data-stu-id="00427-209">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="00427-210">Tato nová `RowVersion` hodnota bude uložena ve skrytém poli, když upravit stránka se zobrazí znovu a další čas kliknutí **Uložit**, pouze souběžnosti chyby, ke kterým dochází, protože redisplay stránky pro úpravu bude zachycena.</span><span class="sxs-lookup"><span data-stu-id="00427-210">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="00427-211">`ModelState.Remove` Příkazu se totiž `ModelState` má starý `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="00427-211">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="00427-212">V zobrazení `ModelState` hodnota pole má přednost před hodnoty vlastností modelu Pokud jsou obě přítomny.</span><span class="sxs-lookup"><span data-stu-id="00427-212">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-department-edit-view"></a><span data-ttu-id="00427-213">Aktualizace zobrazení pro úpravy pro oddělení</span><span class="sxs-lookup"><span data-stu-id="00427-213">Update the Department Edit view</span></span>

<span data-ttu-id="00427-214">V *Views/Departments/Edit.cshtml*, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="00427-214">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="00427-215">Přidání skrytého pole k uložení `RowVersion` hodnotu vlastnosti, hned za skryté pole pro `DepartmentID` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="00427-215">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="00427-216">Přidejte do rozevíracího seznamu možnost "Vybrat Administrator".</span><span class="sxs-lookup"><span data-stu-id="00427-216">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts-in-the-edit-page"></a><span data-ttu-id="00427-217">Na stránce Upravit test konfliktů souběžnosti</span><span class="sxs-lookup"><span data-stu-id="00427-217">Test concurrency conflicts in the Edit page</span></span>

<span data-ttu-id="00427-218">Spusťte aplikaci a přejděte na stránku oddělení indexu.</span><span class="sxs-lookup"><span data-stu-id="00427-218">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="00427-219">Klikněte pravým tlačítkem na **upravit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě**, klikněte **upravit** hypertextového odkazu pro anglickou oddělení.</span><span class="sxs-lookup"><span data-stu-id="00427-219">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="00427-220">Prohlížeč dvě karty se nyní zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="00427-220">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="00427-221">Změňte pole na první záložce prohlížeče a klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="00427-221">Change a field in the first browser tab and click **Save**.</span></span>

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="00427-223">Prohlížeč zobrazí indexovou stránku s změněné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="00427-223">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="00427-224">Změňte pole na druhé záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="00427-224">Change a field in the second browser tab.</span></span>

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="00427-226">Klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="00427-226">Click **Save**.</span></span> <span data-ttu-id="00427-227">Zobrazí chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="00427-227">You see an error message:</span></span>

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error.png)

<span data-ttu-id="00427-229">Klikněte na tlačítko **Uložit** znovu.</span><span class="sxs-lookup"><span data-stu-id="00427-229">Click **Save** again.</span></span> <span data-ttu-id="00427-230">Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="00427-230">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="00427-231">Uložené hodnoty se zobrazí, jakmile se zobrazí stránka indexu.</span><span class="sxs-lookup"><span data-stu-id="00427-231">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="00427-232">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="00427-232">Update the Delete page</span></span>

<span data-ttu-id="00427-233">Odstranění stránky Entity Framework detekuje souběžnosti konflikty způsobené někdo jinak úpravy oddělení podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="00427-233">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="00427-234">Když třídy MetadataExchangeClientMode `Delete` metoda zobrazí potvrzení zobrazení, zobrazení zahrnuje původní `RowVersion` hodnotu ve skrytém poli.</span><span class="sxs-lookup"><span data-stu-id="00427-234">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="00427-235">Hodnota je pak možné HttpPost `Delete` metodu, která je volána, když uživatel potvrdí odstranění.</span><span class="sxs-lookup"><span data-stu-id="00427-235">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="00427-236">Entity Framework vytvoří příkaz SQL DELETE, obsahuje klauzuli WHERE s původní `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="00427-236">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="00427-237">Pokud vliv na výsledky příkazu v nulový počet řádků (tj. řádek byl změněn, jakmile se zobrazí stránka potvrzení odstranění), je vyvolána výjimka souběžnosti a HttpGet `Delete` metoda je volána k chybě příznak nastaven na hodnotu true, pokud chcete znovu zobrazit potvrzovací stránku s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="00427-237">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="00427-238">Je také možné, že vzhledem k tomu, že řádek byl odstraněn jiným uživatelem, tak v tom případě se zobrazí žádná chybová zpráva vliv nulový počet řádků.</span><span class="sxs-lookup"><span data-stu-id="00427-238">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="00427-239">Aktualizace metod Delete v oddělení kontroleru</span><span class="sxs-lookup"><span data-stu-id="00427-239">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="00427-240">V *DepartmentsController.cs*, nahraďte třídy MetadataExchangeClientMode `Delete` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="00427-240">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="00427-241">Metoda přijímá volitelný parametr, který označuje, zda je právě na stránce zobrazí znovu po chybě souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="00427-241">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="00427-242">Pokud tento příznak má hodnotu true a oddělení již existuje, byla odstraněna jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="00427-242">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="00427-243">V takovém případě kód provede přesměrování na indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="00427-243">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="00427-244">Pokud tento příznak má hodnotu true a oddělení neexistuje, byla změněna jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="00427-244">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="00427-245">V takovém případě kód odešle chybovou zprávu pro zobrazení s využitím `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="00427-245">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="00427-246">Nahraďte kód v HttpPost `Delete` – metoda (s názvem `DeleteConfirmed`) následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="00427-246">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="00427-247">V automaticky generovaný kód, který jste právě nahrazen tato metoda přijaty pouze ID záznamu:</span><span class="sxs-lookup"><span data-stu-id="00427-247">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>


```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="00427-248">Změnili jste tento parametr k oddělení instanci entity vytvořené vazače modelu.</span><span class="sxs-lookup"><span data-stu-id="00427-248">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="00427-249">To poskytuje EF přístup k hodnotě vlastnosti RowVersion kromě klíč záznamu.</span><span class="sxs-lookup"><span data-stu-id="00427-249">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="00427-250">Také jste změnili název metody akce z `DeleteConfirmed` k `Delete`.</span><span class="sxs-lookup"><span data-stu-id="00427-250">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="00427-251">Automaticky generovaný kód používá název `DeleteConfirmed` poskytnout metoda HttpPost jedinečnou signaturu.</span><span class="sxs-lookup"><span data-stu-id="00427-251">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="00427-252">(CLR vyžaduje přetížené metody s parametry jinou metodu.) Teď, když podpisy jsou jedinečné, můžete zůstat u konvence MVC a použijte stejný název pro odstranění metody HttpPost a HttpGet.</span><span class="sxs-lookup"><span data-stu-id="00427-252">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="00427-253">Pokud již byla odstraněna z oddělení, `AnyAsync` metoda vrátí hodnotu false a aplikace právě přejde zpět do metody indexu.</span><span class="sxs-lookup"><span data-stu-id="00427-253">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="00427-254">Pokud došlo k chybě souběžnosti je zachycena, kód znovu zobrazí na stránce potvrzení odstranění a zajišťuje, že příznak, který označují, že by se zobrazit zpráva chybě souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="00427-254">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="00427-255">Zobrazení aktualizovat, odstranit</span><span class="sxs-lookup"><span data-stu-id="00427-255">Update the Delete view</span></span>

<span data-ttu-id="00427-256">V *Views/Departments/Delete.cshtml*, nahraďte následující kód, který přidá polem chybové zprávy a skrytá pole vlastností DepartmentID a RowVersion automaticky generovaný kód.</span><span class="sxs-lookup"><span data-stu-id="00427-256">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="00427-257">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="00427-257">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="00427-258">To provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="00427-258">This makes the following changes:</span></span>

* <span data-ttu-id="00427-259">Přidá chybovou zprávu mezi `h2` a `h3` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="00427-259">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="00427-260">Nahradí celý název v FirstMidName **správce** pole.</span><span class="sxs-lookup"><span data-stu-id="00427-260">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="00427-261">Odebere RowVersion pole.</span><span class="sxs-lookup"><span data-stu-id="00427-261">Removes the RowVersion field.</span></span>

* <span data-ttu-id="00427-262">Přidá skryté pole pro `RowVersion` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="00427-262">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="00427-263">Spusťte aplikaci a přejděte na stránku oddělení indexu.</span><span class="sxs-lookup"><span data-stu-id="00427-263">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="00427-264">Klikněte pravým tlačítkem na **odstranit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě**, na první kartě klikněte na tlačítko **upravit** hypertextového odkazu pro anglickou oddělení.</span><span class="sxs-lookup"><span data-stu-id="00427-264">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="00427-265">V prvním okně, změňte jednu z hodnot a klikněte na tlačítko **Uložit**:</span><span class="sxs-lookup"><span data-stu-id="00427-265">In the first window, change one of the values, and click **Save**:</span></span>

![Stránky pro úpravu oddělení po změně před delete](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="00427-267">Na druhé kartě klikněte **odstranit**.</span><span class="sxs-lookup"><span data-stu-id="00427-267">In the second tab, click **Delete**.</span></span> <span data-ttu-id="00427-268">Zobrazí chybová zpráva souběžnosti a oddělení hodnoty se aktualizují s tím, co je aktuálně v databázi.</span><span class="sxs-lookup"><span data-stu-id="00427-268">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Stránka potvrzení odstranění oddělení s došlo k chybě souběžnosti](concurrency/_static/delete-error.png)

<span data-ttu-id="00427-270">Vyberete-li **odstranit** znovu, budete přesměrováni na indexovou stránku, který ukazuje, že byl odstraněn z oddělení.</span><span class="sxs-lookup"><span data-stu-id="00427-270">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="00427-271">Aktualizovat podrobnosti a vytvořit zobrazení</span><span class="sxs-lookup"><span data-stu-id="00427-271">Update Details and Create views</span></span>

<span data-ttu-id="00427-272">Můžete volitelně vyčistit v podrobnostech o automaticky generovaný kód a vytvořit zobrazení.</span><span class="sxs-lookup"><span data-stu-id="00427-272">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="00427-273">Nahraďte kód v *Views/Departments/Details.cshtml* odstranění RowVersion sloupce a zobrazit úplné jméno správce.</span><span class="sxs-lookup"><span data-stu-id="00427-273">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="00427-274">Nahraďte kód v *Views/Departments/Create.cshtml* vyberte možnost přidat do rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="00427-274">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="summary"></a><span data-ttu-id="00427-275">Souhrn</span><span class="sxs-lookup"><span data-stu-id="00427-275">Summary</span></span>

<span data-ttu-id="00427-276">Dokončení tohoto postupu Úvod ke zpracování konfliktů souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="00427-276">This completes the introduction to handling concurrency conflicts.</span></span> <span data-ttu-id="00427-277">Další informace o tom, jak zpracovat souběžnosti v EF Core najdete v tématu [konfliktů souběžnosti](https://docs.microsoft.com/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="00427-277">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](https://docs.microsoft.com/ef/core/saving/concurrency).</span></span> <span data-ttu-id="00427-278">Další kurz ukazuje postupy při implementaci tabulky na hierarchii dědičnosti pro entity instruktorem a studentů.</span><span class="sxs-lookup"><span data-stu-id="00427-278">The next tutorial shows how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> <span data-ttu-id="00427-279">[Předchozí](update-related-data.md)
> [další](inheritance.md)</span><span class="sxs-lookup"><span data-stu-id="00427-279">[Previous](update-related-data.md)
[Next](inheritance.md)</span></span>
