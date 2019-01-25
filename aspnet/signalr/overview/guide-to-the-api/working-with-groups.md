---
uid: signalr/overview/guide-to-the-api/working-with-groups
title: Práce se skupinami v knihovně SignalR | Dokumentace Microsoftu
author: bradygaster
description: Toto téma popisuje, jak zachovat informace o členství ve skupině pomocí rozhraní API pro rozbočovač.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: cd378ecd-3e9e-4236-b902-65916d85a048
msc.legacyurl: /signalr/overview/guide-to-the-api/working-with-groups
msc.type: authoredcontent
ms.openlocfilehash: 384b7e5f07fa46ea3cc32e5c18c3c2327b7aedd3
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836802"
---
<a name="working-with-groups-in-signalr"></a><span data-ttu-id="6e57d-103">Práce se skupinami v knihovně SignalR</span><span class="sxs-lookup"><span data-stu-id="6e57d-103">Working with Groups in SignalR</span></span>
====================
<span data-ttu-id="6e57d-104">podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="6e57d-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="6e57d-105">Toto téma popisuje, jak přidat uživatele do skupin a zachovat informace o členství ve skupině.</span><span class="sxs-lookup"><span data-stu-id="6e57d-105">This topic describes how to add users to groups and persist group membership information.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="6e57d-106">Verze softwaru použitým v tomto tématu</span><span class="sxs-lookup"><span data-stu-id="6e57d-106">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="6e57d-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="6e57d-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="6e57d-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="6e57d-108">.NET 4.5</span></span>
> - <span data-ttu-id="6e57d-109">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="6e57d-109">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="6e57d-110">Předchozích verzích tohoto tématu</span><span class="sxs-lookup"><span data-stu-id="6e57d-110">Previous versions of this topic</span></span>
>
> <span data-ttu-id="6e57d-111">Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="6e57d-111">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="6e57d-112">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="6e57d-112">Questions and comments</span></span>
>
> <span data-ttu-id="6e57d-113">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="6e57d-113">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="6e57d-114">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="6e57d-114">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="overview"></a><span data-ttu-id="6e57d-115">Přehled</span><span class="sxs-lookup"><span data-stu-id="6e57d-115">Overview</span></span>

<span data-ttu-id="6e57d-116">Skupinami v knihovně SignalR poskytuje metodu pro vysílání zpráv do zadaného podmnožiny připojených klientů.</span><span class="sxs-lookup"><span data-stu-id="6e57d-116">Groups in SignalR provide a method for broadcasting messages to specified subsets of connected clients.</span></span> <span data-ttu-id="6e57d-117">Skupina může mít libovolný počet klientů a klienta můžete mít libovolný počet skupin.</span><span class="sxs-lookup"><span data-stu-id="6e57d-117">A group can have any number of clients, and a client can be a member of any number of groups.</span></span> <span data-ttu-id="6e57d-118">Není nutné explicitně vytvářet skupiny.</span><span class="sxs-lookup"><span data-stu-id="6e57d-118">You don't have to explicitly create groups.</span></span> <span data-ttu-id="6e57d-119">V důsledku toho skupina se automaticky vytvoří při prvním volání Groups.Add zadáte její název a se odstraní, když odeberete poslední připojení z členství v ní.</span><span class="sxs-lookup"><span data-stu-id="6e57d-119">In effect, a group is automatically created the first time you specify its name in a call to Groups.Add, and it is deleted when you remove the last connection from membership in it.</span></span> <span data-ttu-id="6e57d-120">Úvod do používání skupin najdete v tématu [Správa členství ve skupině ze třídy rozbočovače](hubs-api-guide-server.md#groupsfromhub) v rozhraní API rozbočovače – Příručka pro Server.</span><span class="sxs-lookup"><span data-stu-id="6e57d-120">For an introduction to using groups, see [How to manage group membership from the Hub class](hubs-api-guide-server.md#groupsfromhub) in the Hubs API - Server Guide.</span></span>

<span data-ttu-id="6e57d-121">Neexistuje žádné rozhraní API pro získání seznamu členství ve skupinách nebo seznam skupin.</span><span class="sxs-lookup"><span data-stu-id="6e57d-121">There is no API for getting a group membership list or a list of groups.</span></span> <span data-ttu-id="6e57d-122">Funkce SignalR odesílá zprávy do klientů a skupin založené na modelu pub/sub a serveru neumožňuje spravovat seznam skupin nebo členství ve skupinách.</span><span class="sxs-lookup"><span data-stu-id="6e57d-122">SignalR sends messages to clients and groups based on a pub/sub model, and the server does not maintain lists of groups or group memberships.</span></span> <span data-ttu-id="6e57d-123">To pomáhá maximalizovat škálovatelnost, protože pokaždé, když přidáte uzel do webové farmy, jakýkoli stav, který udržuje SignalR musí být rozšířena na nový uzel.</span><span class="sxs-lookup"><span data-stu-id="6e57d-123">This helps maximize scalability, because whenever you add a node to a web farm, any state that SignalR maintains has to be propagated to the new node.</span></span>

<span data-ttu-id="6e57d-124">Při přidávání uživatele do skupiny použití `Groups.Add` metoda, uživatel dostane zprávy přesměruje do této skupiny pro dobu trvání aktuálního připojení, ale nad rámec aktuální připojení není trvalý členství uživatele ve skupině.</span><span class="sxs-lookup"><span data-stu-id="6e57d-124">When you add a user to a group using the `Groups.Add` method, the user receives messages directed to that group for the duration of the current connection, but the user's membership in that group is not persisted beyond the current connection.</span></span> <span data-ttu-id="6e57d-125">Pokud chcete trvale uchovávat informace o skupinách a členství ve skupině, musí tato data ukládat v úložišti, jako jsou databáze nebo Azure table storage.</span><span class="sxs-lookup"><span data-stu-id="6e57d-125">If you want to permanently retain information about groups and group membership, you must store that data in a repository such as a database or Azure table storage.</span></span> <span data-ttu-id="6e57d-126">Potom pokaždé, když uživatel připojí k vaší aplikaci načíst z úložiště skupiny, které uživatel patří a ručně přidejte tohoto uživatele do těchto skupin.</span><span class="sxs-lookup"><span data-stu-id="6e57d-126">Then, each time a user connects to your application, you retrieve from the repository which groups the user belongs to, and manually add that user to those groups.</span></span>

<span data-ttu-id="6e57d-127">Když znovu připojovat po dočasné přerušení, uživatel automaticky znovu připojí dříve přiřazené skupiny.</span><span class="sxs-lookup"><span data-stu-id="6e57d-127">When reconnecting after a temporary disruption, the user automatically re-joins the previously-assigned groups.</span></span> <span data-ttu-id="6e57d-128">Automatické opětovné připojování ke rozbočovačů skupinu platí pouze při opětovné připojení, ne už při vytvoření nového připojení.</span><span class="sxs-lookup"><span data-stu-id="6e57d-128">Automatically rejoining a group only applies when reconnecting, not when establishing a new connection.</span></span> <span data-ttu-id="6e57d-129">Digitálně podepsaný token je předán z klienta, který obsahuje seznam dříve přiřazené skupiny.</span><span class="sxs-lookup"><span data-stu-id="6e57d-129">A digitally-signed token is passed from the client that contains the list of previously-assigned groups.</span></span> <span data-ttu-id="6e57d-130">Pokud chcete ověřit, zda uživatel patří do požadované skupiny, výchozí chování můžete přepsat.</span><span class="sxs-lookup"><span data-stu-id="6e57d-130">If you want to verify whether the user belongs to the requested groups, you can override the default behavior.</span></span>

<span data-ttu-id="6e57d-131">Toto téma obsahuje následující oddíly:</span><span class="sxs-lookup"><span data-stu-id="6e57d-131">This topic includes the following sections:</span></span>

- [<span data-ttu-id="6e57d-132">Přidávání a odebírání uživatelů</span><span class="sxs-lookup"><span data-stu-id="6e57d-132">Adding and removing users</span></span>](#add)
- [<span data-ttu-id="6e57d-133">Volání členů skupiny</span><span class="sxs-lookup"><span data-stu-id="6e57d-133">Calling members of a group</span></span>](#call)
- [<span data-ttu-id="6e57d-134">Ukládání členství ve skupinách v databázi</span><span class="sxs-lookup"><span data-stu-id="6e57d-134">Storing group membership in a database</span></span>](#storedatabase)
- [<span data-ttu-id="6e57d-135">Ukládání členství ve skupině ve službě Azure table storage</span><span class="sxs-lookup"><span data-stu-id="6e57d-135">Storing group membership in Azure table storage</span></span>](#storeazuretable)
- [<span data-ttu-id="6e57d-136">Ověřování členství ve skupině při obnovování spojení</span><span class="sxs-lookup"><span data-stu-id="6e57d-136">Verifying group membership when reconnecting</span></span>](#verify)

<a id="add"></a>

## <a name="adding-and-removing-users"></a><span data-ttu-id="6e57d-137">Přidávání a odebírání uživatelů</span><span class="sxs-lookup"><span data-stu-id="6e57d-137">Adding and removing users</span></span>

<span data-ttu-id="6e57d-138">Chcete-li přidat nebo odebrat uživatele ze skupiny, zavolejte [přidat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) nebo [odebrat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) metody a předejte jí id uživatele, připojení a názvu skupiny jako parametry.</span><span class="sxs-lookup"><span data-stu-id="6e57d-138">To add or remove users from a group, you call the [Add](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) or [Remove](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) methods, and pass in the user's connection id and group's name as parameters.</span></span> <span data-ttu-id="6e57d-139">Není nutné ručně odebrat uživatele ze skupiny po ukončení připojení.</span><span class="sxs-lookup"><span data-stu-id="6e57d-139">You do not need to manually remove a user from a group when the connection ends.</span></span>

<span data-ttu-id="6e57d-140">Následující příklad ukazuje `Groups.Add` a `Groups.Remove` metod používaných v metodách rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6e57d-140">The following example shows the `Groups.Add` and `Groups.Remove` methods used in Hub methods.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample1.cs?highlight=5,10)]

<span data-ttu-id="6e57d-141">`Groups.Add` a `Groups.Remove` metody spustit asynchronně.</span><span class="sxs-lookup"><span data-stu-id="6e57d-141">The `Groups.Add` and `Groups.Remove` methods execute asynchronously.</span></span>

<span data-ttu-id="6e57d-142">Pokud chcete přidat klienta do skupiny a okamžitě odešle zprávu do klienta pomocí skupiny, budete muset Ujistěte se, že metoda Groups.Add skončí jako první.</span><span class="sxs-lookup"><span data-stu-id="6e57d-142">If you want to add a client to a group and immediately send a message to the client by using the group, you have to make sure that the Groups.Add method finishes first.</span></span> <span data-ttu-id="6e57d-143">Následující příklady kódu ukazují, jak to udělat.</span><span class="sxs-lookup"><span data-stu-id="6e57d-143">The following code examples show how to do that.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample2.cs?highlight=1,3)]

<span data-ttu-id="6e57d-144">Obecně by neměla zahrnovat `await` při volání `Groups.Remove` metoda vzhledem k tomu, že id připojení, který se pokoušíte odebrat může nadále již nebudou dostupné.</span><span class="sxs-lookup"><span data-stu-id="6e57d-144">In general, you should not include `await` when calling the `Groups.Remove` method because the connection id that you are trying to remove might no longer be available.</span></span> <span data-ttu-id="6e57d-145">V takovém případě `TaskCanceledException` je vyvolána výjimka, jakmile vyprší časový limit žádosti. Pokud vaše aplikace musíte zajistit, že uživatel odebral ze skupiny před odesláním zprávy do skupiny, můžete přidat `await` před `Groups.Remove`a potom zachytit `TaskCanceledException` výjimku, která by mohla být vyvolána.</span><span class="sxs-lookup"><span data-stu-id="6e57d-145">In that case, `TaskCanceledException` is thrown after the request times out. If your application must ensure that the user has been removed from the group before sending a message to the group, you can add `await` before `Groups.Remove`, and then catch the `TaskCanceledException` exception that might be thrown.</span></span>

<a id="call"></a>

## <a name="calling-members-of-a-group"></a><span data-ttu-id="6e57d-146">Volání členů skupiny</span><span class="sxs-lookup"><span data-stu-id="6e57d-146">Calling members of a group</span></span>

<span data-ttu-id="6e57d-147">Odesílat zprávy do všech členů skupiny nebo pouze členové zadané skupiny, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="6e57d-147">You can send messages to all of the members of a group or only specified members of the group, as shown in the following examples.</span></span>

- <span data-ttu-id="6e57d-148">**Všechny** připojených klientů v zadané skupině.</span><span class="sxs-lookup"><span data-stu-id="6e57d-148">**All** connected clients in a specified group.</span></span>

    [!code-css[Main](working-with-groups/samples/sample3.css)]
- <span data-ttu-id="6e57d-149">Všechny připojené klienty v zadané skupině **s výjimkou určitých klientech**, identifikovaný podle ID připojení.</span><span class="sxs-lookup"><span data-stu-id="6e57d-149">All connected clients in a specified group **except the specified clients**, identified by connection ID.</span></span>

    [!code-csharp[Main](working-with-groups/samples/sample4.cs)]
- <span data-ttu-id="6e57d-150">Všechny připojené klienty v zadané skupině **s výjimkou volajícího klienta**.</span><span class="sxs-lookup"><span data-stu-id="6e57d-150">All connected clients in a specified group **except the calling client**.</span></span>

    [!code-css[Main](working-with-groups/samples/sample5.css)]

<a id="storedatabase"></a>

## <a name="storing-group-membership-in-a-database"></a><span data-ttu-id="6e57d-151">Ukládání členství ve skupinách v databázi</span><span class="sxs-lookup"><span data-stu-id="6e57d-151">Storing group membership in a database</span></span>

<span data-ttu-id="6e57d-152">Následující příklady ukazují, jak uchovávat informace pro skupiny a uživatele v databázi.</span><span class="sxs-lookup"><span data-stu-id="6e57d-152">The following examples show how to retain group and user information in a database.</span></span> <span data-ttu-id="6e57d-153">Můžete použít libovolný technologií přístupu dat; Následující příklad ukazuje, jak definovat modely s využitím rozhraní Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="6e57d-153">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="6e57d-154">Tyto modely entity odpovídají databázové tabulky a pole.</span><span class="sxs-lookup"><span data-stu-id="6e57d-154">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="6e57d-155">Datová struktura se může značně lišit v závislosti na požadavcích vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="6e57d-155">Your data structure could vary considerably depending on the requirements of your application.</span></span> <span data-ttu-id="6e57d-156">Tento příklad obsahuje třídu s názvem `ConversationRoom` který bude jedinečný pro aplikaci, která umožňuje uživatelům připojit se k konverzací o různých tématům, jako jsou sportovní nebo zahrady.</span><span class="sxs-lookup"><span data-stu-id="6e57d-156">This example includes a class named `ConversationRoom` which would be unique to an application that enables users to join conversations about different subjects, such as sports or gardening.</span></span> <span data-ttu-id="6e57d-157">Tento příklad také zahrnuje třídu pro připojení.</span><span class="sxs-lookup"><span data-stu-id="6e57d-157">This example also includes a class for the connections.</span></span> <span data-ttu-id="6e57d-158">Třída připojení není nezbytně nutná pro sledování členství ve skupině, ale je často součástí robustní řešení pro sledování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="6e57d-158">The connection class is not absolutely required for tracking group membership but is frequently part of robust solution to tracking users.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample6.cs)]

<span data-ttu-id="6e57d-159">Potom můžete v rozbočovači, načtěte informace o skupině a uživatele z databáze a ručně přidat uživatele do příslušných skupin.</span><span class="sxs-lookup"><span data-stu-id="6e57d-159">Then, in the hub, you can retrieve the group and user information from the database and manually add the user to the appropriate groups.</span></span> <span data-ttu-id="6e57d-160">V příkladu neobsahuje kód pro sledování připojení uživatelů.</span><span class="sxs-lookup"><span data-stu-id="6e57d-160">The example does not include code for tracking the user connections.</span></span> <span data-ttu-id="6e57d-161">V tomto příkladu `await` – klíčové slovo není použita před `Groups.Add` vzhledem k tomu, že zpráva se odešle okamžitě členy skupiny.</span><span class="sxs-lookup"><span data-stu-id="6e57d-161">In this example, the `await` keyword is not applied before `Groups.Add` because a message is not immediately sent to members of the group.</span></span> <span data-ttu-id="6e57d-162">Pokud chcete odeslat zprávu na všechny členy skupiny bezprostředně po přidání nového člena, chcete použít `await` – klíčové slovo, aby se zajistilo dokončení asynchronní operace.</span><span class="sxs-lookup"><span data-stu-id="6e57d-162">If you want to send a message to all members of the group immediately after adding the new member, you would want to apply the `await` keyword to make sure the asynchronous operation has completed.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample7.cs)]

<a id="storeazuretable"></a>

## <a name="storing-group-membership-in-azure-table-storage"></a><span data-ttu-id="6e57d-163">Ukládání členství ve skupině ve službě Azure table storage</span><span class="sxs-lookup"><span data-stu-id="6e57d-163">Storing group membership in Azure table storage</span></span>

<span data-ttu-id="6e57d-164">Použití Azure table storage k ukládání informací o skupině a uživatel je podobný používání databáze.</span><span class="sxs-lookup"><span data-stu-id="6e57d-164">Using Azure table storage to store group and user information is similar to using a database.</span></span> <span data-ttu-id="6e57d-165">Následující příklad ukazuje entitu tabulky, která uloží uživatelské jméno a název skupiny.</span><span class="sxs-lookup"><span data-stu-id="6e57d-165">The following example shows a table entity that stores the user name and group name.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample8.cs)]

<span data-ttu-id="6e57d-166">V centru načtete přiřazených skupin, když se uživatel připojí.</span><span class="sxs-lookup"><span data-stu-id="6e57d-166">In the hub, you retrieve the assigned groups when the user connects.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample9.cs)]

<a id="verify"></a>

## <a name="verifying-group-membership-when-reconnecting"></a><span data-ttu-id="6e57d-167">Ověřování členství ve skupině při obnovování spojení</span><span class="sxs-lookup"><span data-stu-id="6e57d-167">Verifying group membership when reconnecting</span></span>

<span data-ttu-id="6e57d-168">Ve výchozím nastavení SignalR automaticky znovu přiřadí uživatele do příslušných skupin při obnovování spojení z dočasné přerušení, jako je například pokud je připojení vyřadit a znovu vytvořit předtím, než vyprší časový limit připojení. Informace o skupině pro daného uživatele je předán do tokenu při opětovné připojení, a tento token ověření na serveru.</span><span class="sxs-lookup"><span data-stu-id="6e57d-168">By default, SignalR automatically re-assigns a user to the appropriate groups when reconnecting from a temporary disruption, such as when a connection is dropped and re-established before the connection times out. The user's group information is passed in a token when reconnecting, and that token is verified on the server.</span></span> <span data-ttu-id="6e57d-169">Informace o procesu ověření u některých uživatelů do skupin najdete v tématu [některých skupin při obnovování spojení](../security/introduction-to-security.md#rejoingroup).</span><span class="sxs-lookup"><span data-stu-id="6e57d-169">For information about the verification process for rejoining users to groups, see [Rejoining groups when reconnecting](../security/introduction-to-security.md#rejoingroup).</span></span>

<span data-ttu-id="6e57d-170">Obecně platí abyste používali výchozí chování automaticky opětovné připojování ke rozbočovačů, že skupiny na znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="6e57d-170">In general, you should use the default behavior of automatically rejoining groups on reconnect.</span></span> <span data-ttu-id="6e57d-171">Funkce SignalR skupiny nejsou určeny jako vhodný mechanismus zabezpečení pro omezení přístupu k citlivým datům.</span><span class="sxs-lookup"><span data-stu-id="6e57d-171">SignalR groups are not intended as a security mechanism for restricting access to sensitive data.</span></span> <span data-ttu-id="6e57d-172">Pokud aplikace musí zkontrolovat členství ve skupině uživatele při obnovování spojení, však můžete přepsat výchozí chování.</span><span class="sxs-lookup"><span data-stu-id="6e57d-172">However, if your application must double-check a user's group membership when reconnecting, you can override the default behavior.</span></span> <span data-ttu-id="6e57d-173">Změna výchozího chování lze přidat zatížení do databáze protože členství ve skupině uživatele musí být načten pro každý opětovné připojení, a nikoli jen když se uživatel připojí.</span><span class="sxs-lookup"><span data-stu-id="6e57d-173">Changing the default behavior can add a burden to your database because a user's group membership must be retrieved for each reconnection rather than just when the user connects.</span></span>

<span data-ttu-id="6e57d-174">Pokud je nutné ověřit členství ve skupině na znovu připojit, vytvořte nový modul kanálu rozbočovače, který vrátí seznam přiřazených skupin, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="6e57d-174">If you must verify group membership on reconnect, create a new hub pipeline module that returns a list of assigned groups, as shown below.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample10.cs)]

<span data-ttu-id="6e57d-175">Poté přidejte modul do kanálu rozbočovače, jak je zdůrazněno níže.</span><span class="sxs-lookup"><span data-stu-id="6e57d-175">Then, add that module to the hub pipeline, as highlighted below.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample11.cs?highlight=4)]
