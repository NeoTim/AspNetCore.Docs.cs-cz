---
uid: signalr/overview/guide-to-the-api/mapping-users-to-connections
title: Mapování uživatelů knihovny SignalR na připojení | Dokumentace Microsoftu
author: bradygaster
description: Toto téma ukazuje, jak uchovávat informace týkající se uživatelů a jejich připojení. Patrick Fletcher pomohla zápisu v tomto tématu. Verze softwaru použitým v tomto tématu...
ms.author: bradyg
ms.date: 12/30/2014
ms.assetid: f80c08b1-3f1f-432c-980c-c7b6edeb31b1
msc.legacyurl: /signalr/overview/guide-to-the-api/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: 786fc6bbc0b8d430770cf19d1647dbdba26347aa
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837192"
---
<a name="mapping-signalr-users-to-connections"></a><span data-ttu-id="bf77f-105">Mapování uživatelů knihovny SignalR na připojení</span><span class="sxs-lookup"><span data-stu-id="bf77f-105">Mapping SignalR Users to Connections</span></span>
====================
<span data-ttu-id="bf77f-106">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="bf77f-106">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="bf77f-107">Toto téma ukazuje, jak uchovávat informace týkající se uživatelů a jejich připojení.</span><span class="sxs-lookup"><span data-stu-id="bf77f-107">This topic shows how to retain information about users and their connections.</span></span>
>
> <span data-ttu-id="bf77f-108">Patrick Fletcher pomohla zápisu v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="bf77f-108">Patrick Fletcher helped write this topic.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="bf77f-109">Verze softwaru použitým v tomto tématu</span><span class="sxs-lookup"><span data-stu-id="bf77f-109">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="bf77f-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="bf77f-110">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="bf77f-111">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="bf77f-111">.NET 4.5</span></span>
> - <span data-ttu-id="bf77f-112">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="bf77f-112">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="bf77f-113">Předchozích verzích tohoto tématu</span><span class="sxs-lookup"><span data-stu-id="bf77f-113">Previous versions of this topic</span></span>
>
> <span data-ttu-id="bf77f-114">Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="bf77f-114">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="bf77f-115">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="bf77f-115">Questions and comments</span></span>
>
> <span data-ttu-id="bf77f-116">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="bf77f-116">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="bf77f-117">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="bf77f-117">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="introduction"></a><span data-ttu-id="bf77f-118">Úvod</span><span class="sxs-lookup"><span data-stu-id="bf77f-118">Introduction</span></span>

<span data-ttu-id="bf77f-119">Každé připojení klienta k rozbočovači předá id jedinečné připojení. Můžete načíst tuto hodnotu v `Context.ConnectionId` vlastnost kontext rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="bf77f-119">Each client connecting to a hub passes a unique connection id. You can retrieve this value in the `Context.ConnectionId` property of the hub context.</span></span> <span data-ttu-id="bf77f-120">Pokud vaše aplikace potřebuje pro mapování uživatele pro id připojení a uložení mapování, můžete použít jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="bf77f-120">If your application needs to map a user to the connection id and persist that mapping, you can use one of the following:</span></span>

- [<span data-ttu-id="bf77f-121">Uživatelské ID zprostředkovatele (knihovnou SignalR 2)</span><span class="sxs-lookup"><span data-stu-id="bf77f-121">The User ID Provider (SignalR 2)</span></span>](#IUserIdProvider)
- <span data-ttu-id="bf77f-122">[Úložiště v paměti](#inmemory), jako je například slovník</span><span class="sxs-lookup"><span data-stu-id="bf77f-122">[In-memory storage](#inmemory), such as a dictionary</span></span>
- [<span data-ttu-id="bf77f-123">Skupiny SignalR pro každého uživatele</span><span class="sxs-lookup"><span data-stu-id="bf77f-123">SignalR group for each user</span></span>](#groups)
- <span data-ttu-id="bf77f-124">[Trvalé, externí úložiště](#database), jako jsou databázové tabulky nebo Azure table storage</span><span class="sxs-lookup"><span data-stu-id="bf77f-124">[Permanent, external storage](#database), such as a database table or Azure table storage</span></span>

<span data-ttu-id="bf77f-125">Každá z těchto implementacích se zobrazí v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="bf77f-125">Each of these implementations is shown in this topic.</span></span> <span data-ttu-id="bf77f-126">Můžete použít `OnConnected`, `OnDisconnected`, a `OnReconnected` metody `Hub` třídy ke sledování stavu připojení uživatele.</span><span class="sxs-lookup"><span data-stu-id="bf77f-126">You use the `OnConnected`, `OnDisconnected`, and `OnReconnected` methods of the `Hub` class to track the user connection status.</span></span>

<span data-ttu-id="bf77f-127">Nejlepším řešením pro vaše aplikace závisí na:</span><span class="sxs-lookup"><span data-stu-id="bf77f-127">The best approach for your application depends on:</span></span>

- <span data-ttu-id="bf77f-128">Počet webových serverů, který je hostitelem vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="bf77f-128">The number of web servers hosting your application.</span></span>
- <span data-ttu-id="bf77f-129">Určuje, zda je nutné získat seznam aktuálně připojených uživatelů.</span><span class="sxs-lookup"><span data-stu-id="bf77f-129">Whether you need to get a list of the currently connected users.</span></span>
- <span data-ttu-id="bf77f-130">Určuje, zda je potřeba uchovávat informace, skupiny a uživatele po restartování aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="bf77f-130">Whether you need to persist group and user information when the application or server restarts.</span></span>
- <span data-ttu-id="bf77f-131">Latence volání externí server určuje, zda je problém.</span><span class="sxs-lookup"><span data-stu-id="bf77f-131">Whether the latency of calling an external server is an issue.</span></span>

<span data-ttu-id="bf77f-132">Následující tabulka uvádí, jaký přístup funguje pro tyto aspekty.</span><span class="sxs-lookup"><span data-stu-id="bf77f-132">The following table shows which approach works for these considerations.</span></span>

|  | <span data-ttu-id="bf77f-133">Více než jeden server</span><span class="sxs-lookup"><span data-stu-id="bf77f-133">More than one server</span></span> | <span data-ttu-id="bf77f-134">Získat seznam aktuálně připojených uživatelů</span><span class="sxs-lookup"><span data-stu-id="bf77f-134">Get list of currently connected users</span></span> | <span data-ttu-id="bf77f-135">Uchovávání informací po restartování</span><span class="sxs-lookup"><span data-stu-id="bf77f-135">Persist information after restarts</span></span> | <span data-ttu-id="bf77f-136">Zajištění optimálního výkonu</span><span class="sxs-lookup"><span data-stu-id="bf77f-136">Optimal performance</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="bf77f-137">ID uživatele zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="bf77f-137">UserID Provider</span></span> | ![](mapping-users-to-connections/_static/image1.png) |  |  | ![](mapping-users-to-connections/_static/image2.png) |
| <span data-ttu-id="bf77f-138">V paměti</span><span class="sxs-lookup"><span data-stu-id="bf77f-138">In-memory</span></span> |  | ![](mapping-users-to-connections/_static/image3.png) |  | ![](mapping-users-to-connections/_static/image4.png) |
| <span data-ttu-id="bf77f-139">Skupiny jednoho uživatele</span><span class="sxs-lookup"><span data-stu-id="bf77f-139">Single-user groups</span></span> | ![](mapping-users-to-connections/_static/image5.png) |  |  | ![](mapping-users-to-connections/_static/image6.png) |
| <span data-ttu-id="bf77f-140">Trvalé, externí</span><span class="sxs-lookup"><span data-stu-id="bf77f-140">Permanent, external</span></span> | ![](mapping-users-to-connections/_static/image7.png) | ![](mapping-users-to-connections/_static/image8.png) | ![](mapping-users-to-connections/_static/image9.png) |  |

<a id="IUserIdProvider"></a>

## <a name="iuserid-provider"></a><span data-ttu-id="bf77f-141">IUserID poskytovatele</span><span class="sxs-lookup"><span data-stu-id="bf77f-141">IUserID provider</span></span>

<span data-ttu-id="bf77f-142">Tato funkce umožňuje uživatelům určit, co je ID uživatele podle IRequest přes nové rozhraní IUserIdProvider.</span><span class="sxs-lookup"><span data-stu-id="bf77f-142">This feature allows users to specify what the userId is based on an IRequest via a new interface IUserIdProvider.</span></span>

<span data-ttu-id="bf77f-143">**The IUserIdProvider**</span><span class="sxs-lookup"><span data-stu-id="bf77f-143">**The IUserIdProvider**</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

<span data-ttu-id="bf77f-144">Ve výchozím nastavení, bude implementace, která používá uživatele `IPrincipal.Identity.Name` jako uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="bf77f-144">By default, there will be an implementation that uses the user's `IPrincipal.Identity.Name` as the user name.</span></span> <span data-ttu-id="bf77f-145">Chcete-li toto nastavení změnit, zaregistrovat vaše implementace `IUserIdProvider` s globální hostitelem při spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="bf77f-145">To change this, register your implementation of `IUserIdProvider` with the global host when your application starts:</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<span data-ttu-id="bf77f-146">Z v rámci rozbočovač, budete mít k odesílání zpráv pro tyto uživatele prostřednictvím rozhraní API pro následující:</span><span class="sxs-lookup"><span data-stu-id="bf77f-146">From within a hub, you'll be able to send messages to these users via the following API:</span></span>

<span data-ttu-id="bf77f-147">**Odesílání zprávy pro konkrétního uživatele**</span><span class="sxs-lookup"><span data-stu-id="bf77f-147">**Sending a message to a specific user**</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs?highlight=5)]

<a id="inmemory"></a>

## <a name="in-memory-storage"></a><span data-ttu-id="bf77f-148">Úložiště v paměti</span><span class="sxs-lookup"><span data-stu-id="bf77f-148">In-memory storage</span></span>

<span data-ttu-id="bf77f-149">Následující příklady ukazují, jak uchovávat informace o připojení a uživatele ve slovníku, která je uložená v paměti.</span><span class="sxs-lookup"><span data-stu-id="bf77f-149">The following examples show how to retain connection and user information in a dictionary that is stored in memory.</span></span> <span data-ttu-id="bf77f-150">Používá slovníku `HashSet` pro uložení id připojení. Kdykoli uživatel může mít víc než jedno připojení k aplikaci SignalR.</span><span class="sxs-lookup"><span data-stu-id="bf77f-150">The dictionary uses a `HashSet` to store the connection id. At any time a user could have more than one connection to the SignalR application.</span></span> <span data-ttu-id="bf77f-151">Například uživatel, který je připojený prostřednictvím více zařízení nebo více než jedné karty prohlížeče by mít více než jeden id připojení.</span><span class="sxs-lookup"><span data-stu-id="bf77f-151">For example, a user who is connected through multiple devices or more than one browser tab would have more than one connection id.</span></span>

<span data-ttu-id="bf77f-152">Pokud aplikaci ukončí, dojde ke ztrátě všech informací, ale ho znovu naplní se uživatelé znovu zavést svoje připojení.</span><span class="sxs-lookup"><span data-stu-id="bf77f-152">If the application shuts down, all of the information is lost, but it will be re-populated as the users re-establish their connections.</span></span> <span data-ttu-id="bf77f-153">Úložiště v paměti nebude fungovat, pokud prostředí obsahuje více než jednom webovém serveru, protože každý server bude mít samostatnou sadu připojení.</span><span class="sxs-lookup"><span data-stu-id="bf77f-153">In-memory storage does not work if your environment includes more than one web server because each server would have a separate collection of connections.</span></span>

<span data-ttu-id="bf77f-154">První příklad ukazuje třídu, která spravuje mapování uživatelů na připojení.</span><span class="sxs-lookup"><span data-stu-id="bf77f-154">The first example shows a class that manages the mapping of users to connections.</span></span> <span data-ttu-id="bf77f-155">Klíč pro HashSet bude uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="bf77f-155">The key for the HashSet will be the user's name.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

<span data-ttu-id="bf77f-156">Další příklad ukazuje způsob použití třídy mapování připojení od rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="bf77f-156">The next example shows how to use the connection mapping class from a hub.</span></span> <span data-ttu-id="bf77f-157">Instance třídy jsou uložena v názvu proměnné `_connections`.</span><span class="sxs-lookup"><span data-stu-id="bf77f-157">The instance of the class is stored in a variable name `_connections`.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a><span data-ttu-id="bf77f-158">Skupiny jednoho uživatele</span><span class="sxs-lookup"><span data-stu-id="bf77f-158">Single-user groups</span></span>

<span data-ttu-id="bf77f-159">Můžete vytvořit skupiny pro každého uživatele a pak odešle zprávu do této skupiny, pokud chcete oslovit pouze tohoto uživatele.</span><span class="sxs-lookup"><span data-stu-id="bf77f-159">You can create a group for each user, and then send a message to that group when you want to reach only that user.</span></span> <span data-ttu-id="bf77f-160">Název každé skupiny je jméno uživatele.</span><span class="sxs-lookup"><span data-stu-id="bf77f-160">The name of each group is the name of the user.</span></span> <span data-ttu-id="bf77f-161">Pokud má uživatel více než jedno připojení, každé id připojení se přidá do skupiny uživatelů.</span><span class="sxs-lookup"><span data-stu-id="bf77f-161">If a user has more than one connection, each connection id is added to the user's group.</span></span>

<span data-ttu-id="bf77f-162">Neměli ručně uživatele odeberete ze skupiny po odpojení.</span><span class="sxs-lookup"><span data-stu-id="bf77f-162">You should not manually remove the user from the group when the user disconnects.</span></span> <span data-ttu-id="bf77f-163">Tato akce se provádí automaticky rozhraním SignalR.</span><span class="sxs-lookup"><span data-stu-id="bf77f-163">This action is automatically performed by the SignalR framework.</span></span>

<span data-ttu-id="bf77f-164">Následující příklad ukazuje, jak implementovat skupiny jednoho uživatele.</span><span class="sxs-lookup"><span data-stu-id="bf77f-164">The following example shows how to implement single-user groups.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a><span data-ttu-id="bf77f-165">Trvalé, externí úložiště</span><span class="sxs-lookup"><span data-stu-id="bf77f-165">Permanent, external storage</span></span>

<span data-ttu-id="bf77f-166">Toto téma ukazuje, jak používat databáze nebo úložiště tabulek v Azure k ukládání informací o připojení.</span><span class="sxs-lookup"><span data-stu-id="bf77f-166">This topic shows how to use either a database or Azure table storage for storing connection information.</span></span> <span data-ttu-id="bf77f-167">Tento přístup funguje, když máte více webových serverů, protože každý webový server může komunikovat s stejného úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="bf77f-167">This approach works when you have multiple web servers because each web server can interact with the same data repository.</span></span> <span data-ttu-id="bf77f-168">Pokud vaše webové servery zastavit pracovní nebo restartování aplikace `OnDisconnected` metoda není volána.</span><span class="sxs-lookup"><span data-stu-id="bf77f-168">If your web servers stop working or the application restarts, the `OnDisconnected` method is not called.</span></span> <span data-ttu-id="bf77f-169">Proto je možné, že úložiště dat bude obsahovat záznamy pro ID připojení, které už nejsou platné.</span><span class="sxs-lookup"><span data-stu-id="bf77f-169">Therefore, it is possible that your data repository will have records for connection ids that are no longer valid.</span></span> <span data-ttu-id="bf77f-170">Vyčistit osamocené záznamy, které staví na zneplatnit jakékoli připojení, který byl vytvořen mimo časový rámec, který je relevantní pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bf77f-170">To clean up these orphaned records, you may wish to invalidate any connection that was created outside of a timeframe that is relevant to your application.</span></span> <span data-ttu-id="bf77f-171">Příklady v této části zahrnout hodnotu pro sledování při vytváření připojení, ale nezobrazovat návod k vyčištění starých záznamů, protože můžete chtít udělat jako proces na pozadí.</span><span class="sxs-lookup"><span data-stu-id="bf77f-171">The examples in this section include a value for tracking when the connection was created, but do not show how to clean up old records because you may want to do that as background process.</span></span>

### <a name="database"></a><span data-ttu-id="bf77f-172">Databáze</span><span class="sxs-lookup"><span data-stu-id="bf77f-172">Database</span></span>

<span data-ttu-id="bf77f-173">Následující příklady ukazují, jak uchovávat informace o připojení a uživatele v databázi.</span><span class="sxs-lookup"><span data-stu-id="bf77f-173">The following examples show how to retain connection and user information in a database.</span></span> <span data-ttu-id="bf77f-174">Můžete použít libovolný technologií přístupu dat; Následující příklad ukazuje, jak definovat modely s využitím rozhraní Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="bf77f-174">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="bf77f-175">Tyto modely entity odpovídají databázové tabulky a pole.</span><span class="sxs-lookup"><span data-stu-id="bf77f-175">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="bf77f-176">Datová struktura se může značně lišit v závislosti na požadavcích vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="bf77f-176">Your data structure could vary considerably depending on the requirements of your application.</span></span>

<span data-ttu-id="bf77f-177">První příklad ukazuje, jak lze definovat entitu uživatele, která můžou být spojené s mnoha entit připojení.</span><span class="sxs-lookup"><span data-stu-id="bf77f-177">The first example shows how to define a user entity that can be associated with many connection entities.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]

<span data-ttu-id="bf77f-178">Z centra, pak můžete sledovat stav každé připojení kódem zobrazeným pod.</span><span class="sxs-lookup"><span data-stu-id="bf77f-178">Then, from the hub, you can track the state of each connection with the code shown below.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample8.cs)]

<a id="azure"></a>
### <a name="azure-table-storage"></a><span data-ttu-id="bf77f-179">Úložiště tabulek v Azure</span><span class="sxs-lookup"><span data-stu-id="bf77f-179">Azure table storage</span></span>

<span data-ttu-id="bf77f-180">Následující příklad úložiště tabulek v Azure je podobně jako v příkladu databáze.</span><span class="sxs-lookup"><span data-stu-id="bf77f-180">The following Azure table storage example is similar to the database example.</span></span> <span data-ttu-id="bf77f-181">Nezahrnuje všechny informace, které je třeba začít pomocí služby Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="bf77f-181">It does not include all of the information that you would need to get started with Azure Table Storage Service.</span></span> <span data-ttu-id="bf77f-182">Informace najdete v tématu [postupy používání úložiště Table z .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span><span class="sxs-lookup"><span data-stu-id="bf77f-182">For information, see [How to use Table storage from .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span></span>

<span data-ttu-id="bf77f-183">Následující příklad ukazuje entitu tabulky pro ukládání informací o připojení.</span><span class="sxs-lookup"><span data-stu-id="bf77f-183">The following example shows a table entity for storing connection information.</span></span> <span data-ttu-id="bf77f-184">Rozdělují data podle uživatelského jména a identifikuje každé entity podle id připojení, takže uživatel může mít víc připojení v každém okamžiku.</span><span class="sxs-lookup"><span data-stu-id="bf77f-184">It partitions the data by user name, and identifies each entity by the connection id, so a user can have multiple connections at any time.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample9.cs)]

<span data-ttu-id="bf77f-185">V centru sledovat stav připojení jednotlivých uživatelů.</span><span class="sxs-lookup"><span data-stu-id="bf77f-185">In the hub, you track the status of each user's connection.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample10.cs)]
