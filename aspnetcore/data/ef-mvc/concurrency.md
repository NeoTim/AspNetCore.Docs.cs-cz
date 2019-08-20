---
title: 'Kurz: Zpracování souběžnosti – ASP.NET MVC pomocí EF Core'
description: Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
author: tdykstra
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: d4b83bd269b067ab6fa9e8666109bacc602589bd
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583250"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="5c3e5-103">Kurz: Zpracování souběžnosti – ASP.NET MVC pomocí EF Core</span><span class="sxs-lookup"><span data-stu-id="5c3e5-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="5c3e5-104">V předchozích kurzech jste zjistili, jak aktualizovat data.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="5c3e5-105">Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="5c3e5-106">Vytvoříte webové stránky, které budou fungovat s entitou oddělení a budou zpracovávat chyby souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="5c3e5-107">Následující ilustrace znázorňují stránky upravit a odstranit, včetně některých zpráv zobrazených v případě, že dojde ke konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Stránka pro úpravy oddělení](concurrency/_static/edit-error.png)

![Stránka pro odstranění oddělení](concurrency/_static/delete-error.png)

<span data-ttu-id="5c3e5-110">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5c3e5-111">Další informace o konfliktech souběžnosti</span><span class="sxs-lookup"><span data-stu-id="5c3e5-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="5c3e5-112">Přidat vlastnost sledování</span><span class="sxs-lookup"><span data-stu-id="5c3e5-112">Add a tracking property</span></span>
> * <span data-ttu-id="5c3e5-113">Vytvoření kontroleru a zobrazení oddělení</span><span class="sxs-lookup"><span data-stu-id="5c3e5-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="5c3e5-114">Aktualizovat zobrazení indexu</span><span class="sxs-lookup"><span data-stu-id="5c3e5-114">Update Index view</span></span>
> * <span data-ttu-id="5c3e5-115">Aktualizovat metody úprav</span><span class="sxs-lookup"><span data-stu-id="5c3e5-115">Update Edit methods</span></span>
> * <span data-ttu-id="5c3e5-116">Aktualizovat zobrazení pro úpravy</span><span class="sxs-lookup"><span data-stu-id="5c3e5-116">Update Edit view</span></span>
> * <span data-ttu-id="5c3e5-117">Konflikty testů v souběžnosti</span><span class="sxs-lookup"><span data-stu-id="5c3e5-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="5c3e5-118">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="5c3e5-118">Update the Delete page</span></span>
> * <span data-ttu-id="5c3e5-119">Aktualizovat podrobnosti a vytvořit zobrazení</span><span class="sxs-lookup"><span data-stu-id="5c3e5-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5c3e5-120">Požadavky</span><span class="sxs-lookup"><span data-stu-id="5c3e5-120">Prerequisites</span></span>

* [<span data-ttu-id="5c3e5-121">Aktualizace souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="5c3e5-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="5c3e5-122">Konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="5c3e5-122">Concurrency conflicts</span></span>

<span data-ttu-id="5c3e5-123">Ke konfliktu souběžnosti dojde, když jeden uživatel zobrazuje data entity, aby je mohl upravit, a poté jiný uživatel aktualizuje data stejné entity před zápisem prvního uživatele do databáze.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="5c3e5-124">Pokud nepovolíte detekci takových konfliktů, si aktualizace databáze poslední přepíše změny provedené ostatními uživateli.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="5c3e5-125">V mnoha aplikacích je toto riziko přijatelné: v případě, že existuje několik uživatelů nebo málo aktualizací, nebo pokud jsou nějaké změny přepsány, náklady na programování pro souběžnost můžou převážit výhody.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="5c3e5-126">V takovém případě nemusíte konfigurovat aplikaci pro zpracování konfliktů souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="5c3e5-127">Pesimistická souběžnost (uzamykání)</span><span class="sxs-lookup"><span data-stu-id="5c3e5-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="5c3e5-128">Pokud vaše aplikace potřebuje zabránit náhodné ztrátě dat ve scénářích souběžnosti, stačí jeden způsob, jak to provést, pomocí zámků databáze.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="5c3e5-129">Tato metoda se nazývá pesimistická souběžnost.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="5c3e5-130">Například před čtením řádku z databáze si vyžádáte zámek jen pro čtení nebo pro přístup k aktualizacím.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="5c3e5-131">Pokud zamknete řádek pro přístup k aktualizacím, žádní jiní uživatelé nemůžou Uzamknout řádek buď pro čtení, nebo pro přístup k aktualizacím, protože by získali kopii dat, která se v procesu mění.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="5c3e5-132">Pokud zamknete řádek pro přístup jen pro čtení, můžou ho jiní uživatelé taky uzamknout pro přístup jen pro čtení, ale ne pro aktualizace.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="5c3e5-133">Správa zámků má nevýhody.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="5c3e5-134">Může být komplexní pro program.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-134">It can be complex to program.</span></span> <span data-ttu-id="5c3e5-135">Vyžaduje významné prostředky správy databáze a může způsobit problémy s výkonem, protože se zvyšuje počet uživatelů aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="5c3e5-136">Z těchto důvodů ne všechny systémy správy databáze podporují pesimistickou souběžnost.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="5c3e5-137">Entity Framework Core pro něj neposkytuje žádnou integrovanou podporu a v tomto kurzu se vám nezobrazí, jak ho implementovat.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="5c3e5-138">Optimistická metoda souběžného zpracování</span><span class="sxs-lookup"><span data-stu-id="5c3e5-138">Optimistic Concurrency</span></span>

<span data-ttu-id="5c3e5-139">Alternativou k pesimistické souběžnosti je Optimistická souběžnost.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="5c3e5-140">Optimistická souběžnost znamená, že může dojít ke konfliktům souběžnosti a v případě, že je funguje správně.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="5c3e5-141">Jana například navštíví stránku pro úpravu oddělení a změní částku rozpočtu pro anglické oddělení z $350 000,00 na $0,00.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="5c3e5-143">Předtím, než Jan klikne **Uložit**, Jan navštíví na stejnou stránku a změny pole Datum zahájení 9/1/2013 z 9/1/2007.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Změna počátečního data a 2013](concurrency/_static/change-date.png)

<span data-ttu-id="5c3e5-145">Jana klikne na **Uložit** a po návratu prohlížeče na stránku indexu uvidí jeho změnu.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Změnit na hodnotu nula rozpočtu](concurrency/_static/budget-zero.png)

<span data-ttu-id="5c3e5-147">Potom Jan klikne na **Uložit** na stránce pro úpravy, která stále zobrazuje rozpočet $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="5c3e5-148">Co bude dál se určuje podle způsobu zpracování konfliktů souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="5c3e5-149">Mezi tyto možnosti patří:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-149">Some of the options include the following:</span></span>

* <span data-ttu-id="5c3e5-150">Můžete sledovat, kterou vlastnost uživatel změnil, a aktualizovat pouze odpovídající sloupce v databázi.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="5c3e5-151">V ukázkovém scénáři by se neztratila žádná data, protože dva uživatelé aktualizovali různé vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="5c3e5-152">Když někdo příště prochází v anglickém oddělení, uvidí změny Jana i Jan – počáteční datum 9/1/2013 a rozpočet s nulovými dolary.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="5c3e5-153">Tato metoda aktualizace může snížit počet konfliktů, které by mohly mít za následek ztrátu dat, ale nemůže se vyhnout ztrátě dat, pokud se ve stejné vlastnosti entity provedou konkurenční změny.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="5c3e5-154">To, zda Entity Framework funguje tímto způsobem závisí na způsobu implementace kódu aktualizace.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="5c3e5-155">V rámci webové aplikace to často není praktické, protože může vyžadovat, abyste zachovali velké množství stavu, aby bylo možné sledovat všechny původní hodnoty vlastností pro entitu a také nové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="5c3e5-156">Udržování velkých objemů stavu může ovlivnit výkon aplikace, protože buď vyžaduje prostředky serveru, nebo musí být součástí samotné webové stránky (například v skrytých polích) nebo v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="5c3e5-157">Můžete nechat John's na změnu Jana změna přepsána.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="5c3e5-158">Když někdo příště prochází v anglickém oddělení, uvidí 9/1/2013 a obnovenou hodnotu $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="5c3e5-159">To se označuje jako *klient WINS* nebo *Poslední ve scénáři WINS* .</span><span class="sxs-lookup"><span data-stu-id="5c3e5-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="5c3e5-160">(Všechny hodnoty z klienta přednost co je v úložišti.) Jak je uvedeno v části Úvod do této části, pokud neuděláte žádné kódování pro zpracování souběžnosti, k tomu dojde automaticky.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="5c3e5-161">Můžete zabránit tomu, aby se změnila aktualizace od Jan v databázi.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="5c3e5-162">Obvykle byste zobrazili chybovou zprávu, zobrazit její aktuální stav a umožnit jim, aby znovu použila své změny, pokud to ještě chce udělat.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="5c3e5-163">Tento postup se nazývá *Store Wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="5c3e5-164">(Hodnoty úložiště dat přednost hodnoty odeslány klientem.) V tomto kurzu implementujete scénář služby WINS pro Store.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="5c3e5-165">Tato metoda zajistí, že nedojde k přepsání žádné změny bez upozornění uživatele na to, co se děje.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="5c3e5-166">Zjišťování konfliktů souběžnosti</span><span class="sxs-lookup"><span data-stu-id="5c3e5-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="5c3e5-167">Konflikty můžete vyřešit zpracováním `DbConcurrencyException` výjimek, které Entity Framework vyvolá.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="5c3e5-168">Chcete-li zjistit, kdy vyvolat tyto výjimky, Entity Framework musí být schopna detekovat konflikty.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="5c3e5-169">Proto je nutné správně nakonfigurovat databázi a datový model.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="5c3e5-170">Mezi možnosti pro povolení detekce konfliktů patří následující:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="5c3e5-171">V tabulce databáze zahrňte sloupec sledování, který se dá použít k určení, kdy došlo ke změně řádku.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="5c3e5-172">Pak můžete nakonfigurovat Entity Framework pro zahrnutí tohoto sloupce do klauzule WHERE příkazů SQL Update nebo DELETE.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="5c3e5-173">Datový typ sloupce sledování je obvykle `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="5c3e5-174">`rowversion` Hodnota je sekvenční číslo, které se zvýší pokaždé, když se řádek aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="5c3e5-175">V příkazu Update nebo DELETE zahrnuje klauzule WHERE původní hodnotu sloupce sledování (původní verze řádku).</span><span class="sxs-lookup"><span data-stu-id="5c3e5-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="5c3e5-176">Pokud byl aktualizovaný řádek změněn jiným uživatelem, hodnota ve `rowversion` sloupci se liší od původní hodnoty, takže příkaz Update nebo DELETE nemůže najít řádek, který se má aktualizovat z klauzule WHERE.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="5c3e5-177">Pokud Entity Framework zjistí, že nebyly aktualizovány žádné řádky pomocí příkazu Update nebo Delete (tj. Pokud je počet ovlivněných řádků nula), je interpretována jako konflikt souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="5c3e5-178">Nakonfigurujte Entity Framework tak, aby zahrnoval původní hodnoty všech sloupců v tabulce v klauzuli WHERE příkazů Update a DELETE.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="5c3e5-179">Stejně jako v první možnosti, pokud se cokoli na řádku od prvního načtení řádku změnilo, klauzule WHERE nevrátí řádek, který se má aktualizovat, což Entity Framework interpretuje jako konflikt souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="5c3e5-180">U databázových tabulek, které mají mnoho sloupců, může tento přístup mít za následek velmi velké klauzule WHERE a může vyžadovat, abyste zachovali velké objemy stavů.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="5c3e5-181">Jak bylo uvedeno dříve, údržba velkých objemů stavu může ovlivnit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="5c3e5-182">Proto se tento přístup obecně nedoporučuje a nejedná se o metodu používanou v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="5c3e5-183">Pokud chcete tento přístup implementovat do souběžnosti, je nutné označit všechny vlastnosti neprimárního klíče v entitě, pro kterou chcete sledovat souběžnost, přidáním `ConcurrencyCheck` atributu do těchto.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="5c3e5-184">Tato změna umožňuje Entity Framework zahrnout všechny sloupce v klauzuli WHERE SQL příkazu Update a DELETE.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="5c3e5-185">Ve zbývající části tohoto kurzu přidáte `rowversion` k entitě oddělení vlastnost sledování, vytvoříte kontroler a zobrazení a otestujete, jestli vše funguje správně.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="5c3e5-186">Přidat vlastnost sledování</span><span class="sxs-lookup"><span data-stu-id="5c3e5-186">Add a tracking property</span></span>

<span data-ttu-id="5c3e5-187">V *Models/Department.cs*, přidání vlastnosti sledování do s názvem RowVersion:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="5c3e5-188">`Timestamp` Atribut určuje, zda bude tento sloupec zahrnut v klauzuli WHERE příkazů Update a DELETE odeslaných do databáze.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="5c3e5-189">Atribut je volán `Timestamp` , protože předchozí verze SQL Server používaly datový typ `timestamp` SQL předtím, než ho `rowversion` SQL nahradil.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="5c3e5-190">Typ .NET pro `rowversion` je pole bajtů.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="5c3e5-191">Pokud dáváte přednost používání rozhraní Fluent API, můžete použít `IsConcurrencyToken` metodu (v *data/SchoolContext. cs*) k určení vlastnosti sledování, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="5c3e5-192">Přidáním vlastnosti, kterou jste změnili databázový model, takže je nutné provést další migraci.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="5c3e5-193">Uložte změny a sestavte projekt a pak v příkazovém okně zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```console
dotnet ef migrations add RowVersion
```

```console
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="5c3e5-194">Vytvoření kontroleru a zobrazení oddělení</span><span class="sxs-lookup"><span data-stu-id="5c3e5-194">Create Departments controller and views</span></span>

<span data-ttu-id="5c3e5-195">Generování uživatelského rozhraní a zobrazení jako dříve pro studenty, kurzy a instruktory.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Oddělení uživatelského rozhraní](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="5c3e5-197">V souboru *DepartmentsController.cs* změňte všechny čtyři výskyty řetězce "FirstMidName" na "FullName", aby rozevírací seznamy správce oddělení obsahovaly celé jméno instruktora, nikoli jenom příjmení.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="5c3e5-198">Aktualizovat zobrazení indexu</span><span class="sxs-lookup"><span data-stu-id="5c3e5-198">Update Index view</span></span>

<span data-ttu-id="5c3e5-199">Modul generování uživatelského rozhraní vytvořil v zobrazení indexu sloupec RowVersion, ale toto pole by se nemělo zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="5c3e5-200">Nahraďte kód v *zobrazeních/departments/index. cshtml* následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="5c3e5-201">Tím se změní nadpis na "oddělení", odstraní se sloupec RowVersion a pro správce se zobrazí jméno a příjmení místo křestní jméno.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="5c3e5-202">Aktualizovat metody úprav</span><span class="sxs-lookup"><span data-stu-id="5c3e5-202">Update Edit methods</span></span>

<span data-ttu-id="5c3e5-203">V metodě HttpGet `Edit` `Details` a metodě přidejte `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="5c3e5-204">V metodě HttpGet `Edit` přidejte Eager načítání pro správce.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="5c3e5-205">Nahraďte existující kód pro metodu HTTPPOST `Edit` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="5c3e5-206">Kód začíná tím, že se pokusí přečíst oddělení, které se má aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="5c3e5-207">`FirstOrDefaultAsync` Pokud metoda vrátí hodnotu null, oddělení bylo odstraněno jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="5c3e5-208">V takovém případě kód používá hodnoty vystaveného formuláře k vytvoření entity oddělení, aby se stránka pro úpravy mohla znovu zobrazit s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="5c3e5-209">Jako alternativu nebudete muset entitu oddělení znovu vytvořit, pokud se zobrazí pouze chybová zpráva bez zobrazení polí oddělení.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="5c3e5-210">Zobrazení ukládá původní `RowVersion` hodnotu do skrytého pole a tato metoda obdrží tuto hodnotu `rowVersion` v parametru.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="5c3e5-211">Před voláním `SaveChanges`je nutné umístit tuto původní `RowVersion` hodnotu vlastnosti do `OriginalValues` kolekce pro entitu.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="5c3e5-212">Pak když Entity Framework vytvoří příkaz SQL Update, bude tento příkaz obsahovat klauzuli WHERE, která vyhledá řádek, který má původní `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="5c3e5-213">Pokud nejsou žádné řádky ovlivněny příkazem Update (žádné řádky nemají původní `RowVersion` hodnotu), Entity Framework `DbUpdateConcurrencyException` vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="5c3e5-214">Kód v bloku catch pro tuto výjimku získá entitu ovlivněného oddělení, která má aktualizované hodnoty z `Entries` vlastnosti objektu Exception.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="5c3e5-215">Kolekce bude obsahovat pouze jeden `EntityEntry` objekt. `Entries`</span><span class="sxs-lookup"><span data-stu-id="5c3e5-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="5c3e5-216">Tento objekt lze použít k získání nových hodnot zadaných uživatelem a aktuálními hodnotami databáze.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="5c3e5-217">Kód přidá vlastní chybovou zprávu pro každý sloupec, který má jiné hodnoty databáze než uživatel zadaný na stránce pro úpravy (tady se zobrazí pouze jedno pole pro zkrácení).</span><span class="sxs-lookup"><span data-stu-id="5c3e5-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="5c3e5-218">Nakonec kód nastaví `RowVersion` hodnotu `departmentToUpdate` na novou hodnotu načtenou z databáze.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="5c3e5-219">Tato nová `RowVersion` hodnota se uloží do skrytého pole, když se znovu zobrazí stránka pro úpravy, a když uživatel příště klikne na **Uložit**, zachytí se jenom chyby souběžnosti, ke kterým dojde od zobrazení stránky pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="5c3e5-220">`ModelState.Remove` Příkazu se totiž `ModelState` má starý `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="5c3e5-221">V zobrazení `ModelState` má hodnota pro pole přednost před hodnotami vlastností modelu, pokud jsou oba přítomny.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="5c3e5-222">Aktualizovat zobrazení pro úpravy</span><span class="sxs-lookup"><span data-stu-id="5c3e5-222">Update Edit view</span></span>

<span data-ttu-id="5c3e5-223">V *zobrazení/oddělení/upravit. cshtml*proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="5c3e5-224">Přidejte skryté pole pro uložení `RowVersion` hodnoty vlastnosti hned za skryté pole `DepartmentID` pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="5c3e5-225">Přidejte v rozevíracím seznamu možnost vybrat správce.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="5c3e5-226">Konflikty testů v souběžnosti</span><span class="sxs-lookup"><span data-stu-id="5c3e5-226">Test concurrency conflicts</span></span>

<span data-ttu-id="5c3e5-227">Spusťte aplikaci a pokračujte na stránku s indexem oddělení.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="5c3e5-228">Klikněte pravým tlačítkem myši na hypertextový odkaz pro jazykové oddělení a vyberte **otevřít na nové kartě**a pak klikněte na hypertextový odkaz pro **Úpravy** v anglickém oddělení.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="5c3e5-229">Dvě karty prohlížeče nyní zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="5c3e5-230">Změňte pole na první kartě prohlížeče a klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-230">Change a field in the first browser tab and click **Save**.</span></span>

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="5c3e5-232">Prohlížeč zobrazí stránku indexu se změněnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="5c3e5-233">Změňte pole na druhé kartě prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-233">Change a field in the second browser tab.</span></span>

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="5c3e5-235">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-235">Click **Save**.</span></span> <span data-ttu-id="5c3e5-236">Zobrazí se chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-236">You see an error message:</span></span>

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error.png)

<span data-ttu-id="5c3e5-238">Klikněte na tlačítko **Uložit** znovu.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-238">Click **Save** again.</span></span> <span data-ttu-id="5c3e5-239">Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="5c3e5-240">Po zobrazení stránky index se zobrazí uložené hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="5c3e5-241">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="5c3e5-241">Update the Delete page</span></span>

<span data-ttu-id="5c3e5-242">Pro stránku odstranění Entity Framework detekuje konflikty souběžnosti způsobené někým jiným upravováním oddělení podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="5c3e5-243">Když metoda HttpGet `Delete` zobrazí zobrazení potvrzení, zobrazení obsahuje původní `RowVersion` hodnotu ve skrytém poli.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="5c3e5-244">Tato hodnota je pak k dispozici metodě `Delete` HTTPPOST, která je volána, když uživatel potvrdí odstranění.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="5c3e5-245">Když Entity Framework vytvoří příkaz SQL DELETE, zahrnuje klauzuli WHERE s původní `RowVersion` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="5c3e5-246">Pokud tento příkaz má vliv na nulové řádky (což znamená, že řádek byl změněn po zobrazení stránky pro potvrzení odstranění), je vyvolána výjimka souběžnosti a metoda HttpGet `Delete` je volána s příznakem chyby nastaveným na hodnotu true, aby bylo možné znovu zobrazit potvrzovací stránka s chybovou zprávou</span><span class="sxs-lookup"><span data-stu-id="5c3e5-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="5c3e5-247">Je také možné, že byly ovlivněny nulové řádky, protože řádek byl odstraněn jiným uživatelem, takže v takovém případě se nezobrazí žádná chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="5c3e5-248">Aktualizace metod Delete v řadiči oddělení</span><span class="sxs-lookup"><span data-stu-id="5c3e5-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="5c3e5-249">V *DepartmentsController.cs*nahraďte metodu HttpGet `Delete` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="5c3e5-250">Metoda přijímá volitelný parametr, který označuje, zda se stránka po chybě souběžného zpracování znovu zobrazuje.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="5c3e5-251">Pokud má tento příznak hodnotu true a zadané oddělení už neexistuje, odstranil jiný uživatel.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="5c3e5-252">V takovém případě kód přesměruje na stránku indexu.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="5c3e5-253">Pokud má tento příznak hodnotu true a oddělení existuje, změnil ho jiný uživatel.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="5c3e5-254">V takovém případě kód pošle do zobrazení chybovou zprávu pomocí `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="5c3e5-255">Nahraďte kód v metodě HTTPPOST `Delete` (s názvem `DeleteConfirmed`) následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="5c3e5-256">V právě vygenerovaném kódu jste tuto metodu přijali pouze s ID záznamu:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="5c3e5-257">Změnili jste tento parametr na instanci entity oddělení vytvořenou pomocí pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="5c3e5-258">Díky tomu má EF přístup k hodnotě vlastnosti RowVersion kromě klíče záznamu.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="5c3e5-259">Také jste změnili název metody akce z `DeleteConfirmed` na. `Delete`</span><span class="sxs-lookup"><span data-stu-id="5c3e5-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="5c3e5-260">Generovaný kód použil název `DeleteConfirmed` , který metodě HTTPPOST poskytne jedinečný podpis.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="5c3e5-261">(CLR vyžaduje, aby byly přetížené metody pro různé parametry metody.) Teď, když jsou podpisy jedinečné, můžete s úmluvou MVC pracovat a používat stejný název pro metody Delete HttpPost a HttpGet.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="5c3e5-262">Pokud je oddělení již odstraněno, `AnyAsync` vrátí metoda hodnotu false a aplikace se vrátí zpět do metody indexu.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="5c3e5-263">Pokud je zachycena chyba souběžnosti, kód znovu zobrazí stránku pro potvrzení odstranění a poskytne příznak označující, že by měla zobrazit chybovou zprávu o souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="5c3e5-264">Aktualizace zobrazení pro odstranění</span><span class="sxs-lookup"><span data-stu-id="5c3e5-264">Update the Delete view</span></span>

<span data-ttu-id="5c3e5-265">V *zobrazení/oddělení/odstranění. cshtml*nahraďte kód generovaný následujícím kódem, který přidá pole chybové zprávy a skrytá pole pro vlastnosti DepartmentID a rowversion.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="5c3e5-266">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-266">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="5c3e5-267">Provede tyto změny:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-267">This makes the following changes:</span></span>

* <span data-ttu-id="5c3e5-268">Přidá chybovou zprávu mezi `h2` nadpisy a. `h3`</span><span class="sxs-lookup"><span data-stu-id="5c3e5-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="5c3e5-269">Nahradí celý název v FirstMidName **správce** pole.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="5c3e5-270">Odebere pole RowVersion.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="5c3e5-271">Přidá skryté pole pro `RowVersion` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="5c3e5-272">Spusťte aplikaci a pokračujte na stránku s indexem oddělení.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="5c3e5-273">Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** pro anglické oddělení a vyberte **otevřít na nové kartě**a pak na první kartě klikněte na odkaz **Upravit** pro anglické oddělení.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="5c3e5-274">V prvním okně změňte jednu z hodnot a klikněte na **Uložit**:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-274">In the first window, change one of the values, and click **Save**:</span></span>

![Stránka pro úpravu oddělení po změně před odstraněním](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="5c3e5-276">Na druhé kartě klikněte na **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="5c3e5-277">Zobrazí se chybová zpráva o souběžnosti a hodnoty oddělení se aktualizují s tím, co je aktuálně v databázi.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Stránka pro potvrzení odstranění oddělení s chybou souběžnosti](concurrency/_static/delete-error.png)

<span data-ttu-id="5c3e5-279">Pokud znovu kliknete na tlačítko **Odstranit** , budete přesměrováni na stránku index, která ukazuje, že oddělení bylo odstraněno.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="5c3e5-280">Aktualizovat podrobnosti a vytvořit zobrazení</span><span class="sxs-lookup"><span data-stu-id="5c3e5-280">Update Details and Create views</span></span>

<span data-ttu-id="5c3e5-281">Volitelně můžete vyčistit kód vygenerovaný v podrobnostech a vytvořit zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="5c3e5-282">Nahraďte kód v *zobrazeních/departments/details. cshtml* pro odstranění sloupce rowversion a zobrazením úplného jména správce.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="5c3e5-283">Chcete-li přidat možnost výběru do rozevíracího seznamu, nahraďte kód v *zobrazeních/departments/Create. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="5c3e5-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="5c3e5-284">Získat kód</span><span class="sxs-lookup"><span data-stu-id="5c3e5-284">Get the code</span></span>

[<span data-ttu-id="5c3e5-285">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-285">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="5c3e5-286">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5c3e5-286">Additional resources</span></span>

 <span data-ttu-id="5c3e5-287">Další informace o zpracování souběžnosti v EF Core naleznete v tématu [konflikty souběžnosti](/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="5c3e5-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="5c3e5-288">Další postup</span><span class="sxs-lookup"><span data-stu-id="5c3e5-288">Next steps</span></span>

<span data-ttu-id="5c3e5-289">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="5c3e5-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5c3e5-290">Dozvědělo se o konfliktech souběžnosti</span><span class="sxs-lookup"><span data-stu-id="5c3e5-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="5c3e5-291">Přidala se vlastnost sledování.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-291">Added a tracking property</span></span>
> * <span data-ttu-id="5c3e5-292">Vytvořen kontroler a zobrazení oddělení</span><span class="sxs-lookup"><span data-stu-id="5c3e5-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="5c3e5-293">Aktualizované zobrazení indexu</span><span class="sxs-lookup"><span data-stu-id="5c3e5-293">Updated Index view</span></span>
> * <span data-ttu-id="5c3e5-294">Aktualizované metody úprav</span><span class="sxs-lookup"><span data-stu-id="5c3e5-294">Updated Edit methods</span></span>
> * <span data-ttu-id="5c3e5-295">Aktualizace zobrazení pro úpravy</span><span class="sxs-lookup"><span data-stu-id="5c3e5-295">Updated Edit view</span></span>
> * <span data-ttu-id="5c3e5-296">Testované konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="5c3e5-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="5c3e5-297">Aktualizace stránky odstranění</span><span class="sxs-lookup"><span data-stu-id="5c3e5-297">Updated the Delete page</span></span>
> * <span data-ttu-id="5c3e5-298">Aktualizované podrobnosti a vytváření zobrazení</span><span class="sxs-lookup"><span data-stu-id="5c3e5-298">Updated Details and Create views</span></span>

<span data-ttu-id="5c3e5-299">Přejděte k dalšímu kurzu, kde se dozvíte, jak implementovat dědičnost tabulek na hierarchii pro entity instruktor a student.</span><span class="sxs-lookup"><span data-stu-id="5c3e5-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5c3e5-300">Generace Implementace dědičnosti tabulek na hierarchii</span><span class="sxs-lookup"><span data-stu-id="5c3e5-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
