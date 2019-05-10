---
title: 'Kurz: Zpracování souběžnosti – ASP.NET MVC s EF Core'
description: Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: d3954800f4f1358565a627768e34465215dc4f6e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900510"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="4a532-103">Kurz: Zpracování souběžnosti – ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="4a532-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="4a532-104">V předchozích kurzech jste zjistili, jak aktualizovat data.</span><span class="sxs-lookup"><span data-stu-id="4a532-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="4a532-105">Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="4a532-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="4a532-106">Vytvoříte webové stránky, které pracují s entitou oddělení a zpracování chyb, které souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="4a532-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="4a532-107">Upravit a odstranit stránky, včetně některé zprávy, které se zobrazí, pokud dojde ke konfliktu souběžnosti na následujících obrázcích.</span><span class="sxs-lookup"><span data-stu-id="4a532-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Stránky pro úpravu oddělení](concurrency/_static/edit-error.png)

![Odstranění stránky oddělení](concurrency/_static/delete-error.png)

<span data-ttu-id="4a532-110">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="4a532-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4a532-111">Další informace o konfliktů souběžnosti</span><span class="sxs-lookup"><span data-stu-id="4a532-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="4a532-112">Přidání vlastnosti sledování do</span><span class="sxs-lookup"><span data-stu-id="4a532-112">Add a tracking property</span></span>
> * <span data-ttu-id="4a532-113">Vytvoření kontroleru oddělení a zobrazení</span><span class="sxs-lookup"><span data-stu-id="4a532-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="4a532-114">Aktualizace zobrazení Index</span><span class="sxs-lookup"><span data-stu-id="4a532-114">Update Index view</span></span>
> * <span data-ttu-id="4a532-115">Aktualizace metod Edit</span><span class="sxs-lookup"><span data-stu-id="4a532-115">Update Edit methods</span></span>
> * <span data-ttu-id="4a532-116">Aktualizovat zobrazení pro úpravy</span><span class="sxs-lookup"><span data-stu-id="4a532-116">Update Edit view</span></span>
> * <span data-ttu-id="4a532-117">Testování konfliktů souběžnosti</span><span class="sxs-lookup"><span data-stu-id="4a532-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="4a532-118">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="4a532-118">Update the Delete page</span></span>
> * <span data-ttu-id="4a532-119">Aktualizovat podrobnosti a vytvořit zobrazení</span><span class="sxs-lookup"><span data-stu-id="4a532-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4a532-120">Požadavky</span><span class="sxs-lookup"><span data-stu-id="4a532-120">Prerequisites</span></span>

* [<span data-ttu-id="4a532-121">Aktualizace souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="4a532-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="4a532-122">Konflikty souběžnosti</span><span class="sxs-lookup"><span data-stu-id="4a532-122">Concurrency conflicts</span></span>

<span data-ttu-id="4a532-123">Ke konfliktu souběžnosti dochází, když jeden uživatel zobrazuje entity data abyste ji mohli editovat a aktualizuje stejné entity data jiným uživatelem než změnu první uživatele je zapsána do databáze.</span><span class="sxs-lookup"><span data-stu-id="4a532-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="4a532-124">Pokud nepovolíte detekce takové konflikty je, kdo aktualizuje databázi poslední přepíše změny dalších uživatelů.</span><span class="sxs-lookup"><span data-stu-id="4a532-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="4a532-125">V mnoha aplikacích je přijatelné toto riziko: Pokud existuje několik uživatelů nebo několik aktualizací, nebo pokud není skutečně důležité, pokud některé změny budou přepsány, výhody vyváží nižší náklady na programování pro souběžnost.</span><span class="sxs-lookup"><span data-stu-id="4a532-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="4a532-126">V takovém případě není nutné nakonfigurovat aplikaci pro zpracování konfliktů souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="4a532-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="4a532-127">Pesimistická souběžnost (uzamčení)</span><span class="sxs-lookup"><span data-stu-id="4a532-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="4a532-128">Pokud vaše aplikace potřebuje se tak ztrátě dat ve scénářích souběžnosti, je to udělat jedním ze způsobů použití uzamčení databáze.</span><span class="sxs-lookup"><span data-stu-id="4a532-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="4a532-129">Tomu se říká Pesimistická souběžnost.</span><span class="sxs-lookup"><span data-stu-id="4a532-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="4a532-130">Například předtím, než se pustíte do čtení řádku z databáze, můžete požádat o zámek pro jen pro čtení nebo pro přístup k aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="4a532-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="4a532-131">Pokud řádek pro aktualizaci přístup, žádné uživatelé můžou zamknout řádku, buď pro jen pro čtení nebo aktualizaci přístup, protože by dostanou kopii dat, která se právě mění.</span><span class="sxs-lookup"><span data-stu-id="4a532-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="4a532-132">Pokud řádek pro přístup jen pro čtení, ostatní také zařízení Uzamknout pro přístup jen pro čtení, ale ne pro aktualizace.</span><span class="sxs-lookup"><span data-stu-id="4a532-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="4a532-133">Zámky pro správu má nevýhody.</span><span class="sxs-lookup"><span data-stu-id="4a532-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="4a532-134">Může být složité do programu.</span><span class="sxs-lookup"><span data-stu-id="4a532-134">It can be complex to program.</span></span> <span data-ttu-id="4a532-135">Vyžaduje významné databáze správy zdrojů, a to může způsobit problémy s výkonem jako počet uživatelů aplikace zvyšuje.</span><span class="sxs-lookup"><span data-stu-id="4a532-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="4a532-136">Z těchto důvodů ne všechny systémy správy databáze nepodporují Pesimistická souběžnost.</span><span class="sxs-lookup"><span data-stu-id="4a532-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="4a532-137">Entity Framework Core obsahuje předdefinovanou podporu pro ni a v tomto kurzu nezobrazí způsobu jeho implementace.</span><span class="sxs-lookup"><span data-stu-id="4a532-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="4a532-138">Optimistická metoda souběžného zpracování</span><span class="sxs-lookup"><span data-stu-id="4a532-138">Optimistic Concurrency</span></span>

<span data-ttu-id="4a532-139">Alternativa k Pesimistická souběžnost je Optimistická souběžnost.</span><span class="sxs-lookup"><span data-stu-id="4a532-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="4a532-140">Povolení konfliktů souběžnosti, která se provede a reaguje správně, pokud tomu znamená, že optimistického řízení souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="4a532-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="4a532-141">Například Jana navštíví stránku Upravit oddělení a změní hodnotu rozpočtu pro anglickou oddělení z $350,000.00 na 0.00 $.</span><span class="sxs-lookup"><span data-stu-id="4a532-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="4a532-143">Předtím, než Jan klikne **Uložit**, Jan navštíví na stejnou stránku a změny pole Datum zahájení 9/1/2013 z 9/1/2007.</span><span class="sxs-lookup"><span data-stu-id="4a532-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Změna počátečního data a 2013](concurrency/_static/change-date.png)

<span data-ttu-id="4a532-145">Jan klikne **Uložit** první a jí změnit návratu na indexovou stránku v prohlížeči se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="4a532-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Změnit na hodnotu nula rozpočtu](concurrency/_static/budget-zero.png)

<span data-ttu-id="4a532-147">Pak Jan klikne **Uložit** na stránce Upravit, která stále zobrazuje rozpočtu 350,000.00 $.</span><span class="sxs-lookup"><span data-stu-id="4a532-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="4a532-148">Co bude dál se určuje podle způsobu zpracování konfliktů souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="4a532-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="4a532-149">Mezi možnosti patří následující:</span><span class="sxs-lookup"><span data-stu-id="4a532-149">Some of the options include the following:</span></span>

* <span data-ttu-id="4a532-150">Můžete sledovat, které vlastnosti uživatele byl změněn a aktualizovat pouze odpovídající sloupce v databázi.</span><span class="sxs-lookup"><span data-stu-id="4a532-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="4a532-151">V ukázkovém scénáři žádné by dojít ke ztrátě dat., protože různé vlastnosti byly aktualizovány dva uživatelé.</span><span class="sxs-lookup"><span data-stu-id="4a532-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="4a532-152">Při příštím někdo přejde z anglické oddělení, zobrazí se Jana a John's na změny – datum zahájení o 9/1/2013 a rozpočet nulové dolarů.</span><span class="sxs-lookup"><span data-stu-id="4a532-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="4a532-153">Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě, ale nemůže zamezení ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost entity.</span><span class="sxs-lookup"><span data-stu-id="4a532-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="4a532-154">Ať už rozhraní Entity Framework funguje tímto způsobem závisí na implementace aktualizace kódu.</span><span class="sxs-lookup"><span data-stu-id="4a532-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="4a532-155">Není často praktické ve webové aplikaci, protože to může vyžadovat spravovat velké množství stavu aby bylo možné udržovat přehled o všech původní hodnoty vlastností pro entitu a nové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4a532-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="4a532-156">Správa velkého objemu stavu může ovlivnit výkon aplikace, protože ji vyžaduje prostředky serveru nebo musí být součástí webové stránky (například v skrytá pole) nebo do souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="4a532-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="4a532-157">Můžete nechat John's na změnu Jana změna přepsána.</span><span class="sxs-lookup"><span data-stu-id="4a532-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="4a532-158">Při příštím někdo přejde z anglické oddělení, zobrazí se 9/1/2013 a obnovený $350,000.00 hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4a532-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="4a532-159">Tento postup se nazývá *Wins, klient* nebo *poslední ve službě Wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="4a532-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="4a532-160">(Všechny hodnoty z klienta přednost co je v úložišti.) Jak jsme uvedli v úvodu do této části, pokud tak učiníte vytvářet kód pro zpracování souběžnosti, k tomu dochází automaticky.</span><span class="sxs-lookup"><span data-stu-id="4a532-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="4a532-161">John's na změnu může zabránit aktualizují v databázi.</span><span class="sxs-lookup"><span data-stu-id="4a532-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="4a532-162">By obvykle zobrazí chybovou zprávu, zobrazí jeho aktuální stav dat a mu umožní se jeho změny znovu použijte, pokud chce je.</span><span class="sxs-lookup"><span data-stu-id="4a532-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="4a532-163">Tento postup se nazývá *Store Wins* scénář.</span><span class="sxs-lookup"><span data-stu-id="4a532-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="4a532-164">(Hodnoty úložiště dat přednost hodnoty odeslány klientem.) V tomto kurzu budete implementovat scénář Store Wins.</span><span class="sxs-lookup"><span data-stu-id="4a532-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="4a532-165">Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění na co se děje.</span><span class="sxs-lookup"><span data-stu-id="4a532-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="4a532-166">Zjišťování konfliktů souběžnosti</span><span class="sxs-lookup"><span data-stu-id="4a532-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="4a532-167">Konflikty lze vyřešit zpracování `DbConcurrencyException` výjimky, které vyvolá rozhraní Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="4a532-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="4a532-168">Pokud chcete zjistit, kdy se má vyvolat tyto výjimky, musí být schopen rozpoznat konflikty Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="4a532-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="4a532-169">Proto je nutné nakonfigurovat databázi a datový model odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="4a532-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="4a532-170">Některé možnosti aktivace zjišťování konfliktů, patří:</span><span class="sxs-lookup"><span data-stu-id="4a532-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="4a532-171">V tabulce databáze zahrnují sledování sloupec, který slouží k určení, kdy změnil řádek.</span><span class="sxs-lookup"><span data-stu-id="4a532-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="4a532-172">Potom můžete nakonfigurovat rozhraní Entity Framework zahrnout sloupce Where – klauzule SQL aktualizace a odstranění příkazů.</span><span class="sxs-lookup"><span data-stu-id="4a532-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="4a532-173">Datový typ sloupce pro sledování je obvykle `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="4a532-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="4a532-174">`rowversion` Hodnotu pořadové číslo, které se zvýší při každé aktualizaci řádku.</span><span class="sxs-lookup"><span data-stu-id="4a532-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="4a532-175">V příkazu Update nebo Delete Where – klauzule obsahuje původní hodnota sloupce pro sledování (původní verze řádku).</span><span class="sxs-lookup"><span data-stu-id="4a532-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="4a532-176">Pokud se změnila řádek aktualizován jiným uživatelem, hodnota v `rowversion` sloupce se liší od původní hodnotu, aby příkazu Update nebo Delete nelze najít řádek aktualizovat z důvodu Where – klauzule.</span><span class="sxs-lookup"><span data-stu-id="4a532-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="4a532-177">Když najde Entity Framework, že byly aktualizovány žádné řádky podle Update nebo Delete příkazu (to znamená, když počet ovlivněných řádků je nula), interpretuje, který jako ke konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="4a532-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="4a532-178">Konfigurace rozhraní Entity Framework pro zahrnutí původní hodnota každý sloupec v tabulce v Where klauzule příkazy Update a Delete.</span><span class="sxs-lookup"><span data-stu-id="4a532-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="4a532-179">Jako první možnost, pokud něco v řádku změnilo od řádku se nejdřív přečíst Where – klauzule nevrátí řádek k aktualizaci, která nastavení interpretuje Entity Framework jako ke konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="4a532-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="4a532-180">Pro databázové tabulky, které mají mnoho sloupců, tento přístup může vést k velmi velké Where klauzule a můžete požadovat, že udržujete velké množství stavu.</span><span class="sxs-lookup"><span data-stu-id="4a532-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="4a532-181">Jak bylo uvedeno dříve, udržování velké množství stavu může ovlivnit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a532-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="4a532-182">Proto tento postup se obecně nedoporučuje, které není metoda použitá v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="4a532-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="4a532-183">Pokud chcete k implementaci tohoto přístupu se souběžností, budete muset označit všechny vlastnosti primárního klíče v entitě, kterou chcete sledovat souběžnosti pro tak, že přidáte `ConcurrencyCheck` atributu na ně.</span><span class="sxs-lookup"><span data-stu-id="4a532-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="4a532-184">Tato změna umožňuje rozhraní Entity Framework zahrňte všechny sloupce v klauzuli Where příkazu SQL příkazy Update a Delete.</span><span class="sxs-lookup"><span data-stu-id="4a532-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="4a532-185">Ve zbývající části tohoto kurzu přidáte `rowversion` vlastnosti sledování do entity oddělení, vytvořit kontroler a zobrazení a otestovat a ověřit, že vše funguje správně.</span><span class="sxs-lookup"><span data-stu-id="4a532-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="4a532-186">Přidání vlastnosti sledování do</span><span class="sxs-lookup"><span data-stu-id="4a532-186">Add a tracking property</span></span>

<span data-ttu-id="4a532-187">V *Models/Department.cs*, přidání vlastnosti sledování do s názvem RowVersion:</span><span class="sxs-lookup"><span data-stu-id="4a532-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="4a532-188">`Timestamp` Atribut určuje, zda tento sloupec součástí Where klauzule příkazy Update a Delete odešlou do databáze.</span><span class="sxs-lookup"><span data-stu-id="4a532-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="4a532-189">Atribut se nazývá `Timestamp` protože předchozích verzí SQL serveru použít SQL `timestamp` datového typu než SQL `rowversion` nahradili jsme ho.</span><span class="sxs-lookup"><span data-stu-id="4a532-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="4a532-190">Typ formátu .NET pro `rowversion` bajtové pole.</span><span class="sxs-lookup"><span data-stu-id="4a532-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="4a532-191">Pokud chcete použít rozhraní fluent API, můžete použít `IsConcurrencyToken` – metoda (v *Data/SchoolContext.cs*) pro určení této vlastnosti sledování, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4a532-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="4a532-192">Přidáním vlastnosti změnit model databáze, takže je třeba provést další migraci.</span><span class="sxs-lookup"><span data-stu-id="4a532-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="4a532-193">Uložte změny a sestavte projekt a potom zadejte následující příkazy v příkazovém okně:</span><span class="sxs-lookup"><span data-stu-id="4a532-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```console
dotnet ef migrations add RowVersion
```

```console
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="4a532-194">Vytvoření kontroleru oddělení a zobrazení</span><span class="sxs-lookup"><span data-stu-id="4a532-194">Create Departments controller and views</span></span>

<span data-ttu-id="4a532-195">Stejně jako dříve pro studenty, kurzy a vyučující, generování uživatelského rozhraní oddělení kontroler a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="4a532-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Oddělení vygenerované uživatelské rozhraní](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="4a532-197">V *DepartmentsController.cs* souborů, změňte všechny čtyři výskyty "FirstMidName" na "FullName" tak, aby oddělení správce rozevírací seznamy bude obsahovat úplný název instruktorem, nikoli pouze poslední název.</span><span class="sxs-lookup"><span data-stu-id="4a532-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="4a532-198">Aktualizace zobrazení Index</span><span class="sxs-lookup"><span data-stu-id="4a532-198">Update Index view</span></span>

<span data-ttu-id="4a532-199">Generování uživatelského rozhraní stroj vytvořil RowVersion sloupec v indexu zobrazení, ale nebude se zobrazovat toto pole.</span><span class="sxs-lookup"><span data-stu-id="4a532-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="4a532-200">Nahraďte kód v *Views/Departments/Index.cshtml* následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="4a532-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="4a532-201">To se změní na záhlaví "Oddělení", odstraní sloupec RowVersion a zobrazí jméno a příjmení namísto křestní jméno správce.</span><span class="sxs-lookup"><span data-stu-id="4a532-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="4a532-202">Aktualizace metod Edit</span><span class="sxs-lookup"><span data-stu-id="4a532-202">Update Edit methods</span></span>

<span data-ttu-id="4a532-203">V obou třídy MetadataExchangeClientMode `Edit` metoda a `Details` metodu, přidejte `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="4a532-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="4a532-204">V třídy MetadataExchangeClientMode `Edit` metodu, přidejte předběžné načítání pro správce.</span><span class="sxs-lookup"><span data-stu-id="4a532-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="4a532-205">Nahraďte stávající kód httppost `Edit` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4a532-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="4a532-206">Kód začíná pokusu o čtení z oddělení aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="4a532-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="4a532-207">Pokud `SingleOrDefaultAsync` metoda vrátí hodnotu null, z oddělení byla odstraněna jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="4a532-207">If the `SingleOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="4a532-208">V takovém případě kód používá hodnoty odeslaného formuláře vytvořit entitu oddělení tak, aby stránky pro úpravu můžete zobrazí znovu, zobrazí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="4a532-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="4a532-209">Jako alternativu nebude muset znovu vytvořit entity oddělení, pokud zobrazení pouze chybové zprávy bez opětovné zobrazení pole oddělení.</span><span class="sxs-lookup"><span data-stu-id="4a532-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="4a532-210">Zobrazení ukládá původní `RowVersion` hodnotu ve skrytém poli a tato metoda přijímá hodnotu do `rowVersion` parametru.</span><span class="sxs-lookup"><span data-stu-id="4a532-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="4a532-211">Před voláním `SaveChanges`, budete muset vytvořit z původní `RowVersion` hodnoty vlastnosti `OriginalValues` kolekce entity.</span><span class="sxs-lookup"><span data-stu-id="4a532-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="4a532-212">Pak při Entity Framework vytvoří příkaz SQL pro sadu VS11, tohoto příkazu bude obsahovat klauzuli WHERE, která hledá řádek, který má původní `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4a532-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="4a532-213">Pokud žádné řádky jsou ovlivněny příkazu UPDATE (žádné řádky mít původní `RowVersion` hodnota), vyvolá rozhraní Entity Framework `DbUpdateConcurrencyException` výjimky.</span><span class="sxs-lookup"><span data-stu-id="4a532-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="4a532-214">Kód v bloku catch pro tuto výjimku získá ovlivněné oddělení entity, která má aktualizované hodnoty z `Entries` vlastnost v objektu výjimky.</span><span class="sxs-lookup"><span data-stu-id="4a532-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="4a532-215">`Entries` Kolekce bude obsahovat jen jeden `EntityEntry` objektu.</span><span class="sxs-lookup"><span data-stu-id="4a532-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="4a532-216">Tento objekt slouží k získání nové hodnoty zadané uživatelem a hodnot v aktuální databázi.</span><span class="sxs-lookup"><span data-stu-id="4a532-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="4a532-217">Kód přidá vlastní chybovou zprávu pro každý sloupec, který má různých hodnot v databázi z co uživatel zadaný v úpravách stránky (pouze jedno pole je tady pro zkrácení).</span><span class="sxs-lookup"><span data-stu-id="4a532-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="4a532-218">Nakonec kód nastaví `RowVersion` hodnotu `departmentToUpdate` na novou hodnotu načtených z databáze.</span><span class="sxs-lookup"><span data-stu-id="4a532-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="4a532-219">Tato nová `RowVersion` hodnota bude uložena ve skrytém poli, když upravit stránka se zobrazí znovu a další čas kliknutí **Uložit**, pouze souběžnosti chyby, ke kterým dochází, protože redisplay stránky pro úpravu bude zachycena.</span><span class="sxs-lookup"><span data-stu-id="4a532-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="4a532-220">`ModelState.Remove` Příkazu se totiž `ModelState` má starý `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4a532-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="4a532-221">V zobrazení `ModelState` hodnota pole má přednost před hodnoty vlastností modelu Pokud jsou obě přítomny.</span><span class="sxs-lookup"><span data-stu-id="4a532-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="4a532-222">Aktualizovat zobrazení pro úpravy</span><span class="sxs-lookup"><span data-stu-id="4a532-222">Update Edit view</span></span>

<span data-ttu-id="4a532-223">V *Views/Departments/Edit.cshtml*, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="4a532-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="4a532-224">Přidání skrytého pole k uložení `RowVersion` hodnotu vlastnosti, hned za skryté pole pro `DepartmentID` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4a532-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="4a532-225">Přidejte do rozevíracího seznamu možnost "Vybrat Administrator".</span><span class="sxs-lookup"><span data-stu-id="4a532-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="4a532-226">Testování konfliktů souběžnosti</span><span class="sxs-lookup"><span data-stu-id="4a532-226">Test concurrency conflicts</span></span>

<span data-ttu-id="4a532-227">Spusťte aplikaci a přejděte na stránku oddělení indexu.</span><span class="sxs-lookup"><span data-stu-id="4a532-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="4a532-228">Klikněte pravým tlačítkem na **upravit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě**, klikněte **upravit** hypertextového odkazu pro anglickou oddělení.</span><span class="sxs-lookup"><span data-stu-id="4a532-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="4a532-229">Prohlížeč dvě karty se nyní zobrazují stejné informace.</span><span class="sxs-lookup"><span data-stu-id="4a532-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="4a532-230">Změňte pole na první záložce prohlížeče a klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="4a532-230">Change a field in the first browser tab and click **Save**.</span></span>

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="4a532-232">Prohlížeč zobrazí indexovou stránku s změněné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4a532-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="4a532-233">Změňte pole na druhé záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4a532-233">Change a field in the second browser tab.</span></span>

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="4a532-235">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="4a532-235">Click **Save**.</span></span> <span data-ttu-id="4a532-236">Zobrazí chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="4a532-236">You see an error message:</span></span>

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error.png)

<span data-ttu-id="4a532-238">Klikněte na tlačítko **Uložit** znovu.</span><span class="sxs-lookup"><span data-stu-id="4a532-238">Click **Save** again.</span></span> <span data-ttu-id="4a532-239">Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4a532-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="4a532-240">Uložené hodnoty se zobrazí, jakmile se zobrazí stránka indexu.</span><span class="sxs-lookup"><span data-stu-id="4a532-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="4a532-241">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="4a532-241">Update the Delete page</span></span>

<span data-ttu-id="4a532-242">Odstranění stránky Entity Framework detekuje souběžnosti konflikty způsobené někdo jinak úpravy oddělení podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="4a532-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="4a532-243">Když třídy MetadataExchangeClientMode `Delete` metoda zobrazí potvrzení zobrazení, zobrazení zahrnuje původní `RowVersion` hodnotu ve skrytém poli.</span><span class="sxs-lookup"><span data-stu-id="4a532-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="4a532-244">Hodnota je pak možné HttpPost `Delete` metodu, která je volána, když uživatel potvrdí odstranění.</span><span class="sxs-lookup"><span data-stu-id="4a532-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="4a532-245">Entity Framework vytvoří příkaz SQL DELETE, obsahuje klauzuli WHERE s původní `RowVersion` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4a532-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="4a532-246">Pokud vliv na výsledky příkazu v nulový počet řádků (tj. řádek byl změněn, jakmile se zobrazí stránka potvrzení odstranění), je vyvolána výjimka souběžnosti a HttpGet `Delete` metoda je volána k chybě příznak nastaven na hodnotu true, pokud chcete znovu zobrazit potvrzovací stránku s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="4a532-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="4a532-247">Je také možné, že vzhledem k tomu, že řádek byl odstraněn jiným uživatelem, tak v tom případě se zobrazí žádná chybová zpráva vliv nulový počet řádků.</span><span class="sxs-lookup"><span data-stu-id="4a532-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="4a532-248">Aktualizace metod Delete v oddělení kontroleru</span><span class="sxs-lookup"><span data-stu-id="4a532-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="4a532-249">V *DepartmentsController.cs*, nahraďte třídy MetadataExchangeClientMode `Delete` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4a532-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="4a532-250">Metoda přijímá volitelný parametr, který označuje, zda je právě na stránce zobrazí znovu po chybě souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="4a532-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="4a532-251">Pokud tento příznak má hodnotu true a oddělení již existuje, byla odstraněna jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="4a532-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="4a532-252">V takovém případě kód provede přesměrování na indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="4a532-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="4a532-253">Pokud tento příznak má hodnotu true a oddělení neexistuje, byla změněna jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="4a532-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="4a532-254">V takovém případě kód odešle chybovou zprávu pro zobrazení s využitím `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="4a532-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="4a532-255">Nahraďte kód v HttpPost `Delete` – metoda (s názvem `DeleteConfirmed`) následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="4a532-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="4a532-256">V automaticky generovaný kód, který jste právě nahrazen tato metoda přijaty pouze ID záznamu:</span><span class="sxs-lookup"><span data-stu-id="4a532-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="4a532-257">Změnili jste tento parametr k oddělení instanci entity vytvořené vazače modelu.</span><span class="sxs-lookup"><span data-stu-id="4a532-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="4a532-258">To poskytuje EF přístup k hodnotě vlastnosti RowVersion kromě klíč záznamu.</span><span class="sxs-lookup"><span data-stu-id="4a532-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="4a532-259">Také jste změnili název metody akce z `DeleteConfirmed` k `Delete`.</span><span class="sxs-lookup"><span data-stu-id="4a532-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="4a532-260">Automaticky generovaný kód používá název `DeleteConfirmed` poskytnout metoda HttpPost jedinečnou signaturu.</span><span class="sxs-lookup"><span data-stu-id="4a532-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="4a532-261">(CLR vyžaduje přetížené metody s parametry jinou metodu.) Teď, když podpisy jsou jedinečné, můžete zůstat u konvence MVC a použijte stejný název pro odstranění metody HttpPost a HttpGet.</span><span class="sxs-lookup"><span data-stu-id="4a532-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="4a532-262">Pokud již byla odstraněna z oddělení, `AnyAsync` metoda vrátí hodnotu false a aplikace právě přejde zpět do metody indexu.</span><span class="sxs-lookup"><span data-stu-id="4a532-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="4a532-263">Pokud došlo k chybě souběžnosti je zachycena, kód znovu zobrazí na stránce potvrzení odstranění a zajišťuje, že příznak, který označují, že by se zobrazit zpráva chybě souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="4a532-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="4a532-264">Zobrazení aktualizovat, odstranit</span><span class="sxs-lookup"><span data-stu-id="4a532-264">Update the Delete view</span></span>

<span data-ttu-id="4a532-265">V *Views/Departments/Delete.cshtml*, nahraďte následující kód, který přidá polem chybové zprávy a skrytá pole vlastností DepartmentID a RowVersion automaticky generovaný kód.</span><span class="sxs-lookup"><span data-stu-id="4a532-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="4a532-266">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="4a532-266">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="4a532-267">To provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="4a532-267">This makes the following changes:</span></span>

* <span data-ttu-id="4a532-268">Přidá chybovou zprávu mezi `h2` a `h3` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="4a532-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="4a532-269">Nahradí celý název v FirstMidName **správce** pole.</span><span class="sxs-lookup"><span data-stu-id="4a532-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="4a532-270">Odebere RowVersion pole.</span><span class="sxs-lookup"><span data-stu-id="4a532-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="4a532-271">Přidá skryté pole pro `RowVersion` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4a532-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="4a532-272">Spusťte aplikaci a přejděte na stránku oddělení indexu.</span><span class="sxs-lookup"><span data-stu-id="4a532-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="4a532-273">Klikněte pravým tlačítkem na **odstranit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě**, na první kartě klikněte na tlačítko **upravit** hypertextového odkazu pro anglickou oddělení.</span><span class="sxs-lookup"><span data-stu-id="4a532-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="4a532-274">V prvním okně, změňte jednu z hodnot a klikněte na tlačítko **Uložit**:</span><span class="sxs-lookup"><span data-stu-id="4a532-274">In the first window, change one of the values, and click **Save**:</span></span>

![Stránky pro úpravu oddělení po změně před delete](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="4a532-276">Na druhé kartě klikněte **odstranit**.</span><span class="sxs-lookup"><span data-stu-id="4a532-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="4a532-277">Zobrazí chybová zpráva souběžnosti a oddělení hodnoty se aktualizují s tím, co je aktuálně v databázi.</span><span class="sxs-lookup"><span data-stu-id="4a532-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Stránka potvrzení odstranění oddělení s došlo k chybě souběžnosti](concurrency/_static/delete-error.png)

<span data-ttu-id="4a532-279">Vyberete-li **odstranit** znovu, budete přesměrováni na indexovou stránku, který ukazuje, že byl odstraněn z oddělení.</span><span class="sxs-lookup"><span data-stu-id="4a532-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="4a532-280">Aktualizovat podrobnosti a vytvořit zobrazení</span><span class="sxs-lookup"><span data-stu-id="4a532-280">Update Details and Create views</span></span>

<span data-ttu-id="4a532-281">Můžete volitelně vyčistit v podrobnostech o automaticky generovaný kód a vytvořit zobrazení.</span><span class="sxs-lookup"><span data-stu-id="4a532-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="4a532-282">Nahraďte kód v *Views/Departments/Details.cshtml* odstranění RowVersion sloupce a zobrazit úplné jméno správce.</span><span class="sxs-lookup"><span data-stu-id="4a532-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="4a532-283">Nahraďte kód v *Views/Departments/Create.cshtml* vyberte možnost přidat do rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="4a532-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="4a532-284">Získat kód</span><span class="sxs-lookup"><span data-stu-id="4a532-284">Get the code</span></span>

[<span data-ttu-id="4a532-285">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="4a532-285">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="4a532-286">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4a532-286">Additional resources</span></span>

 <span data-ttu-id="4a532-287">Další informace o tom, jak zpracovat souběžnosti v EF Core najdete v tématu [konfliktů souběžnosti](/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="4a532-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="4a532-288">Další kroky</span><span class="sxs-lookup"><span data-stu-id="4a532-288">Next steps</span></span>

<span data-ttu-id="4a532-289">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="4a532-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4a532-290">Dozvěděli jste se o konfliktů souběžnosti</span><span class="sxs-lookup"><span data-stu-id="4a532-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="4a532-291">Přidání vlastnosti sledování do</span><span class="sxs-lookup"><span data-stu-id="4a532-291">Added a tracking property</span></span>
> * <span data-ttu-id="4a532-292">Vytvořený kontroler oddělení a zobrazení</span><span class="sxs-lookup"><span data-stu-id="4a532-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="4a532-293">Aktualizace zobrazení indexu</span><span class="sxs-lookup"><span data-stu-id="4a532-293">Updated Index view</span></span>
> * <span data-ttu-id="4a532-294">Aktualizace metod Edit</span><span class="sxs-lookup"><span data-stu-id="4a532-294">Updated Edit methods</span></span>
> * <span data-ttu-id="4a532-295">Aktualizované zobrazení pro úpravy</span><span class="sxs-lookup"><span data-stu-id="4a532-295">Updated Edit view</span></span>
> * <span data-ttu-id="4a532-296">Konflikty otestované souběžnosti</span><span class="sxs-lookup"><span data-stu-id="4a532-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="4a532-297">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="4a532-297">Updated the Delete page</span></span>
> * <span data-ttu-id="4a532-298">Aktualizovat podrobnosti a vytvořit zobrazení</span><span class="sxs-lookup"><span data-stu-id="4a532-298">Updated Details and Create views</span></span>

<span data-ttu-id="4a532-299">Přejděte k dalšímu kurzu, kde se naučíte, jak implementovat tabulky na hierarchii dědičnosti pro entity instruktorem a studentů.</span><span class="sxs-lookup"><span data-stu-id="4a532-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="4a532-300">Další: Implementace tabulky za hierarchie dědičnosti</span><span class="sxs-lookup"><span data-stu-id="4a532-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
