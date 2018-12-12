---
uid: signalr/overview/older-versions/persistent-connection-authorization
title: Ověřování a autorizace trvalých připojení SignalR (SignalR 1.x) | Dokumentace Microsoftu
author: pfletcher
description: Toto téma popisuje, jak vynutit autorizaci na trvalé připojení. Obecné informace o integraci zabezpečení do aplikace SignalR...
ms.author: riande
ms.date: 10/21/2013
ms.assetid: c34bc627-41af-4c21-a817-e97a19a7f252
msc.legacyurl: /signalr/overview/older-versions/persistent-connection-authorization
msc.type: authoredcontent
ms.openlocfilehash: 4973cb9bd03088106fe9502e0e706c6c28d2d46d
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53286854"
---
<a name="authentication-and-authorization-for-signalr-persistent-connections-signalr-1x"></a><span data-ttu-id="e1fcd-104">Ověřování a autorizace trvalých připojení SignalR (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="e1fcd-104">Authentication and Authorization for SignalR Persistent Connections (SignalR 1.x)</span></span>
====================
<span data-ttu-id="e1fcd-105">podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="e1fcd-105">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="e1fcd-106">Toto téma popisuje, jak vynutit autorizaci na trvalé připojení.</span><span class="sxs-lookup"><span data-stu-id="e1fcd-106">This topic describes how to enforce authorization on a persistent connection.</span></span> <span data-ttu-id="e1fcd-107">Obecné informace o integraci zabezpečení do aplikace funkci SignalR naleznete v tématu [Úvod do zabezpečení](index.md).</span><span class="sxs-lookup"><span data-stu-id="e1fcd-107">For general information about integrating security into a SignalR application, see [Introduction to Security](index.md).</span></span>


## <a name="enforce-authorization"></a><span data-ttu-id="e1fcd-108">Vynutit autorizaci</span><span class="sxs-lookup"><span data-stu-id="e1fcd-108">Enforce authorization</span></span>

<span data-ttu-id="e1fcd-109">K vynucení pravidel autorizace, při použití [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) je nutné přepsat `AuthorizeRequest` metody.</span><span class="sxs-lookup"><span data-stu-id="e1fcd-109">To enforce authorization rules when using a [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) you must override the `AuthorizeRequest` method.</span></span> <span data-ttu-id="e1fcd-110">Nelze použít `Authorize` atribut s trvalé připojení.</span><span class="sxs-lookup"><span data-stu-id="e1fcd-110">You cannot use the `Authorize` attribute with persistent connections.</span></span> <span data-ttu-id="e1fcd-111">`AuthorizeRequest` Metoda je volána rozhraním SignalR před každým požadavkem k ověření, že je uživatel oprávnění k provedení požadované akce.</span><span class="sxs-lookup"><span data-stu-id="e1fcd-111">The `AuthorizeRequest` method is called by the SignalR Framework before every request to verify that the user is authorized to perform the requested action.</span></span> <span data-ttu-id="e1fcd-112">`AuthorizeRequest` Metoda není volána z klienta; místo toho ověření uživatele přes standardní ověřovací mechanismus vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="e1fcd-112">The `AuthorizeRequest` method is not called from the client; instead, you authenticate the user through your application's standard authentication mechanism.</span></span>

<span data-ttu-id="e1fcd-113">Následující příklad ukazuje, jak omezit požadavky na ověření uživatelé.</span><span class="sxs-lookup"><span data-stu-id="e1fcd-113">The example below shows how to limit requests to authenticated users.</span></span>

[!code-csharp[Main](persistent-connection-authorization/samples/sample1.cs)]

<span data-ttu-id="e1fcd-114">Můžete přidat libovolný vlastní autorizace logiku v metodě AuthorizeRequest; například kontroluje, zda uživatel patří do určité role.</span><span class="sxs-lookup"><span data-stu-id="e1fcd-114">You can add any customized authorization logic in the AuthorizeRequest method; such as, checking whether a user belongs to a particular role.</span></span>
