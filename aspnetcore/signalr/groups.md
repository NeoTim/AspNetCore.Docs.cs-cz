---
title: Správa uživatelů a skupin v knihovně SignalR
author: bradygaster
description: Přehled ASP.NET Core SignalR uživatelů a skupin správy.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/groups
ms.openlocfilehash: 45f2bb44e03a586b7fc186525fdd3a2645c820d5
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667749"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="91f53-103">Správa uživatelů a skupin v knihovně SignalR</span><span class="sxs-lookup"><span data-stu-id="91f53-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="91f53-104">Podle [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="91f53-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="91f53-105">Funkce SignalR umožňuje zprávy k odeslání do všech připojení, které jsou spojené s konkrétním uživatelem, jakož i pojmenovaným skupinám připojení.</span><span class="sxs-lookup"><span data-stu-id="91f53-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="91f53-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="91f53-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="91f53-107">Uživatelé v knihovně SignalR</span><span class="sxs-lookup"><span data-stu-id="91f53-107">Users in SignalR</span></span>

<span data-ttu-id="91f53-108">Funkce SignalR umožňuje odesílání zpráv do všech připojení, které jsou spojené s konkrétním uživatelem.</span><span class="sxs-lookup"><span data-stu-id="91f53-108">SignalR allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="91f53-109">Ve výchozím nastavení, používá SignalR `ClaimTypes.NameIdentifier` z `ClaimsPrincipal` přidružené k připojení jako identifikátor uživatele.</span><span class="sxs-lookup"><span data-stu-id="91f53-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="91f53-110">Jeden uživatel může mít více připojení k aplikaci s knihovnou SignalR.</span><span class="sxs-lookup"><span data-stu-id="91f53-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="91f53-111">Například může být připojen uživatel na svém počítači, stejně jako svůj telefon.</span><span class="sxs-lookup"><span data-stu-id="91f53-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="91f53-112">Každé zařízení má samostatného připojení SignalR, ale jsou všechny související se stejným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="91f53-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="91f53-113">Pokud je uživateli odeslána zpráva, všechna připojení přidružená k tomuto uživateli zobrazí zpráva.</span><span class="sxs-lookup"><span data-stu-id="91f53-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="91f53-114">Identifikátor uživatele pro připojení je přístupný `Context.UserIdentifier` vlastnosti v centru.</span><span class="sxs-lookup"><span data-stu-id="91f53-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="91f53-115">Odeslání zprávy do konkrétního uživatele tím, že předáte identifikátor uživatele, který `User` fungovat ve své metodě rozbočovače, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="91f53-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="91f53-116">Identifikátor uživatele je velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="91f53-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="91f53-117">Skupinami v knihovně SignalR</span><span class="sxs-lookup"><span data-stu-id="91f53-117">Groups in SignalR</span></span>

<span data-ttu-id="91f53-118">Skupina je kolekce připojení přidružená k názvu.</span><span class="sxs-lookup"><span data-stu-id="91f53-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="91f53-119">Pro všechna připojení ve skupině nelze odesílat zprávy.</span><span class="sxs-lookup"><span data-stu-id="91f53-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="91f53-120">Skupiny jsou doporučeným způsobem, jak odesílat připojení nebo více připojení, protože tyto skupiny jsou spravována aplikací.</span><span class="sxs-lookup"><span data-stu-id="91f53-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="91f53-121">Připojení může být členem více skupin.</span><span class="sxs-lookup"><span data-stu-id="91f53-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="91f53-122">Díky tomu skupiny ideální pro něco jako je chatovací aplikaci, kde každý prostor může být reprezentována jako skupinu.</span><span class="sxs-lookup"><span data-stu-id="91f53-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="91f53-123">Připojení může být přidán či odebrán ze skupiny přes `AddToGroupAsync` a `RemoveFromGroupAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="91f53-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="91f53-124">Členství ve skupině se nezachová při opětovném navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="91f53-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="91f53-125">Připojení je potřeba znovu vstoupit skupině znovu zřízeno.</span><span class="sxs-lookup"><span data-stu-id="91f53-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="91f53-126">Není možné počet členů skupiny, protože tyto informace není k dispozici v případě, že aplikace je škálovaný na více serverů.</span><span class="sxs-lookup"><span data-stu-id="91f53-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="91f53-127">Chcete-li chránit přístup k prostředkům a používání skupin, použijte [ověřování a autorizace](xref:signalr/authn-and-authz) funkce v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91f53-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="91f53-128">Pokud pouze přidáte uživatele do skupiny při přihlašovací údaje jsou platné pro tuto skupinu, zprávy odeslané do této skupiny přejdete jenom na autorizované uživatele.</span><span class="sxs-lookup"><span data-stu-id="91f53-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="91f53-129">Skupiny však nejsou funkce zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="91f53-129">However, groups are not a security feature.</span></span> <span data-ttu-id="91f53-130">Ověřování deklarací identity má funkce, které skupiny tomu tak není, jako je například vypršení platnosti a odvolání.</span><span class="sxs-lookup"><span data-stu-id="91f53-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="91f53-131">Pokud se odvolat oprávnění uživatele pro přístup ke skupině, budete muset ručně zjišťovat a odebrat ze skupiny.</span><span class="sxs-lookup"><span data-stu-id="91f53-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="91f53-132">Názvy skupin rozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="91f53-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="91f53-133">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="91f53-133">Related resources</span></span>

* [<span data-ttu-id="91f53-134">Začínáme</span><span class="sxs-lookup"><span data-stu-id="91f53-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="91f53-135">Centra</span><span class="sxs-lookup"><span data-stu-id="91f53-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="91f53-136">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="91f53-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
