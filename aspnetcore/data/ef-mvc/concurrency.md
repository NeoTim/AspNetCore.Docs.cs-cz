---
title: 'Kurz: Zpracování souběžnosti - ASP.NET MVC s EF Core'
description: Tento kurz ukazuje, jak zpracovat konflikty, když více uživatelů aktualizovat stejnou entitu ve stejnou dobu.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 6839e383093b993ff55095f26cf88cd68708f001
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657393"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="dc2d9-103">Kurz: Zpracování souběžnosti - ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="dc2d9-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="dc2d9-104">V předchozích kurzech jste se naučili aktualizovat data.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="dc2d9-105">Tento kurz ukazuje, jak zpracovat konflikty, když více uživatelů aktualizovat stejnou entitu ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="dc2d9-106">Vytvoříte webové stránky, které pracují s entitou Oddělení a zpracovávají chyby souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="dc2d9-107">Následující ilustrace znázornit upravit a odstranit stránky, včetně některých zpráv, které jsou zobrazeny, pokud dojde ke konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Stránka Úpravy oddělení](concurrency/_static/edit-error.png)

![Stránka Odstranit oddělení](concurrency/_static/delete-error.png)

<span data-ttu-id="dc2d9-110">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dc2d9-111">Informace o konfliktech souběžnosti</span><span class="sxs-lookup"><span data-stu-id="dc2d9-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="dc2d9-112">Přidání vlastnosti sledování</span><span class="sxs-lookup"><span data-stu-id="dc2d9-112">Add a tracking property</span></span>
> * <span data-ttu-id="dc2d9-113">Vytvořit řadič a zobrazení oddělení</span><span class="sxs-lookup"><span data-stu-id="dc2d9-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="dc2d9-114">Aktualizovat zobrazení indexu</span><span class="sxs-lookup"><span data-stu-id="dc2d9-114">Update Index view</span></span>
> * <span data-ttu-id="dc2d9-115">Aktualizovat metody úprav</span><span class="sxs-lookup"><span data-stu-id="dc2d9-115">Update Edit methods</span></span>
> * <span data-ttu-id="dc2d9-116">Aktualizovat zobrazení pro úpravy</span><span class="sxs-lookup"><span data-stu-id="dc2d9-116">Update Edit view</span></span>
> * <span data-ttu-id="dc2d9-117">Testovat konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="dc2d9-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="dc2d9-118">Aktualizace stránky Odstranit</span><span class="sxs-lookup"><span data-stu-id="dc2d9-118">Update the Delete page</span></span>
> * <span data-ttu-id="dc2d9-119">Aktualizovat podrobnosti a vytvořit zobrazení</span><span class="sxs-lookup"><span data-stu-id="dc2d9-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dc2d9-120">Požadavky</span><span class="sxs-lookup"><span data-stu-id="dc2d9-120">Prerequisites</span></span>

* [<span data-ttu-id="dc2d9-121">Aktualizace souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="dc2d9-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="dc2d9-122">Konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="dc2d9-122">Concurrency conflicts</span></span>

<span data-ttu-id="dc2d9-123">Ke konfliktu souběžnosti dochází, když jeden uživatel zobrazí data entity, aby ji mohl upravit, a potom jiný uživatel aktualizuje data stejné entity před zápisem změny prvního uživatele do databáze.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="dc2d9-124">Pokud nepovolíte zjišťování takových konfliktů, ten, kdo aktualizuje databázi naposledy přepíše změny jiného uživatele.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="dc2d9-125">V mnoha aplikacích je toto riziko přijatelné: pokud existuje jen málo uživatelů nebo málo aktualizací nebo pokud není opravdu důležité, pokud jsou některé změny přepsány, náklady na programování souběžnosti mohou převážit nad přínosem.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="dc2d9-126">V takovém případě není nutné konfigurovat aplikaci pro zpracování konfliktů souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="dc2d9-127">Pesimistický souběžnost (zamykání)</span><span class="sxs-lookup"><span data-stu-id="dc2d9-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="dc2d9-128">Pokud vaše aplikace potřebuje zabránit náhodné ztrátě dat ve scénářích souběžnosti, jedním ze způsobů, jak to udělat, je použití uzamčení databáze.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="dc2d9-129">Tomu se říká pesimistické souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="dc2d9-130">Například před čtením řádku z databáze, požádat o zámek pro jen pro čtení nebo pro přístup k aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="dc2d9-131">Pokud uzamknete řádek pro přístup k aktualizaci, žádný jiný uživatel nemá povoleno uzamknout řádek buď pro přístup jen pro čtení, nebo pro přístup k aktualizaci, protože by získali kopii dat, která se právě mění.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="dc2d9-132">Pokud uzamknete řádek pro přístup jen pro čtení, ostatní mohou také zamknout pro přístup jen pro čtení, ale ne pro aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="dc2d9-133">Správa zámků má nevýhody.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="dc2d9-134">Program může být složitý.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-134">It can be complex to program.</span></span> <span data-ttu-id="dc2d9-135">Vyžaduje významné prostředky pro správu databáze a může způsobit problémy s výkonem, protože se zvyšuje počet uživatelů aplikace.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="dc2d9-136">Z těchto důvodů ne všechny systémy správy databáze podporují pesimistické souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="dc2d9-137">Entity Framework Core neposkytuje žádnou integrovanou podporu pro něj a tento kurz neukazuje, jak ji implementovat.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="dc2d9-138">Optimistická metoda souběžného zpracování</span><span class="sxs-lookup"><span data-stu-id="dc2d9-138">Optimistic Concurrency</span></span>

<span data-ttu-id="dc2d9-139">Alternativou k pesimistické souběžnosti je optimistická souběžnost.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="dc2d9-140">Optimistická souběžnost znamená povolení konfliktů souběžnosti a následné odpovídající reakci, pokud ano.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="dc2d9-141">Jana například navštíví stránku Úpravy oddělení a změní částku rozpočtu pro anglické oddělení z 350 000,00 USD na 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="dc2d9-143">Před kliknutím na tlačítko **Uložit**jan navštíví stejnou stránku a změní pole Datum zahájení z 9.1.2007 na 1.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Změna počátečního data na rok 2013](concurrency/_static/change-date.png)

<span data-ttu-id="dc2d9-145">Jan klikne na **Uložit** jako první a uvidí její změnu, když se prohlížeč vrátí na stránku Rejstřík.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Rozpočet změněn na nulu](concurrency/_static/budget-zero.png)

<span data-ttu-id="dc2d9-147">Potom Jan klikne na **Uložit** na stránce Úpravy, která stále zobrazuje rozpočet 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="dc2d9-148">Co se stane dál, je určena tím, jak zpracovat konflikty souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="dc2d9-149">Některé z možností patří následující:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-149">Some of the options include the following:</span></span>

* <span data-ttu-id="dc2d9-150">Můžete sledovat, kterou vlastnost uživatel upravil, a aktualizovat pouze odpovídající sloupce v databázi.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="dc2d9-151">V ukázkovém scénáři by nedošlo ke ztrátě žádných dat, protože dva uživatelé aktualizovali různé vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="dc2d9-152">Příště, když někdo prochází anglické oddělení, uvidí jak Jane a John změny - datum zahájení 9 / 1 / 2013 a rozpočet nula dolarů.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="dc2d9-153">Tato metoda aktualizace může snížit počet konfliktů, které by mohly vést ke ztrátě dat, ale nemůže zabránit ztrátě dat, pokud jsou provedeny konkurenční změny ve stejné vlastnosti entity.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="dc2d9-154">Zda entity Framework funguje tímto způsobem závisí na tom, jak implementovat kód aktualizace.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="dc2d9-155">Často to není praktické ve webové aplikaci, protože to může vyžadovat udržovat velké množství stavu, aby bylo možné sledovat všechny původní hodnoty vlastností pro entitu, jakož i nové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="dc2d9-156">Udržování velkého množství stavu může ovlivnit výkon aplikace, protože vyžaduje prostředky serveru nebo musí být zahrnuto do samotné webové stránky (například ve skrytých polích) nebo do souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="dc2d9-157">Můžeš nechat Johnovu změnu přepsat Janeinu změnu.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="dc2d9-158">Příště někdo prochází anglické oddělení, uvidí 9 / 1 / 2013 a obnovenou hodnotu $ 350,000.00.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="dc2d9-159">To se nazývá *klient wins* nebo poslední *ve wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="dc2d9-160">(Všechny hodnoty od klienta mají přednost před tím, co je v úložišti dat.) Jak je uvedeno v úvodu k této části, pokud neprovedete žádné kódování pro zpracování souběžnosti, k tomu dojde automaticky.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="dc2d9-161">Můžete zabránit janově změně v aktualizaci v databázi.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="dc2d9-162">Obvykle byste zobrazit chybovou zprávu, zobrazit aktuální stav dat a umožnit mu znovu použít změny, pokud stále chce, aby byly tyto.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="dc2d9-163">Tento scénář se nazývá scénář *Store Wins.*</span><span class="sxs-lookup"><span data-stu-id="dc2d9-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="dc2d9-164">(Hodnoty úložiště dat mají přednost před hodnotami odeslané klientem.) Budete implementovat store wins scénář v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="dc2d9-165">Tato metoda zajišťuje, že žádné změny jsou přepsány bez uživatele upozorněni na to, co se děje.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="dc2d9-166">Zjišťování konfliktů souběžnosti</span><span class="sxs-lookup"><span data-stu-id="dc2d9-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="dc2d9-167">Konflikty můžete vyřešit `DbConcurrencyException` zpracováním výjimek, které lze vyvolat v rámci entit.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="dc2d9-168">Chcete-li vědět, kdy vyvolat tyto výjimky, entity framework musí být schopen detekovat konflikty.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="dc2d9-169">Proto je nutné odpovídajícím způsobem nakonfigurovat databázi a datový model.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="dc2d9-170">Mezi možnosti povolení zjišťování konfliktů patří následující:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="dc2d9-171">V databázové tabulce zahrňte sledovací sloupec, který lze použít k určení, kdy byl řádek změněn.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="dc2d9-172">Potom můžete nakonfigurovat entity Framework zahrnout tento sloupec v Where klauzule SQL Update nebo Delete příkazy.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="dc2d9-173">Datový typ sloupce sledování je `rowversion`obvykle .</span><span class="sxs-lookup"><span data-stu-id="dc2d9-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="dc2d9-174">Hodnota `rowversion` je pořadové číslo, které se při každé aktualizaci řádku zintáží.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="dc2d9-175">V příkazu Aktualizovat nebo Odstranit obsahuje klauzule Where původní hodnotu sledovacího sloupce (původní verze řádku).</span><span class="sxs-lookup"><span data-stu-id="dc2d9-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="dc2d9-176">Pokud byl řádek, který je aktualizován, změněn `rowversion` jiným uživatelem, hodnota ve sloupci se liší od původní hodnoty, takže příkaz Update nebo Delete nemůže najít řádek, který chcete aktualizovat z důvodu klauzule Where.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="dc2d9-177">Když entity Framework zjistí, že žádné řádky byly aktualizovány update nebo Delete příkaz (to znamená, když počet ovlivněných řádků je nula), interpretuje, že jako konflikt souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="dc2d9-178">Nakonfigurujte entity Framework tak, aby zahrnoval původní hodnoty každého sloupce v tabulce v commands Where update and Delete.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="dc2d9-179">Stejně jako v první možnosti, pokud se něco v řádku změnilo od prvního čtení řádku, klauzule Where nevrátí řádek k aktualizaci, který rozhraní Entity Framework interpretuje jako konflikt souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="dc2d9-180">Pro databázové tabulky, které mají mnoho sloupců, tento přístup může mít za následek velmi velké Where klauzule a může vyžadovat udržovat velké množství stavu.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="dc2d9-181">Jak již bylo uvedeno dříve, udržování velkého množství stavu může ovlivnit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="dc2d9-182">Proto tento přístup se obecně nedoporučuje a není metoda použitá v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="dc2d9-183">Pokud chcete implementovat tento přístup k souběžnosti, musíte označit všechny vlastnosti bez primárního klíče v entitě, pro kterou chcete sledovat souběžnost, přidáním atributu. `ConcurrencyCheck`</span><span class="sxs-lookup"><span data-stu-id="dc2d9-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="dc2d9-184">Tato změna umožňuje entity Framework zahrnout všechny sloupce v SQL Where klauzule Update a Delete příkazy.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="dc2d9-185">Ve zbývající části tohoto kurzu přidáte `rowversion` vlastnost sledování do entity oddělení, vytvoříte řadič a zobrazení a otestujete, zda vše funguje správně.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="dc2d9-186">Přidání vlastnosti sledování</span><span class="sxs-lookup"><span data-stu-id="dc2d9-186">Add a tracking property</span></span>

<span data-ttu-id="dc2d9-187">V *souboru Models/Department.cs*přidejte vlastnost sledování s názvem RowVersion:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="dc2d9-188">Atribut `Timestamp` určuje, že tento sloupec bude zahrnut do příkazu Where update and Delete odeslaných do databáze.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="dc2d9-189">Atribut je `Timestamp` volán, protože předchozí verze `timestamp` serveru SQL Server `rowversion` používaly datový typ SQL před tím, než jej sql nahradil.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="dc2d9-190">Typ .NET `rowversion` pro je bajtové pole.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="dc2d9-191">Pokud dáváte přednost použití fluent API, `IsConcurrencyToken` můžete použít metodu (v *Data/SchoolContext.cs)* k určení vlastnosti sledování, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="dc2d9-192">Přidáním vlastnosti jste změnili model databáze, takže je třeba provést další migraci.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="dc2d9-193">Uložte změny a vytvořte projekt a zadejte do příkazového okna následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="dc2d9-194">Vytvořit řadič a zobrazení oddělení</span><span class="sxs-lookup"><span data-stu-id="dc2d9-194">Create Departments controller and views</span></span>

<span data-ttu-id="dc2d9-195">Přilejme scaffold a departments controller a zobrazení jako dříve pro studenty, kurzy a instruktory.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Lešení oddělení](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="dc2d9-197">V *souboru DepartmentsController.cs* změňte všechny čtyři výskyty "FirstMidName" na "FullName" tak, aby rozevírací seznamy správce oddělení bude obsahovat celé jméno instruktora, nikoli pouze příjmení.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="dc2d9-198">Aktualizovat zobrazení indexu</span><span class="sxs-lookup"><span data-stu-id="dc2d9-198">Update Index view</span></span>

<span data-ttu-id="dc2d9-199">Modul zasazení vytvořil sloupec RowVersion v zobrazení Rejstříku, ale toto pole by se nemělo zobrazit.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="dc2d9-200">Nahraďte kód v *zobrazení/departments/Index.cshtml* následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="dc2d9-201">Tím se změní nadpis na "Oddělení", odstraní sloupec RowVersion a zobrazí celé jméno namísto křestního jména pro správce.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="dc2d9-202">Aktualizovat metody úprav</span><span class="sxs-lookup"><span data-stu-id="dc2d9-202">Update Edit methods</span></span>

<span data-ttu-id="dc2d9-203">V metodě `Edit` HttpGet `Details` i metodě přidejte `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="dc2d9-204">V Metodě `Edit` HttpGet přidejte dychtivé načítání pro správce.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="dc2d9-205">Nahraďte existující kód `Edit` metody HttpPost následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="dc2d9-206">Kód začíná pokusem o čtení oddělení, které má být aktualizováno.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="dc2d9-207">Pokud `FirstOrDefaultAsync` metoda vrátí hodnotu null, oddělení bylo odstraněno jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="dc2d9-208">V takovém případě kód používá zaúčtované hodnoty formuláře k vytvoření entity oddělení, aby bylo možné znovu zobrazit stránku Úpravy s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="dc2d9-209">Jako alternativu byste nemuseli znovu vytvořit entitu oddělení, pokud zobrazíte pouze chybovou zprávu bez opětovného zobrazení polí oddělení.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="dc2d9-210">Zobrazení ukládá původní `RowVersion` hodnotu ve skrytém poli a tato `rowVersion` metoda obdrží tuto hodnotu v parametru.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="dc2d9-211">Před voláním `SaveChanges`budete muset umístit `RowVersion` tuto původní `OriginalValues` hodnotu vlastnosti do kolekce pro entitu.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="dc2d9-212">Potom při entity Framework vytvoří příkaz SQL UPDATE, tento příkaz bude obsahovat WHERE `RowVersion` klauzule, která hledá řádek, který má původní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="dc2d9-213">Pokud žádné řádky jsou ovlivněny příkazem UPDATE `RowVersion` (žádné řádky mají původní `DbUpdateConcurrencyException` hodnotu), entity framework vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="dc2d9-214">Kód v bloku catch pro tuto výjimku získá entitu `Entries` ovlivněné oddělení, která má aktualizované hodnoty z vlastnosti na objektu výjimky.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="dc2d9-215">Kolekce `Entries` bude mít `EntityEntry` pouze jeden objekt.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="dc2d9-216">Tento objekt můžete použít k získání nových hodnot zadaných uživatelem a aktuálních hodnot databáze.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="dc2d9-217">Kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišné od toho, co uživatel zadal na stránce Upravit (pouze jedno pole je zde zobrazeno pro stručnost).</span><span class="sxs-lookup"><span data-stu-id="dc2d9-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="dc2d9-218">Nakonec kód nastaví `RowVersion` hodnotu `departmentToUpdate` na novou hodnotu načtenou z databáze.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="dc2d9-219">Tato `RowVersion` nová hodnota bude uložena ve skrytém poli při opětovném zobrazení stránky Úprava a při příštím klepnutí na tlačítko **Uložit**budou zachyceny pouze chyby souběžnosti, ke kterým dojde od opětovného zobrazení stránky Úpravy.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="dc2d9-220">Příkaz `ModelState.Remove` je povinný, `ModelState` protože `RowVersion` má starou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="dc2d9-221">V zobrazení má `ModelState` hodnota pole přednost před hodnotami vlastností modelu, pokud jsou k dispozici obě.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="dc2d9-222">Aktualizovat zobrazení pro úpravy</span><span class="sxs-lookup"><span data-stu-id="dc2d9-222">Update Edit view</span></span>

<span data-ttu-id="dc2d9-223">V *části Zobrazení/Oddělení/Edit.cshtml*proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="dc2d9-224">Přidejte skryté pole `RowVersion` pro uložení hodnoty vlastnosti bezprostředně `DepartmentID` za skrytým polem pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="dc2d9-225">Do rozevíracího seznamu přidejte možnost Vybrat správce.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="dc2d9-226">Testovat konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="dc2d9-226">Test concurrency conflicts</span></span>

<span data-ttu-id="dc2d9-227">Spusťte aplikaci a přejděte na stránku Index oddělení.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="dc2d9-228">Klikněte pravým tlačítkem myši na hypertextový odkaz **Upravit** pro anglické oddělení a **vyberte Otevřít na nové kartě**a potom klikněte na odkaz **Upravit** pro anglické oddělení.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="dc2d9-229">Dvě karty prohlížeče nyní zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="dc2d9-230">Změňte pole na první kartě prohlížeče a klepněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-230">Change a field in the first browser tab and click **Save**.</span></span>

![Oddělení Upravit stránku 1 po změně](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="dc2d9-232">Prohlížeč zobrazí stránku Rejstřík se změněnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="dc2d9-233">Změna pole na druhé kartě prohlížeče</span><span class="sxs-lookup"><span data-stu-id="dc2d9-233">Change a field in the second browser tab.</span></span>

![Oddělení Upravit stránku 2 po změně](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="dc2d9-235">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-235">Click **Save**.</span></span> <span data-ttu-id="dc2d9-236">Zobrazí se chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-236">You see an error message:</span></span>

![Chybová zpráva Upravit stránku oddělení](concurrency/_static/edit-error.png)

<span data-ttu-id="dc2d9-238">Klikněte znovu na **Uložit.**</span><span class="sxs-lookup"><span data-stu-id="dc2d9-238">Click **Save** again.</span></span> <span data-ttu-id="dc2d9-239">Hodnota zadaná na druhé kartě prohlížeče se uloží.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="dc2d9-240">Uložené hodnoty se zobrazí, když se zobrazí stránka Rejstřík.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="dc2d9-241">Aktualizace stránky Odstranit</span><span class="sxs-lookup"><span data-stu-id="dc2d9-241">Update the Delete page</span></span>

<span data-ttu-id="dc2d9-242">Pro Odstranit stránky entity framework zjistí konflikty souběžnosti způsobené někdo jiný úpravy oddělení podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="dc2d9-243">Když metoda `Delete` HttpGet zobrazí zobrazení potvrzení, zobrazení `RowVersion` obsahuje původní hodnotu ve skrytém poli.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="dc2d9-244">Tato hodnota je pak k `Delete` dispozici pro metodu HttpPost, která je volána, když uživatel potvrdí odstranění.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="dc2d9-245">Když entity Framework vytvoří příkaz SQL DELETE, obsahuje klauzuli WHERE s původní `RowVersion` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="dc2d9-246">Pokud příkaz má za následek nulové řádky ovlivněny (což znamená, že řádek byl změněn po odstranění potvrzení `Delete` stránka byla zobrazena), je vyvolána výjimka souběžnosti a httpGet metoda je volána s příznakem chyby nastavena na hodnotu true, aby se znovu zobrazit potvrzovací stránku s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="dc2d9-247">Je také možné, že byly ovlivněny nulové řádky, protože řádek byl odstraněn jiným uživatelem, takže v takovém případě se nezobrazí žádná chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="dc2d9-248">Aktualizace metod Delete v řadiči Departments</span><span class="sxs-lookup"><span data-stu-id="dc2d9-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="dc2d9-249">V *DepartmentsController.cs*nahraďte `Delete` metodu HttpGet následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="dc2d9-250">Metoda přijímá volitelný parametr, který označuje, zda je stránka znovu zobrazena po chybě souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="dc2d9-251">Pokud je tento příznak pravdivý a zadané oddělení již neexistuje, byl odstraněn jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="dc2d9-252">V takovém případě kód přesměruje na stránku Index.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="dc2d9-253">Pokud je tento příznak pravdivý a oddělení existuje, byl změněn jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="dc2d9-254">V takovém případě kód odešle chybovou `ViewData`zprávu zobrazení pomocí .</span><span class="sxs-lookup"><span data-stu-id="dc2d9-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="dc2d9-255">Nahraďte kód v `Delete` metodě `DeleteConfirmed`HttpPost (s názvem) následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="dc2d9-256">V kódu scaffolded, který jste právě nahradili, tato metoda přijala pouze ID záznamu:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="dc2d9-257">Tento parametr jste změnili na instanci entity oddělení vytvořenou pořadačmodelu.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="dc2d9-258">To poskytuje ef přístup k RowVersion hodnotu vlastnosti kromě klíče záznamu.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="dc2d9-259">Změnili jste také název `DeleteConfirmed` metody `Delete`akce z na .</span><span class="sxs-lookup"><span data-stu-id="dc2d9-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="dc2d9-260">Kód scaffolded používal `DeleteConfirmed` název k tomu, aby metoda HttpPost měla jedinečný podpis.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="dc2d9-261">(CLR vyžaduje přetížené metody mít různé parametry metody.) Nyní, když podpisy jsou jedinečné, můžete se držet konvence MVC a použít stejný název pro metody httppost a httpget odstranění.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="dc2d9-262">Pokud je oddělení již `AnyAsync` odstraněno, metoda vrátí false a aplikace se vrátí zpět k metodě Index.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="dc2d9-263">Pokud je zachycena chyba souběžnosti, kód znovu zobrazí stránku potvrzení odstranit a poskytuje příznak, který označuje, že by měl zobrazit chybovou zprávu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="dc2d9-264">Aktualizace zobrazení Odstranit</span><span class="sxs-lookup"><span data-stu-id="dc2d9-264">Update the Delete view</span></span>

<span data-ttu-id="dc2d9-265">V *zobrazení/departments/Delete.cshtml*nahraďte kód sklopný pomocí následujícího kódu, který přidá pole chybové zprávy a skrytá pole pro vlastnosti DepartmentID a RowVersion.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="dc2d9-266">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-266">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="dc2d9-267">Tím se změní následující změny:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-267">This makes the following changes:</span></span>

* <span data-ttu-id="dc2d9-268">Přidá chybovou zprávu `h2` `h3` mezi nadpisy a nadpisy.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="dc2d9-269">Nahradí firstmidname v poli **Správce** názvem FullName.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="dc2d9-270">Odebere pole RowVersion.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="dc2d9-271">Přidá skryté pole `RowVersion` pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="dc2d9-272">Spusťte aplikaci a přejděte na stránku Index oddělení.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="dc2d9-273">Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** pro anglické oddělení a **vyberte otevřít na nové kartě**a potom na první kartě klikněte na odkaz **Upravit** hypertextový odkaz pro anglické oddělení.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="dc2d9-274">V prvním okně změňte jednu z hodnot a klepněte na **Uložit**:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-274">In the first window, change one of the values, and click **Save**:</span></span>

![Stránka Úpravy oddělení po změně před odstraněním](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="dc2d9-276">Na druhé kartě klepněte na **tlačítko Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="dc2d9-277">Zobrazí se chybová zpráva souběžnosti a hodnoty oddělení jsou aktualizovány s tím, co je aktuálně v databázi.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Stránka potvrzení odstranění oddělení s chybou souběžnosti](concurrency/_static/delete-error.png)

<span data-ttu-id="dc2d9-279">Pokud znovu kliknete na **Odstranit,** budete přesměrováni na stránku Rejstřík, která ukazuje, že oddělení bylo odstraněno.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="dc2d9-280">Aktualizovat podrobnosti a vytvořit zobrazení</span><span class="sxs-lookup"><span data-stu-id="dc2d9-280">Update Details and Create views</span></span>

<span data-ttu-id="dc2d9-281">Volitelně můžete vyčistit kód scaffolded v podrobnosti a vytvořit zobrazení.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="dc2d9-282">Nahraďte kód v *zobrazení/oddělení/Details.cshtml* odstranit RowVersion sloupec a zobrazit úplný název správce.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="dc2d9-283">Nahraďte kód v *zobrazení/oddělení/Create.cshtml* a přidejte možnost Select do rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="dc2d9-284">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="dc2d9-284">Get the code</span></span>

[<span data-ttu-id="dc2d9-285">Stáhněte nebo zobrazte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-285">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="dc2d9-286">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="dc2d9-286">Additional resources</span></span>

 <span data-ttu-id="dc2d9-287">Další informace o tom, jak zpracovat souběžnost v ef core, naleznete v [tématu konflikty souběžnosti](/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="dc2d9-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="dc2d9-288">Další kroky</span><span class="sxs-lookup"><span data-stu-id="dc2d9-288">Next steps</span></span>

<span data-ttu-id="dc2d9-289">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="dc2d9-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dc2d9-290">Informace o konfliktech souběžnosti</span><span class="sxs-lookup"><span data-stu-id="dc2d9-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="dc2d9-291">Přidána vlastnost sledování</span><span class="sxs-lookup"><span data-stu-id="dc2d9-291">Added a tracking property</span></span>
> * <span data-ttu-id="dc2d9-292">Řadič a zobrazení vytvořených oddělení</span><span class="sxs-lookup"><span data-stu-id="dc2d9-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="dc2d9-293">Aktualizované zobrazení indexu</span><span class="sxs-lookup"><span data-stu-id="dc2d9-293">Updated Index view</span></span>
> * <span data-ttu-id="dc2d9-294">Aktualizované metody úprav</span><span class="sxs-lookup"><span data-stu-id="dc2d9-294">Updated Edit methods</span></span>
> * <span data-ttu-id="dc2d9-295">Aktualizované zobrazení úprav</span><span class="sxs-lookup"><span data-stu-id="dc2d9-295">Updated Edit view</span></span>
> * <span data-ttu-id="dc2d9-296">Testované konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="dc2d9-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="dc2d9-297">Stránka Odstranit byla aktualizována</span><span class="sxs-lookup"><span data-stu-id="dc2d9-297">Updated the Delete page</span></span>
> * <span data-ttu-id="dc2d9-298">Aktualizované podrobnosti a vytvoření zobrazení</span><span class="sxs-lookup"><span data-stu-id="dc2d9-298">Updated Details and Create views</span></span>

<span data-ttu-id="dc2d9-299">Přejdete k dalšímu kurzu, kde se dozvíte, jak implementovat dědičnost tabulky na hierarchii pro entity instruktora a studenta.</span><span class="sxs-lookup"><span data-stu-id="dc2d9-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="dc2d9-300">Další: Implementace dědičnosti tabulky na hierarchii</span><span class="sxs-lookup"><span data-stu-id="dc2d9-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
