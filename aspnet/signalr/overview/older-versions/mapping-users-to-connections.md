---
uid: signalr/overview/older-versions/mapping-users-to-connections
title: Mapování uživatelů knihovny SignalR na připojení v SignalR 1.x | Dokumentace Microsoftu
author: bradygaster
description: Toto téma ukazuje, jak uchovávat informace týkající se uživatelů a jejich připojení.
ms.author: bradyg
ms.date: 10/17/2013
ms.assetid: ebbc93a8-e6c4-4122-8e0d-3aa42293c747
msc.legacyurl: /signalr/overview/older-versions/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: 1e65a11e08a5b060cf8b096b5fe5b90eb8dc5b51
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836711"
---
<a name="mapping-signalr-users-to-connections-in-signalr-1x"></a><span data-ttu-id="cf512-103">Mapování uživatelů knihovny SignalR na připojení v SignalR 1.x</span><span class="sxs-lookup"><span data-stu-id="cf512-103">Mapping SignalR Users to Connections in SignalR 1.x</span></span>
====================
<span data-ttu-id="cf512-104">podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="cf512-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="cf512-105">Toto téma ukazuje, jak uchovávat informace týkající se uživatelů a jejich připojení.</span><span class="sxs-lookup"><span data-stu-id="cf512-105">This topic shows how to retain information about users and their connections.</span></span>


## <a name="introduction"></a><span data-ttu-id="cf512-106">Úvod</span><span class="sxs-lookup"><span data-stu-id="cf512-106">Introduction</span></span>

<span data-ttu-id="cf512-107">Každé připojení klienta k rozbočovači předá id jedinečné připojení. Můžete načíst tuto hodnotu v `Context.ConnectionId` vlastnost kontext rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="cf512-107">Each client connecting to a hub passes a unique connection id. You can retrieve this value in the `Context.ConnectionId` property of the hub context.</span></span> <span data-ttu-id="cf512-108">Pokud vaše aplikace potřebuje pro mapování uživatele pro id připojení a uložení mapování, můžete použít jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="cf512-108">If your application needs to map a user to the connection id and persist that mapping, you can use one of the following:</span></span>

- <span data-ttu-id="cf512-109">[Úložiště v paměti](#inmemory), jako je například slovník</span><span class="sxs-lookup"><span data-stu-id="cf512-109">[In-memory storage](#inmemory), such as a dictionary</span></span>
- [<span data-ttu-id="cf512-110">Skupiny SignalR pro každého uživatele</span><span class="sxs-lookup"><span data-stu-id="cf512-110">SignalR group for each user</span></span>](#groups)
- <span data-ttu-id="cf512-111">[Trvalé, externí úložiště](#database), jako jsou databázové tabulky nebo Azure table storage</span><span class="sxs-lookup"><span data-stu-id="cf512-111">[Permanent, external storage](#database), such as a database table or Azure table storage</span></span>

<span data-ttu-id="cf512-112">Každá z těchto implementacích se zobrazí v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="cf512-112">Each of these implementations is shown in this topic.</span></span> <span data-ttu-id="cf512-113">Můžete použít `OnConnected`, `OnDisconnected`, a `OnReconnected` metody `Hub` třídy ke sledování stavu připojení uživatele.</span><span class="sxs-lookup"><span data-stu-id="cf512-113">You use the `OnConnected`, `OnDisconnected`, and `OnReconnected` methods of the `Hub` class to track the user connection status.</span></span>

<span data-ttu-id="cf512-114">Nejlepším řešením pro vaše aplikace závisí na:</span><span class="sxs-lookup"><span data-stu-id="cf512-114">The best approach for your application depends on:</span></span>

- <span data-ttu-id="cf512-115">Počet webových serverů, který je hostitelem vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="cf512-115">The number of web servers hosting your application.</span></span>
- <span data-ttu-id="cf512-116">Určuje, zda je nutné získat seznam aktuálně připojených uživatelů.</span><span class="sxs-lookup"><span data-stu-id="cf512-116">Whether you need to get a list of the currently connected users.</span></span>
- <span data-ttu-id="cf512-117">Určuje, zda je potřeba uchovávat informace, skupiny a uživatele po restartování aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="cf512-117">Whether you need to persist group and user information when the application or server restarts.</span></span>
- <span data-ttu-id="cf512-118">Latence volání externí server určuje, zda je problém.</span><span class="sxs-lookup"><span data-stu-id="cf512-118">Whether the latency of calling an external server is an issue.</span></span>

<span data-ttu-id="cf512-119">Následující tabulka uvádí, jaký přístup funguje pro tyto aspekty.</span><span class="sxs-lookup"><span data-stu-id="cf512-119">The following table shows which approach works for these considerations.</span></span>

|  | <span data-ttu-id="cf512-120">Více než jeden server</span><span class="sxs-lookup"><span data-stu-id="cf512-120">More than one server</span></span> | <span data-ttu-id="cf512-121">Získat seznam aktuálně připojených uživatelů</span><span class="sxs-lookup"><span data-stu-id="cf512-121">Get list of currently connected users</span></span> | <span data-ttu-id="cf512-122">Uchovávání informací po restartování</span><span class="sxs-lookup"><span data-stu-id="cf512-122">Persist information after restarts</span></span> | <span data-ttu-id="cf512-123">Zajištění optimálního výkonu</span><span class="sxs-lookup"><span data-stu-id="cf512-123">Optimal performance</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="cf512-124">V paměti</span><span class="sxs-lookup"><span data-stu-id="cf512-124">In-memory</span></span> |  | ![](mapping-users-to-connections/_static/image1.png) |  | ![](mapping-users-to-connections/_static/image2.png) |
| <span data-ttu-id="cf512-125">Skupiny jednoho uživatele</span><span class="sxs-lookup"><span data-stu-id="cf512-125">Single-user groups</span></span> | ![](mapping-users-to-connections/_static/image3.png) |  |  | ![](mapping-users-to-connections/_static/image4.png) |
| <span data-ttu-id="cf512-126">Trvalé, externí</span><span class="sxs-lookup"><span data-stu-id="cf512-126">Permanent, external</span></span> | ![](mapping-users-to-connections/_static/image5.png) | ![](mapping-users-to-connections/_static/image6.png) | ![](mapping-users-to-connections/_static/image7.png) |  |

<a id="inmemory"></a>

## <a name="in-memory-storage"></a><span data-ttu-id="cf512-127">Úložiště v paměti</span><span class="sxs-lookup"><span data-stu-id="cf512-127">In-memory storage</span></span>

<span data-ttu-id="cf512-128">Následující příklady ukazují, jak uchovávat informace o připojení a uživatele ve slovníku, která je uložená v paměti.</span><span class="sxs-lookup"><span data-stu-id="cf512-128">The following examples show how to retain connection and user information in a dictionary that is stored in memory.</span></span> <span data-ttu-id="cf512-129">Používá slovníku `HashSet` pro uložení id připojení. Kdykoli uživatel může mít víc než jedno připojení k aplikaci SignalR.</span><span class="sxs-lookup"><span data-stu-id="cf512-129">The dictionary uses a `HashSet` to store the connection id. At any time a user could have more than one connection to the SignalR application.</span></span> <span data-ttu-id="cf512-130">Například uživatel, který je připojený prostřednictvím více zařízení nebo více než jedné karty prohlížeče by mít více než jeden id připojení.</span><span class="sxs-lookup"><span data-stu-id="cf512-130">For example, a user who is connected through multiple devices or more than one browser tab would have more than one connection id.</span></span>

<span data-ttu-id="cf512-131">Pokud aplikaci ukončí, dojde ke ztrátě všech informací, ale ho znovu naplní se uživatelé znovu zavést svoje připojení.</span><span class="sxs-lookup"><span data-stu-id="cf512-131">If the application shuts down, all of the information is lost, but it will be re-populated as the users re-establish their connections.</span></span> <span data-ttu-id="cf512-132">Úložiště v paměti nebude fungovat, pokud prostředí obsahuje více než jednom webovém serveru, protože každý server bude mít samostatnou sadu připojení.</span><span class="sxs-lookup"><span data-stu-id="cf512-132">In-memory storage does not work if your environment includes more than one web server because each server would have a separate collection of connections.</span></span>

<span data-ttu-id="cf512-133">První příklad ukazuje třídu, která spravuje mapování uživatelů na připojení.</span><span class="sxs-lookup"><span data-stu-id="cf512-133">The first example shows a class that manages the mapping of users to connections.</span></span> <span data-ttu-id="cf512-134">Klíč pro HashSet bude uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="cf512-134">The key for the HashSet will be the user's name.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

<span data-ttu-id="cf512-135">Další příklad ukazuje způsob použití třídy mapování připojení od rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="cf512-135">The next example shows how to use the connection mapping class from a hub.</span></span> <span data-ttu-id="cf512-136">Instance třídy jsou uložena v názvu proměnné `_connections`.</span><span class="sxs-lookup"><span data-stu-id="cf512-136">The instance of the class is stored in a variable name `_connections`.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a><span data-ttu-id="cf512-137">Skupiny jednoho uživatele</span><span class="sxs-lookup"><span data-stu-id="cf512-137">Single-user groups</span></span>

<span data-ttu-id="cf512-138">Můžete vytvořit skupiny pro každého uživatele a pak odešle zprávu do této skupiny, pokud chcete oslovit pouze tohoto uživatele.</span><span class="sxs-lookup"><span data-stu-id="cf512-138">You can create a group for each user, and then send a message to that group when you want to reach only that user.</span></span> <span data-ttu-id="cf512-139">Název každé skupiny je jméno uživatele.</span><span class="sxs-lookup"><span data-stu-id="cf512-139">The name of each group is the name of the user.</span></span> <span data-ttu-id="cf512-140">Pokud má uživatel více než jedno připojení, každé id připojení se přidá do skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="cf512-140">If a user has more than one connection, each connection id is added to the user's group.</span></span>

<span data-ttu-id="cf512-141">Neměli ručně uživatele odeberete ze skupiny po odpojení.</span><span class="sxs-lookup"><span data-stu-id="cf512-141">You should not manually remove the user from the group when the user disconnects.</span></span> <span data-ttu-id="cf512-142">Tato akce se provádí automaticky rozhraním SignalR.</span><span class="sxs-lookup"><span data-stu-id="cf512-142">This action is automatically performed by the SignalR framework.</span></span>

<span data-ttu-id="cf512-143">Následující příklad ukazuje, jak implementovat skupiny jednoho uživatele.</span><span class="sxs-lookup"><span data-stu-id="cf512-143">The following example shows how to implement single-user groups.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a><span data-ttu-id="cf512-144">Trvalé, externí úložiště</span><span class="sxs-lookup"><span data-stu-id="cf512-144">Permanent, external storage</span></span>

<span data-ttu-id="cf512-145">Toto téma ukazuje, jak používat databáze nebo úložiště tabulek v Azure k ukládání informací o připojení.</span><span class="sxs-lookup"><span data-stu-id="cf512-145">This topic shows how to use either a database or Azure table storage for storing connection information.</span></span> <span data-ttu-id="cf512-146">Tento přístup funguje, když máte více webových serverů, protože každý webový server může komunikovat s stejného úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="cf512-146">This approach works when you have multiple web servers because each web server can interact with the same data repository.</span></span> <span data-ttu-id="cf512-147">Pokud vaše webové servery zastavit pracovní nebo restartování aplikace `OnDisconnected` metoda není volána.</span><span class="sxs-lookup"><span data-stu-id="cf512-147">If your web servers stop working or the application restarts, the `OnDisconnected` method is not called.</span></span> <span data-ttu-id="cf512-148">Proto je možné, že úložiště dat bude obsahovat záznamy pro ID připojení, které už nejsou platné.</span><span class="sxs-lookup"><span data-stu-id="cf512-148">Therefore, it is possible that your data repository will have records for connection ids that are no longer valid.</span></span> <span data-ttu-id="cf512-149">Vyčistit osamocené záznamy, které staví na zneplatnit jakékoli připojení, který byl vytvořen mimo časový rámec, který je relevantní pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cf512-149">To clean up these orphaned records, you may wish to invalidate any connection that was created outside of a timeframe that is relevant to your application.</span></span> <span data-ttu-id="cf512-150">Příklady v této části zahrnout hodnotu pro sledování při vytváření připojení, ale nezobrazovat návod k vyčištění starých záznamů, protože můžete chtít udělat jako proces na pozadí.</span><span class="sxs-lookup"><span data-stu-id="cf512-150">The examples in this section include a value for tracking when the connection was created, but do not show how to clean up old records because you may want to do that as background process.</span></span>

### <a name="database"></a><span data-ttu-id="cf512-151">Databáze</span><span class="sxs-lookup"><span data-stu-id="cf512-151">Database</span></span>

<span data-ttu-id="cf512-152">Následující příklady ukazují, jak uchovávat informace o připojení a uživatele v databázi.</span><span class="sxs-lookup"><span data-stu-id="cf512-152">The following examples show how to retain connection and user information in a database.</span></span> <span data-ttu-id="cf512-153">Můžete použít libovolný technologií přístupu dat; Následující příklad ukazuje, jak definovat modely s využitím rozhraní Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="cf512-153">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="cf512-154">Tyto modely entity odpovídají databázové tabulky a pole.</span><span class="sxs-lookup"><span data-stu-id="cf512-154">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="cf512-155">Datová struktura se může značně lišit v závislosti na požadavcích vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="cf512-155">Your data structure could vary considerably depending on the requirements of your application.</span></span>

<span data-ttu-id="cf512-156">První příklad ukazuje, jak lze definovat entitu uživatele, která můžou být spojené s mnoha entit připojení.</span><span class="sxs-lookup"><span data-stu-id="cf512-156">The first example shows how to define a user entity that can be associated with many connection entities.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

<span data-ttu-id="cf512-157">Z centra, pak můžete sledovat stav každé připojení kódem zobrazeným pod.</span><span class="sxs-lookup"><span data-stu-id="cf512-157">Then, from the hub, you can track the state of each connection with the code shown below.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

### <a name="azure-table-storage"></a><span data-ttu-id="cf512-158">Úložiště tabulek v Azure</span><span class="sxs-lookup"><span data-stu-id="cf512-158">Azure table storage</span></span>

<span data-ttu-id="cf512-159">Následující příklad úložiště tabulek v Azure je podobně jako v příkladu databáze.</span><span class="sxs-lookup"><span data-stu-id="cf512-159">The following Azure table storage example is similar to the database example.</span></span> <span data-ttu-id="cf512-160">Nezahrnuje všechny informace, které je třeba začít pomocí služby Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="cf512-160">It does not include all of the information that you would need to get started with Azure Table Storage Service.</span></span> <span data-ttu-id="cf512-161">Informace najdete v tématu [postupy používání úložiště Table z .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span><span class="sxs-lookup"><span data-stu-id="cf512-161">For information, see [How to use Table storage from .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span></span>

<span data-ttu-id="cf512-162">Následující příklad ukazuje entitu tabulky pro ukládání informací o připojení.</span><span class="sxs-lookup"><span data-stu-id="cf512-162">The following example shows a table entity for storing connection information.</span></span> <span data-ttu-id="cf512-163">Rozdělují data podle uživatelského jména a identifikuje každé entity podle id připojení, takže uživatel může mít víc připojení v každém okamžiku.</span><span class="sxs-lookup"><span data-stu-id="cf512-163">It partitions the data by user name, and identifies each entity by the connection id, so a user can have multiple connections at any time.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<span data-ttu-id="cf512-164">V centru sledovat stav připojení jednotlivých uživatelů.</span><span class="sxs-lookup"><span data-stu-id="cf512-164">In the hub, you track the status of each user's connection.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]
