---
title: Správa uživatelů a skupin v nástrojiSignalR
author: bradygaster
description: Přehled ASP.NET Core SignalR správy uživatelů a skupin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af498575899fcfa407aba9f9f49c0bfeabadc7a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776295"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="3310f-103">Správa uživatelů a skupin v nástrojiSignalR</span><span class="sxs-lookup"><span data-stu-id="3310f-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="3310f-104">Od [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="3310f-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="3310f-105">umožňuje posílat zprávy na všechna připojení přidružená ke konkrétnímu uživateli i k pojmenovaným skupinám připojení.</span><span class="sxs-lookup"><span data-stu-id="3310f-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="3310f-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="3310f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="3310f-107">Uživatelé vSignalR</span><span class="sxs-lookup"><span data-stu-id="3310f-107">Users in SignalR</span></span>

SignalR<span data-ttu-id="3310f-108">umožňuje odesílat zprávy na všechna připojení přidružená ke konkrétnímu uživateli.</span><span class="sxs-lookup"><span data-stu-id="3310f-108"> allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="3310f-109">Ve výchozím nastavení SignalR používá nástroj `ClaimTypes.NameIdentifier` z `ClaimsPrincipal` přidruženého k připojení jako identifikátor uživatele.</span><span class="sxs-lookup"><span data-stu-id="3310f-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="3310f-110">Jeden uživatel může mít několik připojení k SignalR aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3310f-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="3310f-111">Například uživatel může být připojen na svém počítači a také na telefonu.</span><span class="sxs-lookup"><span data-stu-id="3310f-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="3310f-112">Každé zařízení má samostatné SignalR připojení, ale všechny jsou přidružené ke stejnému uživateli.</span><span class="sxs-lookup"><span data-stu-id="3310f-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="3310f-113">Pokud se uživateli pošle zpráva, zobrazí se tato zpráva u všech připojení přidružených k tomuto uživateli.</span><span class="sxs-lookup"><span data-stu-id="3310f-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="3310f-114">K identifikátoru uživatele pro připojení se dá použít `Context.UserIdentifier` vlastnost ve vašem centru.</span><span class="sxs-lookup"><span data-stu-id="3310f-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="3310f-115">Odeslat zprávu konkrétnímu uživateli předáním identifikátoru uživatele do `User` funkce v metodě centra, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3310f-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="3310f-116">Identifikátor uživatele rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="3310f-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="3310f-117">Skupiny vSignalR</span><span class="sxs-lookup"><span data-stu-id="3310f-117">Groups in SignalR</span></span>

<span data-ttu-id="3310f-118">Skupina je kolekcí připojení přidružených k názvu.</span><span class="sxs-lookup"><span data-stu-id="3310f-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="3310f-119">Zprávy je možné odesílat do všech připojení ve skupině.</span><span class="sxs-lookup"><span data-stu-id="3310f-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="3310f-120">Skupiny jsou doporučeným způsobem, jak odeslat připojení nebo více připojení, protože skupiny jsou spravovány aplikací.</span><span class="sxs-lookup"><span data-stu-id="3310f-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="3310f-121">Připojení může být členem více skupin.</span><span class="sxs-lookup"><span data-stu-id="3310f-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="3310f-122">Díky tomu jsou skupiny ideální pro něco podobného aplikaci Chat, kde každou místnost lze reprezentovat jako skupinu.</span><span class="sxs-lookup"><span data-stu-id="3310f-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="3310f-123">Připojení můžete přidat nebo odebrat ze skupin prostřednictvím metod `AddToGroupAsync` a. `RemoveFromGroupAsync`</span><span class="sxs-lookup"><span data-stu-id="3310f-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="3310f-124">Členství ve skupině se nezachová, když se připojení znovu připojí.</span><span class="sxs-lookup"><span data-stu-id="3310f-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="3310f-125">Připojení se musí znovu připojit ke skupině, když je znovu navázána.</span><span class="sxs-lookup"><span data-stu-id="3310f-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="3310f-126">Není možné spočítat členy skupiny, protože tyto informace nejsou k dispozici, pokud je aplikace škálovaná na více serverů.</span><span class="sxs-lookup"><span data-stu-id="3310f-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="3310f-127">K ochraně přístupu k prostředkům při používání skupin použijte funkci [ověřování a autorizace](xref:signalr/authn-and-authz) v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3310f-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="3310f-128">Pokud přidáte uživatele do skupiny pouze v případě, že jsou pověření platná pro tuto skupinu, zprávy odeslané do této skupiny budou přecházet pouze autorizovaným uživatelům.</span><span class="sxs-lookup"><span data-stu-id="3310f-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="3310f-129">Skupiny ale nejsou funkcí zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="3310f-129">However, groups are not a security feature.</span></span> <span data-ttu-id="3310f-130">Deklarace identity ověřování mají funkce, které skupiny nemají, například vypršení platnosti a odvolání.</span><span class="sxs-lookup"><span data-stu-id="3310f-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="3310f-131">Pokud je oprávnění uživatele k přístupu ke skupině odvoláno, musíte je ručně zjistit a odebrat ze skupiny.</span><span class="sxs-lookup"><span data-stu-id="3310f-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="3310f-132">V názvech skupin se rozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="3310f-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="3310f-133">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="3310f-133">Related resources</span></span>

* [<span data-ttu-id="3310f-134">Začínáme</span><span class="sxs-lookup"><span data-stu-id="3310f-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3310f-135">Centra</span><span class="sxs-lookup"><span data-stu-id="3310f-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="3310f-136">Publikování aplikací do Azure</span><span class="sxs-lookup"><span data-stu-id="3310f-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
