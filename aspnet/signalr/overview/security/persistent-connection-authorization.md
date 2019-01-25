---
uid: signalr/overview/security/persistent-connection-authorization
title: Ověřování a autorizace trvalých připojení SignalR | Dokumentace Microsoftu
author: bradygaster
description: Toto téma popisuje, jak vynutit autorizaci na trvalé připojení. Obecné informace o integraci zabezpečení do aplikace SignalR...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: e264677b-9c01-47ec-94f9-3cd8f08f94af
msc.legacyurl: /signalr/overview/security/persistent-connection-authorization
msc.type: authoredcontent
ms.openlocfilehash: 7dab28f4720b34082f71e487c64af88a8ba01e6c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835619"
---
<a name="authentication-and-authorization-for-signalr-persistent-connections"></a><span data-ttu-id="7e724-104">Ověřování a autorizace trvalých připojení SignalR</span><span class="sxs-lookup"><span data-stu-id="7e724-104">Authentication and Authorization for SignalR Persistent Connections</span></span>
====================
<span data-ttu-id="7e724-105">podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="7e724-105">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="7e724-106">Toto téma popisuje, jak vynutit autorizaci na trvalé připojení.</span><span class="sxs-lookup"><span data-stu-id="7e724-106">This topic describes how to enforce authorization on a persistent connection.</span></span> <span data-ttu-id="7e724-107">Obecné informace o integraci zabezpečení do aplikace funkci SignalR naleznete v tématu [Úvod do zabezpečení](introduction-to-security.md).</span><span class="sxs-lookup"><span data-stu-id="7e724-107">For general information about integrating security into a SignalR application, see [Introduction to Security](introduction-to-security.md).</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="7e724-108">Verze softwaru použitým v tomto tématu</span><span class="sxs-lookup"><span data-stu-id="7e724-108">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="7e724-109">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="7e724-109">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="7e724-110">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="7e724-110">.NET 4.5</span></span>
> - <span data-ttu-id="7e724-111">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="7e724-111">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="7e724-112">Předchozích verzích tohoto tématu</span><span class="sxs-lookup"><span data-stu-id="7e724-112">Previous versions of this topic</span></span>
>
> <span data-ttu-id="7e724-113">Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="7e724-113">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="7e724-114">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="7e724-114">Questions and comments</span></span>
>
> <span data-ttu-id="7e724-115">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="7e724-115">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="7e724-116">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="7e724-116">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="enforce-authorization"></a><span data-ttu-id="7e724-117">Vynutit autorizaci</span><span class="sxs-lookup"><span data-stu-id="7e724-117">Enforce authorization</span></span>

<span data-ttu-id="7e724-118">K vynucení pravidel autorizace, při použití [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) je nutné přepsat `AuthorizeRequest` metody.</span><span class="sxs-lookup"><span data-stu-id="7e724-118">To enforce authorization rules when using a [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) you must override the `AuthorizeRequest` method.</span></span> <span data-ttu-id="7e724-119">Nelze použít `Authorize` atribut s trvalé připojení.</span><span class="sxs-lookup"><span data-stu-id="7e724-119">You cannot use the `Authorize` attribute with persistent connections.</span></span> <span data-ttu-id="7e724-120">`AuthorizeRequest` Metoda je volána rozhraním SignalR před každým požadavkem k ověření, že je uživatel oprávnění k provedení požadované akce.</span><span class="sxs-lookup"><span data-stu-id="7e724-120">The `AuthorizeRequest` method is called by the SignalR Framework before every request to verify that the user is authorized to perform the requested action.</span></span> <span data-ttu-id="7e724-121">`AuthorizeRequest` Metoda není volána z klienta; místo toho ověření uživatele přes standardní ověřovací mechanismus vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e724-121">The `AuthorizeRequest` method is not called from the client; instead, you authenticate the user through your application's standard authentication mechanism.</span></span>

<span data-ttu-id="7e724-122">Následující příklad ukazuje, jak omezit požadavky na ověření uživatelé.</span><span class="sxs-lookup"><span data-stu-id="7e724-122">The example below shows how to limit requests to authenticated users.</span></span>

[!code-csharp[Main](persistent-connection-authorization/samples/sample1.cs)]

<span data-ttu-id="7e724-123">Můžete přidat libovolný vlastní autorizace logiku v metodě AuthorizeRequest; například kontroluje, zda uživatel patří do určité role.</span><span class="sxs-lookup"><span data-stu-id="7e724-123">You can add any customized authorization logic in the AuthorizeRequest method; such as, checking whether a user belongs to a particular role.</span></span>
