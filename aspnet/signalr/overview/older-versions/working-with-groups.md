---
uid: signalr/overview/older-versions/working-with-groups
title: Práce se skupinami v knihovně SignalR 1.x | Dokumentace Microsoftu
author: pfletcher
description: Toto téma popisuje, jak zachovat informace o členství ve skupině pomocí rozhraní API pro rozbočovač.
ms.author: riande
ms.date: 10/21/2013
ms.assetid: 22929efd-68c9-4609-b76d-f8ba42fda01e
msc.legacyurl: /signalr/overview/older-versions/working-with-groups
msc.type: authoredcontent
ms.openlocfilehash: 5096e7a5eb8704762a21eeb88a128d92e75ede60
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287485"
---
<a name="working-with-groups-in-signalr-1x"></a><span data-ttu-id="fc4ec-103">Práce se skupinami v knihovně SignalR 1.x</span><span class="sxs-lookup"><span data-stu-id="fc4ec-103">Working with Groups in SignalR 1.x</span></span>
====================
<span data-ttu-id="fc4ec-104">podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="fc4ec-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="fc4ec-105">Toto téma popisuje, jak přidat uživatele do skupin a zachovat informace o členství ve skupině.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-105">This topic describes how to add users to groups and persist group membership information.</span></span>


## <a name="overview"></a><span data-ttu-id="fc4ec-106">Přehled</span><span class="sxs-lookup"><span data-stu-id="fc4ec-106">Overview</span></span>

<span data-ttu-id="fc4ec-107">Skupinami v knihovně SignalR poskytuje metodu pro vysílání zpráv do zadaného podmnožiny připojených klientů.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-107">Groups in SignalR provide a method for broadcasting messages to specified subsets of connected clients.</span></span> <span data-ttu-id="fc4ec-108">Skupina může mít libovolný počet klientů a klienta můžete mít libovolný počet skupin.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-108">A group can have any number of clients, and a client can be a member of any number of groups.</span></span> <span data-ttu-id="fc4ec-109">Není nutné explicitně vytvářet skupiny.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-109">You don't have to explicitly create groups.</span></span> <span data-ttu-id="fc4ec-110">V důsledku toho skupina se automaticky vytvoří při prvním volání Groups.Add zadáte její název a se odstraní, když odeberete poslední připojení z členství v ní.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-110">In effect, a group is automatically created the first time you specify its name in a call to Groups.Add, and it is deleted when you remove the last connection from membership in it.</span></span> <span data-ttu-id="fc4ec-111">Úvod do používání skupin najdete v tématu [Správa členství ve skupině ze třídy rozbočovače](index.md) v rozhraní API rozbočovače – Příručka pro Server.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-111">For an introduction to using groups, see [How to manage group membership from the Hub class](index.md) in the Hubs API - Server Guide.</span></span>

<span data-ttu-id="fc4ec-112">Neexistuje žádné rozhraní API pro získání seznamu členství ve skupinách nebo seznam skupin.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-112">There is no API for getting a group membership list or a list of groups.</span></span> <span data-ttu-id="fc4ec-113">Funkce SignalR odesílá zprávy do klientů a skupin založené na modelu pub/sub a serveru neumožňuje spravovat seznam skupin nebo členství ve skupinách.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-113">SignalR sends messages to clients and groups based on a pub/sub model, and the server does not maintain lists of groups or group memberships.</span></span> <span data-ttu-id="fc4ec-114">To pomáhá maximalizovat škálovatelnost, protože pokaždé, když přidáte uzel do webové farmy, jakýkoli stav, který udržuje SignalR musí být rozšířena na nový uzel.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-114">This helps maximize scalability, because whenever you add a node to a web farm, any state that SignalR maintains has to be propagated to the new node.</span></span>

<span data-ttu-id="fc4ec-115">Při přidávání uživatele do skupiny použití `Groups.Add` metoda, uživatel dostane zprávy přesměruje do této skupiny pro dobu trvání aktuálního připojení, ale nad rámec aktuální připojení není trvalý členství uživatele ve skupině.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-115">When you add a user to a group using the `Groups.Add` method, the user receives messages directed to that group for the duration of the current connection, but the user's membership in that group is not persisted beyond the current connection.</span></span> <span data-ttu-id="fc4ec-116">Pokud chcete trvale uchovávat informace o skupinách a členství ve skupině, musí tato data ukládat v úložišti, jako jsou databáze nebo Azure table storage.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-116">If you want to permanently retain information about groups and group membership, you must store that data in a repository such as a database or Azure table storage.</span></span> <span data-ttu-id="fc4ec-117">Potom pokaždé, když uživatel připojí k vaší aplikaci načíst z úložiště skupiny, které uživatel patří a ručně přidejte tohoto uživatele do těchto skupin.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-117">Then, each time a user connects to your application, you retrieve from the repository which groups the user belongs to, and manually add that user to those groups.</span></span>

<span data-ttu-id="fc4ec-118">Když znovu připojovat po dočasné přerušení, uživatel automaticky znovu připojí dříve přiřazené skupiny.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-118">When reconnecting after a temporary disruption, the user automatically re-joins the previously-assigned groups.</span></span> <span data-ttu-id="fc4ec-119">Automatické opětovné připojování ke rozbočovačů skupinu platí pouze při opětovné připojení, ne už při vytvoření nového připojení.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-119">Automatically rejoining a group only applies when reconnecting, not when establishing a new connection.</span></span> <span data-ttu-id="fc4ec-120">Digitálně podepsaný token je předán z klienta, který obsahuje seznam dříve přiřazené skupiny.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-120">A digitally-signed token is passed from the client that contains the list of previously-assigned groups.</span></span> <span data-ttu-id="fc4ec-121">Pokud chcete ověřit, zda uživatel patří do požadované skupiny, výchozí chování můžete přepsat.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-121">If you want to verify whether the user belongs to the requested groups, you can override the default behavior.</span></span>

<span data-ttu-id="fc4ec-122">Toto téma obsahuje následující oddíly:</span><span class="sxs-lookup"><span data-stu-id="fc4ec-122">This topic includes the following sections:</span></span>

- [<span data-ttu-id="fc4ec-123">Přidávání a odebírání uživatelů</span><span class="sxs-lookup"><span data-stu-id="fc4ec-123">Adding and removing users</span></span>](#add)
- [<span data-ttu-id="fc4ec-124">Volání členů skupiny</span><span class="sxs-lookup"><span data-stu-id="fc4ec-124">Calling members of a group</span></span>](#call)
- [<span data-ttu-id="fc4ec-125">Ukládání členství ve skupinách v databázi</span><span class="sxs-lookup"><span data-stu-id="fc4ec-125">Storing group membership in a database</span></span>](#storedatabase)
- [<span data-ttu-id="fc4ec-126">Ukládání členství ve skupině ve službě Azure table storage</span><span class="sxs-lookup"><span data-stu-id="fc4ec-126">Storing group membership in Azure table storage</span></span>](#storeazuretable)
- [<span data-ttu-id="fc4ec-127">Ověřování členství ve skupině při obnovování spojení</span><span class="sxs-lookup"><span data-stu-id="fc4ec-127">Verifying group membership when reconnecting</span></span>](#verify)

<a id="add"></a>

## <a name="adding-and-removing-users"></a><span data-ttu-id="fc4ec-128">Přidávání a odebírání uživatelů</span><span class="sxs-lookup"><span data-stu-id="fc4ec-128">Adding and removing users</span></span>

<span data-ttu-id="fc4ec-129">Chcete-li přidat nebo odebrat uživatele ze skupiny, zavolejte [přidat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) nebo [odebrat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) metody a předejte jí id uživatele, připojení a názvu skupiny jako parametry.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-129">To add or remove users from a group, you call the [Add](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) or [Remove](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) methods, and pass in the user's connection id and group's name as parameters.</span></span> <span data-ttu-id="fc4ec-130">Není nutné ručně odebrat uživatele ze skupiny po ukončení připojení.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-130">You do not need to manually remove a user from a group when the connection ends.</span></span>

<span data-ttu-id="fc4ec-131">Následující příklad ukazuje `Groups.Add` a `Groups.Remove` metod používaných v metodách rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-131">The following example shows the `Groups.Add` and `Groups.Remove` methods used in Hub methods.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample1.cs?highlight=5,10)]

<span data-ttu-id="fc4ec-132">`Groups.Add` a `Groups.Remove` metody spustit asynchronně.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-132">The `Groups.Add` and `Groups.Remove` methods execute asynchronously.</span></span>

<span data-ttu-id="fc4ec-133">Pokud chcete přidat klienta do skupiny a okamžitě odešle zprávu do klienta pomocí skupiny, budete muset Ujistěte se, že metoda Groups.Add skončí jako první.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-133">If you want to add a client to a group and immediately send a message to the client by using the group, you have to make sure that the Groups.Add method finishes first.</span></span> <span data-ttu-id="fc4ec-134">Následující příklady kódu ukazují, jak to udělat, ji pomocí kódu, který funguje v rozhraní .NET 4.5 a pomocí kódu, který funguje v rozhraní .NET 4.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-134">The following code examples show how to do that, one by using code that works in .NET 4.5, and one by using code that works in .NET 4.</span></span>

#### <a name="asynchronous-net-45-example"></a><span data-ttu-id="fc4ec-135">Asynchronní rozhraní .NET 4.5 Příklad</span><span class="sxs-lookup"><span data-stu-id="fc4ec-135">Asynchronous .NET 4.5 Example</span></span>

[!code-csharp[Main](working-with-groups/samples/sample2.cs?highlight=1,3)]

#### <a name="asynchronous-net-4-example"></a><span data-ttu-id="fc4ec-136">Asynchronní rozhraní .NET 4 příklad</span><span class="sxs-lookup"><span data-stu-id="fc4ec-136">Asynchronous .NET 4 Example</span></span>

[!code-csharp[Main](working-with-groups/samples/sample3.cs?highlight=3-4)]

<span data-ttu-id="fc4ec-137">Obecně by neměla zahrnovat `await` při volání `Groups.Remove` metoda vzhledem k tomu, že id připojení, který se pokoušíte odebrat může nadále již nebudou dostupné.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-137">In general, you should not include `await` when calling the `Groups.Remove` method because the connection id that you are trying to remove might no longer be available.</span></span> <span data-ttu-id="fc4ec-138">V takovém případě `TaskCanceledException` je vyvolána výjimka, jakmile vyprší časový limit žádosti. Pokud vaše aplikace musíte zajistit, že uživatel odebral ze skupiny před odesláním zprávy do skupiny, můžete přidat `await` před Groups.Remove a pak catch `TaskCanceledException` výjimku, která by mohla být vyvolána.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-138">In that case, `TaskCanceledException` is thrown after the request times out. If your application must ensure that the user has been removed from the group before sending a message to the group, you can add `await` before Groups.Remove, and then catch the `TaskCanceledException` exception that might be thrown.</span></span>

<a id="call"></a>

## <a name="calling-members-of-a-group"></a><span data-ttu-id="fc4ec-139">Volání členů skupiny</span><span class="sxs-lookup"><span data-stu-id="fc4ec-139">Calling members of a group</span></span>

<span data-ttu-id="fc4ec-140">Odesílat zprávy do všech členů skupiny nebo pouze členové zadané skupiny, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-140">You can send messages to all of the members of a group or only specified members of the group, as shown in the following examples.</span></span>

- <span data-ttu-id="fc4ec-141">**Všechny** připojených klientů v zadané skupině.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-141">**All** connected clients in a specified group.</span></span> 

    [!code-css[Main](working-with-groups/samples/sample4.css)]
- <span data-ttu-id="fc4ec-142">Všechny připojené klienty v zadané skupině **s výjimkou určitých klientech**, identifikovaný podle ID připojení.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-142">All connected clients in a specified group **except the specified clients**, identified by connection ID.</span></span> 

    [!code-csharp[Main](working-with-groups/samples/sample5.cs)]
- <span data-ttu-id="fc4ec-143">Všechny připojené klienty v zadané skupině **s výjimkou volajícího klienta**.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-143">All connected clients in a specified group **except the calling client**.</span></span> 

    [!code-css[Main](working-with-groups/samples/sample6.css)]

<a id="storedatabase"></a>

## <a name="storing-group-membership-in-a-database"></a><span data-ttu-id="fc4ec-144">Ukládání členství ve skupinách v databázi</span><span class="sxs-lookup"><span data-stu-id="fc4ec-144">Storing group membership in a database</span></span>

<span data-ttu-id="fc4ec-145">Následující příklady ukazují, jak uchovávat informace pro skupiny a uživatele v databázi.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-145">The following examples show how to retain group and user information in a database.</span></span> <span data-ttu-id="fc4ec-146">Můžete použít libovolný technologií přístupu dat; Následující příklad ukazuje, jak definovat modely s využitím rozhraní Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-146">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="fc4ec-147">Tyto modely entity odpovídají databázové tabulky a pole.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-147">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="fc4ec-148">Datová struktura se může značně lišit v závislosti na požadavcích vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-148">Your data structure could vary considerably depending on the requirements of your application.</span></span> <span data-ttu-id="fc4ec-149">Tento příklad obsahuje třídu s názvem `ConversationRoom` který bude jedinečný pro aplikaci, která umožňuje uživatelům připojit se k konverzací o různých tématům, jako jsou sportovní nebo zahrady.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-149">This example includes a class named `ConversationRoom` which would be unique to an application that enables users to join conversations about different subjects, such as sports or gardening.</span></span> <span data-ttu-id="fc4ec-150">Tento příklad také zahrnuje třídu pro připojení.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-150">This example also includes a class for the connections.</span></span> <span data-ttu-id="fc4ec-151">Třída připojení není nezbytně nutná pro sledování členství ve skupině, ale je často součástí robustní řešení pro sledování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-151">The connection class is not absolutely required for tracking group membership but is frequently part of robust solution to tracking users.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample7.cs)]

<span data-ttu-id="fc4ec-152">Potom můžete v rozbočovači, načtěte informace o skupině a uživatele z databáze a ručně přidat uživatele do příslušných skupin.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-152">Then, in the hub, you can retrieve the group and user information from the database and manually add the user to the appropriate groups.</span></span> <span data-ttu-id="fc4ec-153">V příkladu neobsahuje kód pro sledování připojení uživatelů.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-153">The example does not include code for tracking the user connections.</span></span> <span data-ttu-id="fc4ec-154">V tomto příkladu `await` – klíčové slovo není použita před `Groups.Add` vzhledem k tomu, že zpráva se odešle okamžitě členy skupiny.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-154">In this example, the `await` keyword is not applied before `Groups.Add` because a message is not immediately sent to members of the group.</span></span> <span data-ttu-id="fc4ec-155">Pokud chcete odeslat zprávu na všechny členy skupiny bezprostředně po přidání nového člena, chcete použít `await` – klíčové slovo, aby se zajistilo dokončení asynchronní operace.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-155">If you want to send a message to all members of the group immediately after adding the new member, you would want to apply the `await` keyword to make sure the asynchronous operation has completed.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample8.cs)]

<a id="storeazuretable"></a>

## <a name="storing-group-membership-in-azure-table-storage"></a><span data-ttu-id="fc4ec-156">Ukládání členství ve skupině ve službě Azure table storage</span><span class="sxs-lookup"><span data-stu-id="fc4ec-156">Storing group membership in Azure table storage</span></span>

<span data-ttu-id="fc4ec-157">Použití Azure table storage k ukládání informací o skupině a uživatel je podobný používání databáze.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-157">Using Azure table storage to store group and user information is similar to using a database.</span></span> <span data-ttu-id="fc4ec-158">Následující příklad ukazuje entitu tabulky, která uloží uživatelské jméno a název skupiny.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-158">The following example shows a table entity that stores the user name and group name.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample9.cs)]

<span data-ttu-id="fc4ec-159">V centru načtete přiřazených skupin, když se uživatel připojí.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-159">In the hub, you retrieve the assigned groups when the user connects.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample10.cs)]

<a id="verify"></a>

## <a name="verifying-group-membership-when-reconnecting"></a><span data-ttu-id="fc4ec-160">Ověřování členství ve skupině při obnovování spojení</span><span class="sxs-lookup"><span data-stu-id="fc4ec-160">Verifying group membership when reconnecting</span></span>

<span data-ttu-id="fc4ec-161">Ve výchozím nastavení SignalR automaticky znovu přiřadí uživatele do příslušných skupin při obnovování spojení z dočasné přerušení, jako je například pokud je připojení vyřadit a znovu vytvořit předtím, než vyprší časový limit připojení. Informace o skupině pro daného uživatele je předán do tokenu při opětovné připojení, a tento token ověření na serveru.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-161">By default, SignalR automatically re-assigns a user to the appropriate groups when reconnecting from a temporary disruption, such as when a connection is dropped and re-established before the connection times out. The user's group information is passed in a token when reconnecting, and that token is verified on the server.</span></span> <span data-ttu-id="fc4ec-162">Informace o procesu ověření u některých uživatelů do skupin najdete v tématu [některých skupin při obnovování spojení](index.md).</span><span class="sxs-lookup"><span data-stu-id="fc4ec-162">For information about the verification process for rejoining users to groups, see [Rejoining groups when reconnecting](index.md).</span></span>

<span data-ttu-id="fc4ec-163">Obecně platí abyste používali výchozí chování automaticky opětovné připojování ke rozbočovačů, že skupiny na znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-163">In general, you should use the default behavior of automatically rejoining groups on reconnect.</span></span> <span data-ttu-id="fc4ec-164">Funkce SignalR skupiny nejsou určeny jako vhodný mechanismus zabezpečení pro omezení přístupu k citlivým datům.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-164">SignalR groups are not intended as a security mechanism for restricting access to sensitive data.</span></span> <span data-ttu-id="fc4ec-165">Pokud aplikace musí zkontrolovat členství ve skupině uživatele při obnovování spojení, však můžete přepsat výchozí chování.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-165">However, if your application must double-check a user's group membership when reconnecting, you can override the default behavior.</span></span> <span data-ttu-id="fc4ec-166">Změna výchozího chování lze přidat zatížení do databáze protože členství ve skupině uživatele musí být načten pro každý opětovné připojení, a nikoli jen když se uživatel připojí.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-166">Changing the default behavior can add a burden to your database because a user's group membership must be retrieved for each reconnection rather than just when the user connects.</span></span>

<span data-ttu-id="fc4ec-167">Pokud je nutné ověřit členství ve skupině na znovu připojit, vytvořte nový modul kanálu rozbočovače, který vrátí seznam přiřazených skupin, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-167">If you must verify group membership on reconnect, create a new hub pipeline module that returns a list of assigned groups, as shown below.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample11.cs)]

<span data-ttu-id="fc4ec-168">Poté přidejte modul do kanálu rozbočovače, jak je zdůrazněno níže.</span><span class="sxs-lookup"><span data-stu-id="fc4ec-168">Then, add that module to the hub pipeline, as highlighted below.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample12.cs?highlight=10)]
